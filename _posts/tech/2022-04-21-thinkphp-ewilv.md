---
layout: post
title: ThinkPHP获取最新版本
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

```
<?php

namespace app\api\controller;

use app\common\controller\Api;

/**
 * @title Version 版本接口
 * @description 接口说明
 */
class Version extends Api
{
    public function _initialize()
    {
        parent::_initialize();
    }

    /**
     * @title 获取新版本
     * @description 接口说明
     * @param name:version type:string require:1 desc:版本号
     * @param name:type type:int require:1 desc:类型0用户端1商家端2代理商端
     * @param name:system type:string require:1 desc:系统android/ios
     * @return is_upgrade:是否新版本
     * @return upgrade_url:更新链接
     * @return upgrade_version:更新版本号
     * @return desc:更新的描述
     * @author 开发者
     * @url /api/Version/getVersion
     * @method get
     *
     */
    public function getVersion()
    {
        $version = $this->request->get('version');
        $type = $this->request->get('type', 1);
        $system = $this->request->get('system', 'android');
        $VersionModel = new \app\common\model\Version();
        if (empty($version)) {
            $this->error('请传入版本号');
        }
        $message = '暂无更新';
        $upgrade_version = '';
        $upgrade_url = '';
        $is_upgrade = 0;
        $desc = '';
        $version_list = $VersionModel->where('type', $type)->column('version');
        // echo $VersionModel->getLastSql();exit;
        $version_sort = self::sortVersion($version_list, false);
        $version_sort = self::comparisonVersion($version, $version_sort);
        if (!empty($version_sort)) {
            $versionInfo = $VersionModel->where(['type' => $type, 'version' => $version_sort[0]])->find();
            switch ($type) {
                case 1:
                    // 用户端
                    if (!empty($system) && $system == 'android') {
                        $upgrade_url = $versionInfo['upgrade_url'];
                        $upgrade_version = $versionInfo['version'];
                        $desc = $versionInfo['desc'];
                        if ($version != $versionInfo['version']) {
                            $message = '用户端有更新';
                            $is_upgrade = 1;
                        }
                    } else {
                        // 1197227551 众驾租车
                        $result = self::get_curl('https://itunes.apple.com/cn/lookup', ['id' => '1197227551']);
                        $result_array = json_decode(trim($result), true);
                        if (!empty($result_array) && !empty($result_array['results'][0]['version'])
                            && $result_array['results'][0]['version'] != $version
                            && $result_array['results'][0]['version'] >= $version) {
                            $message = '用户端有更新';
                            $upgrade_url = $result_array['results'][0]['trackViewUrl'];
                            $upgrade_version = $result_array['results'][0]['version'];
                            $is_upgrade = 1;
                            $desc = $result_array['results'][0]['releaseNotes'];
                        }
                    }
                    break;
                case 2:
                    // 商家端
                    if (!empty($system) && $system == 'android') {
                        $upgrade_url = $versionInfo['upgrade_url'];
                        $upgrade_version = $versionInfo['version'];
                        $desc = $versionInfo['desc'];
                        if ($version != $versionInfo['version']) {
                            $message = '商家端有更新';
                            $is_upgrade = 1;
                        }
                    } else {
                        // 1197227551 众驾租车
                        $result = self::get_curl('https://itunes.apple.com/cn/lookup', ['id' => '1197227551']);
                        $result_array = json_decode(trim($result), true);
                        if (!empty($result_array) && !empty($result_array['results'][0]['version'])
                            && $result_array['results'][0]['version'] != $version
                            && $result_array['results'][0]['version'] >= $version) {
                            $message = '商家端有更新';
                            $upgrade_url = $result_array['results'][0]['trackViewUrl'];
                            $upgrade_version = $result_array['results'][0]['version'];
                            $is_upgrade = 1;
                            $desc = $result_array['results'][0]['releaseNotes'];
                        }
                    }
                    break;
                default:
                    break;
            }
        }
        $data = [
            'is_upgrade' => $is_upgrade, 
            'upgrade_url' => $upgrade_url, 
            'upgrade_version' => $upgrade_version, 
            'desc' => $desc
        ];
        // write_log($data);
        $this->success($message, $data);
    }

    public function get_curl($url = '', $data = [])
    {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
        curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);// 跳过证书检查
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);// 从证书中检查SSL加密算法是否存在
        curl_setopt($ch, CURLOPT_USERAGENT, 'Mozilla/5.0 (compatible; MSIE 5.01; Windows NT 5.0)');
        curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);
        curl_setopt($ch, CURLOPT_AUTOREFERER, 1);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        $result = curl_exec($ch);
        if (curl_errno($ch)) {
            write_log(' 提交参数信息 ch:' . json_encode(curl_error($ch)));
            return curl_error($ch);
        }
        curl_close($ch);
        return $result;
    }

    /**
     * 比对版本号
     * @param string $version
     * @param array $versions
     * @return mixed
     */
    function comparisonVersion($version = '', $versions = [])
    {
        foreach ($versions as $key => $value) {
            $result = version_compare($version, $value);
            if ($result == '-1') {// 当前版本比系统版本低
                return $value;
            } else if ($result == '0') {// 当前版本与系统版本一致
                continue;
            } else if ($result == '1') {// 当前版本比系统版本高
                continue;
            }
        }
    }

    /**
     * 排序版本号
     * @param $versions
     * @param bool $sort_rule
     * @return mixed
     */
    function sortVersion($versions, $sort_rule = true)
    {
        foreach ($versions as $key => $value) {
            $firstArr = explode('.', $value);
            $firstArrCount = count($firstArr);
            for ($i = 0; $i < $firstArrCount; $i++) {
                $firstArr[$i] = str_pad($firstArr[$i], 2, 0, STR_PAD_LEFT);
            }
            $versions[$key] = implode('.', $firstArr);
        }
        if ($sort_rule) {
            sort($versions);
        } else {
            rsort($versions);
        }
        foreach ($versions as $key => $value) {
            $firstArr = explode('.', $value);
            $firstArrCount = count($firstArr);
            for ($i = 0; $i < $firstArrCount; $i++) {
                $firstArr[$i] = intval($firstArr[$i]);
            }
            $versions[$key] = implode('.', $firstArr);
        }
        return $versions;
    }
}

```

参考链接：[https://www.bbsmax.com/A/l1dyEoqdem](https://www.bbsmax.com/A/l1dyEoqdem)