---
layout: post
title: ThinkPHP创建定时任务
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

#### ThinkPHP创建定时任务

1. 使用命令创建定时任务(或在app\command目录下创建)
	```
	// 生成用户等级检测命令类（对应文件：app/command/CheckUserLevel.php）
	php think make:command CheckUserLevel

	// 扩展
	// 指定命令命名空间（多应用模式下使用，如 admin 应用）
	php think make:command admin/CheckUserLevel

	// 生成命令时同时指定控制台命令名（如 user:check-level）
	php think make:command CheckUserLevel --name=user:check-level
	```
2. 配置定时任务
   - 打开 `config/console.php` 文件
   - 在 `commands` 数组中添加定时任务配置
   - 例如：`'user:check-level' => app\command\CheckUserLevel::class,`
3. 编写定时任务逻辑
   - 打开 `app/command/CheckUserLevel.php` 文件
   - 实现 `handle` 方法，编写定时任务的具体逻辑
   - 例如：检查用户等级是否过期，过期则更新等级
```
<?php
		declare(strict_types=1);

		namespace app\command;

		use think\console\Command;
		use think\console\Input;
		use think\console\Output;
		use think\facade\Db;
		use think\facade\Log;

		class CheckUserLevel extends Command
		{
			protected function configure()
			{
				$this->setName('user:check-level')
				->setDescription('批量检测用户等级，满足条件自动升级（无队列版）');
			}

			protected function execute(Input $input, Output $output)
			{
				$output->writeln('开始批量检测用户等级：' . date('Y-m-d H:i:s'));

				// 升级规则（配置化，可移到config/user.php）

				// 分页处理，避免一次性加载过多数据
				$page = 1;
				$pageSize = 200; // 单次处理200条，根据服务器性能调整
				$totalProcess = 0;
				$totalSuccess = 0;

				while (true) {
					// 查询未达最高等级的用户（仅查需要检测的字段，减少内存占用）
					$users = Db::name('user_info')
						->where('level', 1)
						->field('id, name,tel,level')
						->page($page, $pageSize)
						->select();

					if (empty($users))
						break;

					foreach ($users as $user) {
						Db::startTrans();
						try {
							// 锁定行，防止并发升级
							Db::name('user_info')->where('id', $user['id'])->lock(true)->find();

							// 获取当前等级的升级规则
							// 1. 推荐用户数 >= 100 
							$sumReferrer = Db::name('user_info')
								->where('from_id', $user['id'])
								->where('state', 1)
								->count();
							// 2. 推荐报费 >= 10000
							$fromIds = Db::name('user_info')
								->where('from_id', $user['id'])
								->where('state', 1)
								->column('id');
							$sumReferrerConsume = Db::name('order_info')
								->whereIn('user_id', $fromIds)
								->where('state', 1)
								->sum('premium');
							if ($sumReferrer < 100 || $sumReferrerConsume < 10000) {
								Db::commit();
								continue; // 不满足条件，跳过
							}
							// 执行升级
							$oldLevel = $user['level'];
							Db::name('user_info')->where('id', $user['id'])->update([
								'level' => 2,
								'level_up_time' => date('Y-m-d H:i:s')
							]);
							Db::commit();
							$totalSuccess++;
							$output->writeln("用户 {$user['name']}（ID:{$user['id']}）升级成功：{$oldLevel}→2");
							Log::info("用户 {$user['id']} 批量升级成功：{$oldLevel}→2");
						} catch (\Exception $e) {
							Db::rollback();
							$output->error("用户 {$user['id']} 升级失败：{$e->getMessage()}");
							Log::error("用户 {$user['id']} 升级失败：{$e->getMessage()}");
						}
					}
					$totalProcess += count($users);
					$page++;
				}
				$output->writeln("批量检测完成！共处理 {$totalProcess} 个用户，成功升级 {$totalSuccess} 个");
				Log::info("用户等级批量升级完成：处理{$totalProcess}人，成功{$totalSuccess}人");
			}
		}
```