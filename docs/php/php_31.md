# 第 3 节 项目实战 2（构建项目框架）

## 一、构建项目框架

> **ps：我们在创建文件夹的时候，可能遇到权限不足的情况，可以选择使用 `sudo` 创建，或者直接改变父文件夹的权限 `sudo chmod -R 777 *`**

##### 1)、在`/var/www/html/`下，创建一个文件夹`webbookmarker`

```php
mkdir webbookmarker 
```

##### 2)、将之前下载的 ThinkPHP 解压后的文件夹内的内容复制到刚刚创建的 `webbookmarker` 下

##### 3)、打开浏览器，访问 `localhost/webbookmarker/`，发现我们不能访问，

##### 提示信息的意思是，权限不够，OK，那我们改变 `webbookmarker` 的父文件夹的权限

```php
sudo chmod -R 777 * 
```

##### 再次打开浏览器，访问 `localhost/webbookmarker/ThinkPHP.php`，此时浏览器显示 `欢迎使用 ThinkPHP！`，说明我们的 ThinkPHP 安装成功了。

##### 4)、将之前下载的 HTML 解析器移动到 `/var/www/html/webbookmarker/Application/Home/Controller/` 下，如果遇到权限问题，记得自己处理。

##### 5)、在 `/var/www/html/webbookmarker/Application/Home/Controller/` 的文件夹中，创建`BookMarkerController.class.php`。

##### 6)、在 `/var/www/html/webbookmarker/Application/Home/Model/` 的文件夹中，创建`BookMarkerModel.class.php`。

##### 7)、在 `/var/www/html/webookmarker/` 创建 `client` 文件夹，该文件夹下保存的是客户端的 html、css、js 等文件

##### 8)、将下载好的 AmazeUI 压缩包解压，同时将解压后的文件夹内的 `assets` 文件夹复制到刚刚创建的 `client` 文件下

##### 9)、在 `client` 文件夹下创建 `index.html`

## 二、项目框架的结构

```php
---webbookmarker
|---Application
 |---Common
  |---省略
 |---Home
  |---Common
  |---Conf
  |---Controller
   |---BookMarkerController.class.php
   |---simple_html_dom.php
  |---Model
   |---BookMarkerModel.class.php
  |---View
  |---index.html
 |---Runtime
 |---index.html
 |---README.md
|---Public
|---client
  |---assets
   |---省略
 |---index.html
|---ThinkPHP
|---composer.json
|---index.php
|---README.md 
```

按照这个参考构建项目目录。其实，有些是默认的 ThinkPHP 结构的，已经被省略了。