# 第 4 节 三、项目实战 3（服务器端功能的实现）

## 一、服务器端功能的实现

### 1\. 我们需要实现哪些功能？

*   根据用户提供 url，去获取改 url 所对应网页的 title 和 icon 信息。
*   将 url，title，icon 添加到数据库中
*   获取数据库中的所有数据
*   修改书签
*   删除书签
*   模糊查询书签

### 2\. 代码书写

**打开 `webbookmarker/Application/Home/Conf/config.php`，配置数据库信息：**

```php
<?php
return array(
    'DB_TYPE' => 'mysql',
    'DB_HOST' => 'localhost',
    'DB_NAME' => 'webbookmarker',
    'DB_USER' => 'root',
    'DB_PWD' => '',
    'DB_PORT' => 3306,
    'DB_PREFIX' => 'web_'
); 
```

**打开 `webbookmarker/Application /Home/Controller/BookMarkerController.class.php`，编写实现服务器功能的代码。**

** 首先编写 `BookMarker` 控制器的命名空间，并引入 HTML 解析器：**

```php
<?php
namespace Home\Controller;
use Think\Controller;
require_once 'simple_html_dom.php';
class BookMarkerController extends Controller {
    /**
     * 默认控制器
     */
    public function index(){
        echo "BookMarker";
    }
} 
```

** 创建并实现私有成员变量 `$msgTpl` 和私有方法 `CreateMsg()`：**

```php
 /**
     * 消息模板
     * @var string
     * @author lishengcn.cn
     */
    private $msgTpl = '{"code": %s, "content": %s}';

    /**
     * 生成消息模板
     * @param integer $code
     * @param string $content
     * @return string 
     * @author lishengcn.cn
     */
    private function CreateMsg($code, $content) {
        $result = sprintf($this->msgTpl, $code, $content);
        return $result;
    } 
```

** 创建并实现私有方法 `FormatIcon()`，该方法用来当获取不到 `icon` 时和获取到的 `icon 的 url` 为相对地址的时候，对其进行处理：**

```php
 /**
     * 格式化 icon 的 url
     * @param string $href
     * @param string $iconUrl
     * @return string
     * @author lishengcn.cn
     */
    private function FormatIcon($href, $iconUrl){
        if(!strstr($iconUrl, "http") && $iconUrl != null) {
            return $href . $iconUrl;
        } elseif ($iconUrl == null) {
            $iconUrl = "https://dn-anything-about-doc.qbox.me/md04176000.png";
            return $iconUrl;
        } else {
            return $iconUrl;
        }
    } 
```

** 创建并实现私有方法 `GetMarker()`，改方法根据前端传过来的 `url` 参数，去获取 `title` 和 `icon`，最后将 `url`，`title`，`icon` 作为一个数据返回：**

```php
 /**
     * 获取网址的 title 和 icon
     * @param string $url
     * @return array 
     * @author lishengcn.cn
     */
    private function GetMarker($title, $url) {
        if (!$html = file_get_html($url)) {
            return false;
        } else {
            if ($title == ''){
                $data['title'] = $html->find('title', 0)->plaintext;
            } else {
                $data['title'] = $title;
            }
            $data['href'] = $url;
            if ($icon = $html->find('link[rel=shortcut icon]', 0)->href) {
                $data['icon'] = $icon;
            } elseif ($icon = $html->find('link[rel=shortcut icon]', 0)->href) {
                $data['icon'] = $icon;
            }
            return $data;
        }
    } 
```

** 添加书签接口，添加成功，向客户端，也就是浏览器，返回 `json` 格式的数据：**

```php
 /**
     * 添加书签
     * @param string $url
     * @author lishengcn.cn
     */
    public function Add($title, $url) {
        if ($data = $this->GetMarker($title, $url)) {
            $data['icon'] = $this->FormatIcon($data['href'], $data['icon']);
            $marker = D('Marker');
            $data['id'] = null;
            $marker->create($data);
            $marker->add($data);
            $data = json_encode($data);
            echo $this->CreateMsg(1, $data);
        } else {
            echo $this->CreateMsg(0, '"获取 url 失败"');
        }
    } 
```

** 删除书签接口:**

```php
 /**
     * 删除书签
     * @param integer $id
     */
    public function Del($id) {
        $marker = D('Marker');
        $condition['id'] = $id;
        if ($marker->where($condition)->delete() != 0) {
            echo $this->CreateMsg(1, '"删除书签成功"');
        } else {
            echo $this->CreateMsg(0, '"删除书签失败"');
        }
    } 
```

** 模糊查询书签接口： **

```php
 /**
     * 模糊查询书签
     * @param string $keyworld
     */
    public function Search($keyworld) {
        $marker = D('Marker');
        $condition['title'] = array('like', "%{$keyworld}%");
        if ($data = $marker->where($condition)->select()) {
            $data = json_encode($data);
            echo $this->CreateMsg(1, $data);
        } else {
            echo $this->CreateMsg(0, '"没有找到相关信息"');
        }
    } 
```

** 修改书签接口：**

