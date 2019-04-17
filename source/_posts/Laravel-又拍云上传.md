---
title: Laravel 又拍云上传
date: 2019-04-18 04:20:15
tags:
---

首先我使用 Google 搜索 `Laravel upyun php-sdk` 这些关键词
![file](https://iocaffcdn.phphub.org/uploads/images/201810/26/20500/cFHT8AOOxg.png?imageView2/2/w/1240/h/0)
首先搜索到的是 [JellyBool](https://github.com/JellyBool) 的 [Laravel 扩展包](https://github.com/JellyBool/flysystem-upyun)，这个扩展包把又拍云的 [php-sdk](https://github.com/upyun/php-sdk) 集成到 Laravel 项目会比较方便。

```bash
composer require "jellybool/flysystem-upyun"
```

安装完成之后



在 `config/app.php` 里添加 `UpyunServiceProvider`:

```php
'providers' => [
    // Other service providers...
    JellyBool\Flysystem\Upyun\UpyunServiceProvider::class,
],
```
在 `config/filesystems.php` 的 `disks` 中添加下面的配置：

```php
return [
    //...
      'upyun' => [
                'driver'        => 'upyun', 
                'bucket'        => env('UPYUN_BUCKET',  '服务名称')
                'operator'      => env('UPYUN_OPERATOR', '操作员的名字'),
                'password'      => env('UPYUN_PASSWORD', '操作员的密码'),
                'domain'        => env('UPYUN_DOMAIN', '服务分配的域名'),
                'protocol'     => 'http', // 服务使用的协议，如需使用 http，在此配置 http
            ],
    //...
];
```

然后在 `.env` 文件配置, 并且拷贝到 `.env.example` 文件里
```
UPYUN_BUCKET=
UPYUN_OPERATOR=
UPYUN_PASSWORD=
UPYUN_DOMAIN=
```

![file](https://iocaffcdn.phphub.org/uploads/images/201810/26/20500/9Elc4ZNMzF.png?imageView2/2/w/1240/h/0)

域名可绑定自己的线上项目域名，然后在 `内容管理` 里面添加管理员和密码（如果管理员和密码不匹配就会报）
```
401 Unauthorized 错误
```
## 快速测试
### 如果你想快速测试自己的配置信息是否正确又或者服务是否可行

在 `resources/views/pages` 下，新建文件视图 `image.blade.php`。
```php
@extends('layouts.app')
@section('title', '又拍云图片上传测试')

@section('content')
    <div class="container">
        <h1 class="text-center">图片上传</h1>
        <div class="content">
            <form enctype="multipart/form-data" method="post" action="/image">
                {{ csrf_field() }}
                <div class="form-group">
                    <input type="file" name="image" value="选择文件">
                </div>
                <div class="form-group">
                    <input type="submit" class="form-control" value="上传图片">
                </div>
            </form>
        </div>
    </div>
@stop
```
然后在 `routes/web.php` 文件里添加以下代码：
```php
<?php
use Illuminate\Http\Request;
.
.
.
// 图片上传测试
Route::get('/image', function () {
    return view('pages.image');
});

Route::post('/image', function (Request $request) {
    $domain = "http://" . config('filesystems.disks.upyun.domain');
    $file_path = Storage::disk('upyun')->put('/image', $request->file('image'));
    return $domain . "/$file_path";
});
```
完成之后访问 `http://项目名称.test/image` 链接，在视图里面测试图片上传，如果上传成功会返回完整的链接。

复制链接，在新的窗口打开。

```
如果图片没有显示，可能要等 3 - 10 分钟才神奇的显示。

如果有谁知道是什么原因，麻烦告诉我一下。
```

可在 Laravel 文档的 [文件存储](https://learnku.com/docs/laravel/5.5/filesystem/1319) 中查看更多的可用方法。

CNAME 可绑定你线上的二级域名: upload.线上域名 [解析记录冲突判断规则](https://www.alibabacloud.com/help/zh/doc-detail/58456.htm)