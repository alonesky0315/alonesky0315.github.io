---
layout: post
title: Dcat Admin  提示语言包
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

#### Dcat Admin  提示语言包
```
// resources\lang\zh_CN\validation.php
<?php
return [
    /*
    |--------------------------------------------------------------------------
    | 验证语言行
    |--------------------------------------------------------------------------
    |
    | 以下语言行包含了验证器类使用的默认错误消息。
    | 有些规则包含多个版本，例如 size 规则可以验证字符串、文件、数组和数值的大小。
    | 您可以在这里自由修改这些语言行，以适应您的应用程序需求。
    |
    */

    'accepted'             => ':attribute 必须接受。',
    'accepted_if'          => '当 :other 为 :value 时，:attribute 必须接受。',
    'active_url'           => ':attribute 不是有效的URL。',
    'after'                => ':attribute 必须是 :date 之后的日期。',
    'after_or_equal'       => ':attribute 必须是 :date 之后或相同的日期。',
    'alpha'                => ':attribute 只能包含字母。',
    'alpha_dash'           => ':attribute 只能包含字母、数字、短横线和下划线。',
    'alpha_num'            => ':attribute 只能包含字母和数字。',
    'array'                => ':attribute 必须是一个数组。',
    'before'               => ':attribute 必须是 :date 之前的日期。',
    'before_or_equal'      => ':attribute 必须是 :date 之前或相同的日期。',
    'between'              => [
        'numeric' => ':attribute 必须在 :min 到 :max 之间。',
        'file'    => ':attribute 必须在 :min 到 :max KB之间。',
        'string'  => ':attribute 必须在 :min 到 :max 个字符之间。',
        'array'   => ':attribute 必须在 :min 到 :max 项之间。',
    ],
    'boolean'              => ':attribute 字段必须为 true 或 false。',
    'confirmed'            => ':attribute 二次确认不匹配。',
    'current_password'     => '密码不正确。',
    'date'                 => ':attribute 不是有效的日期。',
    'date_equals'          => ':attribute 必须是等于 :date 的日期。',
    'date_format'          => ':attribute 与给定的格式 :format 不匹配。',
    'declined'             => ':attribute 必须拒绝。',
    'declined_if'          => '当 :other 为 :value 时，:attribute 必须拒绝。',
    'different'            => ':attribute 和 :other 必须不同。',
    'digits'               => ':attribute 必须是 :digits 位数字。',
    'digits_between'       => ':attribute 必须在 :min 到 :max 位数字之间。',
    'dimensions'           => ':attribute 图片尺寸无效。',
    'distinct'             => ':attribute 字段有重复值。',
    'email'                => ':attribute 必须是有效的电子邮件地址。',
    'ends_with'            => ':attribute 必须以以下之一结尾：:values。',
    'enum'                 => '所选的 :attribute 无效。',
    'exists'               => '所选的 :attribute 无效。',
    'file'                 => ':attribute 必须是文件。',
    'filled'               => ':attribute 字段必须有值。',
    'gt'                   => [
        'numeric' => ':attribute 必须大于 :value。',
        'file'    => ':attribute 必须大于 :value KB。',
        'string'  => ':attribute 必须大于 :value 个字符。',
        'array'   => ':attribute 必须包含多于 :value 项。',
    ],
    'gte'                  => [
        'numeric' => ':attribute 必须大于或等于 :value。',
        'file'    => ':attribute 必须大于或等于 :value KB。',
        'string'  => ':attribute 必须大于或等于 :value 个字符。',
        'array'   => ':attribute 必须包含 :value 项或更多。',
    ],
    'image'                => ':attribute 必须是 jpeg, png, bmp 或者 gif 格式的图片。',
    'in'                   => '所选的 :attribute 无效。',
    'in_array'             => ':attribute 字段不存在于 :other 中。',
    'integer'              => ':attribute 必须是整数。',
    'ip'                   => ':attribute 必须是有效的IP地址。',
    'ipv4'                 => ':attribute 必须是有效的IPv4地址。',
    'ipv6'                 => ':attribute 必须是有效的IPv6地址。',
    'json'                 => ':attribute 必须是有效的JSON字符串。',
    'lt'                   => [
        'numeric' => ':attribute 必须小于 :value。',
        'file'    => ':attribute 必须小于 :value KB。',
        'string'  => ':attribute 必须小于 :value 个字符。',
        'array'   => ':attribute 必须包含少于 :value 项。',
    ],
    'lte'                  => [
        'numeric' => ':attribute 必须小于或等于 :value。',
        'file'    => ':attribute 必须小于或等于 :value KB。',
        'string'  => ':attribute 必须小于或等于 :value 个字符。',
        'array'   => ':attribute 不得包含多于 :value 项。',
    ],
    'mac_address'          => ':attribute 必须是有效的MAC地址。',
    'max'                  => [
        'numeric' => ':attribute 的最大长度为 :max 位。',
        'file'    => ':attribute 不能大于 :max KB。',
        'string'  => ':attribute 不能超过 :max 个字符。',
        'array'   => ':attribute 不能包含超过 :max 项。',
    ],
    'mimes'                => ':attribute 必须是 :values 类型的文件。',
    'mimetypes'            => ':attribute 必须是 :values 类型的文件。',
    'min'                  => [
        'numeric' => ':attribute 必须至少为 :min。',
        'file'    => ':attribute 必须至少为 :min KB。',
        'string'  => ':attribute 必须至少有 :min 个字符。',
        'array'   => ':attribute 必须至少包含 :min 项。',
    ],
    'multiple_of'          => ':attribute 必须是 :value 的倍数。',
    'not_in'               => '所选的 :attribute 无效。',
    'not_regex'            => ':attribute 格式无效。',
    'numeric'              => ':attribute 必须是数字。',
    'password'             => '密码不正确。',
    'present'              => ':attribute 字段必须存在。',
    'prohibited'           => ':attribute 字段被禁止。',
    'prohibited_if'        => '当 :other 为 :value 时，:attribute 字段被禁止。',
    'prohibited_unless'    => '除非 :other 在 :values 中，否则 :attribute 字段被禁止。',
    'prohibits'            => ':attribute 字段禁止 :other 存在。',
    'regex'                => ':attribute 格式无效。',
    'required'             => ':attribute 字段是必需的。',
    'required_array_keys'  => ':attribute 字段必须包含以下键：:values。',
    'required_if'          => '当 :other 为 :value 时，:attribute 字段是必需的。',
    'required_unless'      => '除非 :other 在 :values 中，否则 :attribute 字段是必需的。',
    'required_with'        => '当 :values 存在时，:attribute 字段是必需的。',
    'required_with_all'    => '当 :values 都存在时，:attribute 字段是必需的。',
    'required_without'     => '当 :values 不存在时，:attribute 字段是必需的。',
    'required_without_all' => '当 :values 都不存在时，:attribute 字段是必需的。',
    'same'                 => ':attribute 和 :other 必须匹配。',
    'size'                 => [
        'numeric' => ':attribute 必须是 :size。',
        'file'    => ':attribute 必须是 :size KB。',
        'string'  => ':attribute 必须是 :size 个字符。',
        'array'   => ':attribute 必须包含 :size 项。',
    ],
    'starts_with'          => ':attribute 必须以以下之一开头：:values。',
    'string'               => ':attribute 必须是字符串。',
    'timezone'             => ':attribute 必须是有效的时区。',
    'unique'               => ':attribute 已被占用。',
    'uploaded'             => ':attribute 上传失败。',
    'url'                  => ':attribute 格式无效。',
    'uuid'                 => ':attribute 必须是有效的UUID。',

    /*
    |--------------------------------------------------------------------------
    | 自定义验证语言行
    |--------------------------------------------------------------------------
    |
    | 这里您可以为属性指定自定义验证消息。
    | 例如，下面的行用于为"email"属性的"required"规则指定自定义消息。
    |
    | 'custom' => [
    |     'email' => [
    |         'required' => '请输入您的邮箱地址',
    |     ],
    | ],
    |
    */

    'custom' => [
        'attribute-name' => [
            'rule-name' => 'custom-message',
        ],
    ],

    /*
    |--------------------------------------------------------------------------
    | 自定义属性名称
    |--------------------------------------------------------------------------
    |
    | 以下语言行用于替换我们的属性占位符
    | 例如，":attribute" 可以被替换为更友好的名称，如 "电子邮箱"。
    | 这有助于我们在消息中生成更具表现力的错误消息。
    |
    */

    'attributes' => [
        'name'         => '名字',
        'username'     => '用户名',
        'email'        => '邮箱',
        'password'     => '密码',
        'old_password' => '旧密码',
        'image'        => '图片',
        'password_confirmation' => '密码确认',
        'title'        => '标题',
        'subtitle'     => '副标题',
        'content'      => '内容',
        'published_at' => '发布时间',
        'tag'          => '标签',
        'meta_description' => '主要描述',
        'link'         => '链接',
        'tags'         => '标签',
    ],
];
```