```php
 /**
     * 修改书签
     * @param integer $id
     * @param string $title
     * @param string $href
     */
    public function Alter($id, $title, $href) {
        $marker = D('Marker');
        $data['id'] = $id;
        $data['title'] = $title;
        $data['href'] = $href;
        print_r($data);
        $marker->create($data);
        $marker->save($data);      
    } 
```

**加载全部书签信息接口：**

```php
public function ShowAll() {
        $marker = D('Marker');
        echo json_encode($marker->select());
    } 
```

**打开 `webbookmarker/Application/Home/Model/BookMarkerModel.class.php`，编辑代码：**

```php
<?php
/**
*书签模型
*/
namespace Home\Model;
use Think\Model;
class BookMarkerModel extends Model {
} 
```

### 3\. 接口的访问方式为 `GET`，然后带上相关的参数就行，具体访问方式在后面的 `ajax` 再详细说明

```php
 http://localhost/webbookmarker/index.php/Home/BookMarker/ShowAll
    http://localhost/webbookmarker/index.php/Home/BookMarker/Add
    http://localhost/webbookmarker/index.php/Home/BookMarker/Search
    http://localhost/webbookmarker/index.php/Home/BookMarker/Del
    http://localhost/webbookmarker/index.php/Home/BookMarker/Alter 
```

**附件：整个 `BookMarkerController.class.php` 的代码部分**

```php
<?php
namespace Home\Controller;
use Think\Controller;
require_once 'simple_html_dom.php';

class BookMarkerController extends Controller {
    /**
     * 默认控制器
     */
    public function index(){
        echo "BookMarker";
    }

    /**
     * 消息模板
     * @var string
     * @author lishengcn.cn
     */
    private $msgTpl = '{"code": %s, "content": %s}';

    /**
     * 生成消息模板
     * @param integer $code
     * @param string $content
     * @return string 
     * @author lishengcn.cn
     */
    private function CreateMsg($code, $content) {
        $result = sprintf($this->msgTpl, $code, $content);
        return $result;
    }

    /**
     * 格式化 icon 的 url
     * @param string $href
     * @param string $iconUrl
     * @return string
     * @author lishengcn.cn
     */
    private function FormatIcon($href, $iconUrl){
        if(!strstr($iconUrl, "http") && $iconUrl != null) {
            return $href . $iconUrl;
        } elseif ($iconUrl == null) {
            $iconUrl = "https://dn-anything-about-doc.qbox.me/md04176000.png";
            return $iconUrl;
        } else {
            return $iconUrl;
        }
    }

    /**
     * 获取网址的 title 和 icon
     * @param string $url
     * @return array 
     * @author lishengcn.cn
     */
    private function GetMarker($title, $url) {
        if (!$html = file_get_html($url)) {
            return false;
        } else {
            if ($title == ''){
                $data['title'] = $html->find('title', 0)->plaintext;
            } else {
                $data['title'] = $title;
            }
            $data['href'] = $url;
            if ($icon = $html->find('link[rel=shortcut icon]', 0)->href) {
                $data['icon'] = $icon;
            } elseif ($icon = $html->find('link[rel=shortcut icon]', 0)->href) {
                $data['icon'] = $icon;
            }
            return $data;
        }
    }

    /**
     * 添加书签
     * @param string $url
     * @author lishengcn.cn
     */
    public function Add($title, $url) {
        if ($data = $this->GetMarker($title, $url)) {
            $data['icon'] = $this->FormatIcon($data['href'], $data['icon']);
            $marker = D('Marker');
            $data['id'] = null;
            $marker->create($data);
            $marker->add($data);
            $data = json_encode($data);
            echo $this->CreateMsg(1, $data);
        } else {
            echo $this->CreateMsg(0, '"获取 url 失败"');
        }
    }

    /**
     * 删除书签
     * @param integer $id
     */
    public function Del($id) {
        $marker = D('Marker');
        $condition['id'] = $id;
        if ($marker->where($condition)->delete() != 0) {
            echo $this->CreateMsg(1, '"删除书签成功"');
        } else {
            echo $this->CreateMsg(0, '"删除书签失败"');
        }
    }

    /**
     * 模糊查询书签
     * @param string $keyworld
     */
    public function Search($keyworld) {
        $marker = D('Marker');
        $condition['title'] = array('like', "%{$keyworld}%");
        if ($data = $marker->where($condition)->select()) {
            $data = json_encode($data);
            echo $this->CreateMsg(1, $data);
        } else {
            echo $this->CreateMsg(0, '"没有找到相关信息"');
        }
    }

    /**
     * 修改书签
     * @param integer $id
     * @param string $title
     * @param string $href
     */
    public function Alter($id, $title, $href) {
        $marker = D('Marker');
        $data['id'] = $id;
        $data['title'] = $title;
        $data['href'] = $href;
        print_r($data);
        $marker->create($data);
        $marker->save($data);      
    }

    public function ShowAll() {
        $marker = D('Marker');
        echo json_encode($marker->select());
    }
} 
```