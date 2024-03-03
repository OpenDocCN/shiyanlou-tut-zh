# 第 5 节 项目实战 4（前端非交互功能部分的实现）

## 一、前端代码非交互部分的实现（HTML 的部分）

##### ** 1、从下图，我们发现，我们的需要 5 个按钮分别是添加、查询、返回、修改、删除，然后是中间一大块的书签列表， ：**

![图片描述信息](img/2a6dc1a1cd77bbf88034aed66470c9cf.jpg)

还需要 4 个模拟框用来让用户填写相关的数据，这３个模拟框分别对应添加、查询、修改。

ok，下面开始书签代码

##### ** 2、代码书写**

打开 `webbookmarker/client/index.html`，编辑代码，完成头部属性的设置，和一些 `css` 的书写：

```php
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>云书签</title>
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="renderer" content="webkit">
    <meta http-equiv="Cache-Control" content="no-siteapp"/>
    <link rel="stylesheet" href="assets/css/amazeui.flat.css">
    <style>
    .marker_box {
        padding-left: 50px;
        padding-right: 50px;
    }
    .confirm-list i{
        position: absolute;
        right: 10px;
        top: 15px;
        color: #888;
        width: 60px;
        text-align: center;
        cursor: pointer;
    }
    .del {
        margin-right: 10px;
        color: gray
    }
    .modify {
        margin-right: 10px;
        color: gray
    }
  </style>
</head> 
```

编辑 `body` 中的代码，`header` 中包含一个标题和 3 个按钮：

```php
 <header class="am-topbar am-topbar-fixed-top">
        <div style="margin:0 50px 0 50px">
            <h1 class="am-topbar-brand">
                <a href="#"><i class="am-icon-bookmark"></i> 云书签</a>
            </h1>
            <div class="am-topbar-right">
                <button class="am-btn am-btn-secondary am-topbar-btn am-btn-sm" id="back">
                    <span class="am-icon-undo"></span>
                    返回
                </button>
            </div>
            <div class="am-topbar-right">
                <button class="am-btn am-btn-secondary am-topbar-btn am-btn-sm" id="search">
                    <span class="am-icon-search"></span>
                    查找
                </button>
            </div>
            <div class="am-topbar-right">
                <button class="am-btn am-btn-success am-topbar-btn am-btn-sm" id="add">
                    <span class="am-icon-plus"></span>
                    添加
                </button>
            </div>
        </div>

    </header> 
```

**书签列表容器：**

```php
 <div class="marker_box">
        <ul class="am-list confirm-list" id="marker_list">
        </ul>
    </div> 
```

**4 个模拟框：**

```php
 <!-- 添加模拟框开始 -->
    <div class="am-modal am-modal-prompt" tabindex="-1" id="add_prompt" >
        <div class="am-modal-dialog">
            <div class="am-modal-hd">添加新的书签</div><a class="am-badge am-badge-secondary am-round">地址栏为必填，若标题栏不填，由程序去获取网页的标题</a>
            <div class="am-modal-bd">
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" placeholder="标题：♥( ˘ ³˘)"></div>
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" placeholder="地址：( ˘ ³˘)♥"></div>

            </div>
            <div class="am-modal-footer">
                <span class="am-modal-btn" data-am-modal-cancel>取消</span>
                <span class="am-modal-btn" data-am-modal-confirm>提交</span>
            </div>
        </div>
    </div>
    <!-- 添加模拟框结束 --> 
```

```php
 <!-- 查找模拟框开始 -->
    <div class="am-modal am-modal-prompt" tabindex="-1" id="search_prompt">
        <div class="am-modal-dialog">
            <div class="am-modal-hd">查找书签</div><a class="am-badge am-badge-secondary am-round">支持模糊查询标题</a>
            <div class="am-modal-bd">
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" placeholder="标题：( ˘ ³˘)♥"></div>
            </div>
            <div class="am-modal-footer">
                <span class="am-modal-btn" data-am-modal-cancel>取消</span>
                <span class="am-modal-btn" data-am-modal-confirm>提交</span>
            </div>
        </div>
    </div>
    <!-- 查找模拟框结束 --> 
```

```php
 <!-- 删除模拟框开始 -->
    <div class="am-modal am-modal-confirm" tabindex="-1" id="del_confirm">
        <div class="am-modal-dialog">
            <div class="am-modal-bd">亲 ♥，确定要删除这条记录吗？</div>
            <div class="am-modal-footer">
                <span class="am-modal-btn" data-am-modal-cancel>取消</span>
                <span class="am-modal-btn" data-am-modal-confirm>确定</span>
            </div>
        </div>
    </div>
    <!-- 删除模拟框结束 --> 
```

```php
 <!-- 修改模拟框开始 -->
    <div class="am-modal am-modal-prompt" tabindex="-1" id="modify_prompt">
        <div class="am-modal-dialog">
            <div class="am-modal-hd">修改书签
            <div class="am-modal-bd">
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" id="m_title"></div>
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" id="m_href"></div>

                    <input type="hidden" class="am-modal-prompt-input" id="m_id"></div>

            </div>
            <div class="am-modal-footer">
                <span class="am-modal-btn" data-am-modal-cancel>取消</span>
                <span class="am-modal-btn" data-am-modal-confirm>提交</span>
            </div>
        </div>
    </div>
    <!-- 修改模拟框结束 --> 
```

现在我们从浏览器中输入 `localhost/webbookmarker/client`，就能开看到下图，不过现在我们的那些按钮还无法使用，按键的响应，我们下节课去实现。

`附件`：整个 `client` 下的 `index.html` 的代码部分：

```php
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>云书签</title>
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="renderer" content="webkit">
    <meta http-equiv="Cache-Control" content="no-siteapp"/>
    <link rel="stylesheet" href="assets/css/amazeui.flat.css">
    <style>
    .marker_box {
        padding-left: 50px;
        padding-right: 50px;
    }
    .confirm-list i{
        position: absolute;
        right: 10px;
        top: 15px;
        color: #888;
        width: 60px;
        text-align: center;
        cursor: pointer;

    }

    .del {
        margin-right: 10px;
        color: gray
    }

    .modify {
        margin-right: 10px;
        color: gray
    }
  </style>
</head>
<body>
    <header class="am-topbar am-topbar-fixed-top">
        <div style="margin:0 50px 0 50px">
            <h1 class="am-topbar-brand">
                <a href="#"><i class="am-icon-bookmark"></i> 云书签</a>
            </h1>
            <div class="am-topbar-right">
                <button class="am-btn am-btn-secondary am-topbar-btn am-btn-sm" id="back">
                    <span class="am-icon-undo"></span>
                    返回
                </button>
            </div>
            <div class="am-topbar-right">
                <button class="am-btn am-btn-secondary am-topbar-btn am-btn-sm" id="search">
                    <span class="am-icon-search"></span>
                    查找
                </button>
            </div>
            <div class="am-topbar-right">
                <button class="am-btn am-btn-success am-topbar-btn am-btn-sm" id="add">
                    <span class="am-icon-plus"></span>
                    添加
                </button>
            </div>
        </div>

    </header>
    <br>

    <div class="marker_box">
        <ul class="am-list confirm-list" id="marker_list">
        </ul>
    </div>

    <!-- 添加模拟框开始 -->
    <div class="am-modal am-modal-prompt" tabindex="-1" id="add_prompt" >
        <div class="am-modal-dialog">
            <div class="am-modal-hd">添加新的书签</div><a class="am-badge am-badge-secondary am-round">地址栏为必填，若标题栏不填，由程序去获取网页的标题</a>
            <div class="am-modal-bd">
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" placeholder="标题：♥( ˘ ³˘)"></div>
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" placeholder="地址：( ˘ ³˘)♥"></div>

            </div>
            <div class="am-modal-footer">
                <span class="am-modal-btn" data-am-modal-cancel>取消</span>
                <span class="am-modal-btn" data-am-modal-confirm>提交</span>
            </div>
        </div>
    </div>
    <!-- 添加模拟框结束 -->

    <!-- 查找模拟框开始 -->
    <div class="am-modal am-modal-prompt" tabindex="-1" id="search_prompt">
        <div class="am-modal-dialog">
            <div class="am-modal-hd">查找书签</div><a class="am-badge am-badge-secondary am-round">支持模糊查询标题</a>
            <div class="am-modal-bd">
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" placeholder="标题：( ˘ ³˘)♥"></div>

            </div>
            <div class="am-modal-footer">
                <span class="am-modal-btn" data-am-modal-cancel>取消</span>
                <span class="am-modal-btn" data-am-modal-confirm>提交</span>
            </div>
        </div>
    </div>
    <!-- 查找模拟框结束 -->

    <!-- 删除模拟框开始 -->
    <div class="am-modal am-modal-confirm" tabindex="-1" id="del_confirm">
        <div class="am-modal-dialog">
            <div class="am-modal-bd">亲 ♥，确定要删除这条记录吗？</div>
            <div class="am-modal-footer">
                <span class="am-modal-btn" data-am-modal-cancel>取消</span>
                <span class="am-modal-btn" data-am-modal-confirm>确定</span>
            </div>
        </div>
    </div>
    <!-- 删除模拟框结束 -->

    <!-- 修改模拟框开始 -->
    <div class="am-modal am-modal-prompt" tabindex="-1" id="modify_prompt">
        <div class="am-modal-dialog">
            <div class="am-modal-hd">修改书签
            <div class="am-modal-bd">
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" id="m_title"></div>
                <div class="am-form-group am-form-icon" style="margin:10px">
                    <input type="text" class="am-modal-prompt-input" id="m_href"></div>

                    <input type="hidden" class="am-modal-prompt-input" id="m_id"></div>

            </div>
            <div class="am-modal-footer">
                <span class="am-modal-btn" data-am-modal-cancel>取消</span>
                <span class="am-modal-btn" data-am-modal-confirm>提交</span>
            </div>
        </div>
    </div>
    <!-- 修改模拟框结束 -->

    <!--[if lt IE 9]>
    <script src="http://labfile.oss.aliyuncs.com/jquery/1.11.1/jquery.min.js"></script>
    <script src="http://labfile.oss.aliyuncs.com/modernizr/2.8.3/modernizr.js"></script>
    <script src="/js/polyfill/rem.min.js"></script>
    <script src="/js/polyfill/respond.min.js"></script>
    <script src="/js/amazeui.legacy.js"></script>
    <![endif]-->

    <!--[if (gte IE 9)|!(IE)]>
    <!-->
    <!--<![endif]-->

    <script type='text/javascript' src='http://labfile.oss.aliyuncs.com/jquery/1.11.1/jquery.min.js?ver=3.4.2'></script>
    <script src="assets/js/amazeui.min.js"></script>
    <script type="text/javascript">
    /**
     * 以下 ajax 的 url 接口需要根据实际情况变更
     */

    $(document).ready(function(){
        $.ajax({
            url: 'http://localhost/webbookmarker/index.php/Home/BookMarker/ShowAll',
            dataType: 'json',
            success: function(data){
                var len = data.length;
                var c = '';
                for (var i = 0; i < len; i++) {
                    c += '<li><a  id="' + data[i].id + '" style="font-size: 15px" href="' + data[i].href +
                        '" target="_blank"><img class="am-radius" src="' + data[i].icon + '" width="16px" height="16px"/> ' + data[i].title + '</a><i><a class="modify" data-id="' + data[i].id + '"><span class="am-icon-pencil"></span></a> <a class="del" data-id="' + data[i].id + '"><span class="am-icon-times"></span></a></i></li>';
                }
                $("#marker_list").append(c);
            } 
        })

        $('#add').on('click', function() {
            $('#add_prompt').modal({
                relatedTarget: this,
                onConfirm: function(e) {
                    $.ajax({
                        url: 'http://localhost/webbookmarker/index.php/Home/BookMarker/Add',
                        type: 'GET',
                        dataType: 'json',
                        cache: false,
                        data: {
                            title: e.data[0],
                            url: e.data[1]
                        },
                        success: function(data) {
                            if (data.code == 1) {
                                var c = '';
                                c = '<li><a href="' + data.content.href +
                                    '" target="_blank"><img class="am-radius" src="' + data.content.icon + '" width="16px" height="16px"/> ' + data.content.title + '</a><i><a class="modify" data-id="' + data.content.id + '"><span class="am-icon-pencil"></span></a> <a class="del" data-id="' + data.content.id + '"><span class="am-icon-times"></span></a></i></li>'
                                $("#marker_list").prepend(c);
                            } else {
                                alert("无法添加该书签");
                            };
                        }
                    })
                },
                onCancel: function(e) {}
            });
        });

        $('#search').on('click', function() {
            $('#search_prompt').modal({
                relatedTarget: this,
                onConfirm: function(e) {
                    $.ajax({
                        url: 'http://localhost/webbookmarker/index.php/Home/BookMarker/Search',
                        type: 'GET',
                        dataType: 'json',
                        cache: false,
                        data: {
                            keyworld: e.data
                        },
                        success: function(data) {
                            if (data.code == 1) {
                                var len = data.content.length;
                                var c = '';
                                for(var i = 0; i < len; i++) {
                                    c += '<li><a href="' + data.content[i].href +
                                        '" target="_blank"><img class="am-radius" src="' + data.content[i].icon + '" width="15px" height="15px"/> ' +
                                        data.content[i].title + '</a></li>'; 
                                }
                                $("#marker_list").empty().prepend(c);
                            } else {
                                alert("没找到相关的书签");
                            };
                        }
                    })
                },
                onCancel: function(e) {}
            });
        });

        $('#back').on('click', function() {
            $.ajax({
            url: 'http://localhost/webbookmarker/index.php/Home/BookMarker/ShowAll',
            dataType: 'json',
            success: function(data){
                var len = data.length;
                var c = '';
                for (var i = 0; i < len; i++) {
                    c += '<li><a  style="font-size: 15px" href="' + data[i].href +
                        '" target="_blank"><img class="am-radius" src="' + data[i].icon + '" width="16px" height="16px"/> ' +
                        data[i].title + '</a><i><a class="modify" data-id="' + data[i].id + '"><span class="am-icon-pencil"></span></a> <a class="del" data-id="' + data[i].id + '"><span class="am-icon-times"></span></a></i></li>';
                }
                $("#marker_list").empty();
                $("#marker_list").append(c);
            } 
        })
        })

        $(".del").live("click", function() {
            var dataid = $(this).attr('data-id');
            $.ajax({
                url: 'http://localhost/webbookmarker/index.php/Home/BookMarker/Del',
                type: 'GET',
                dataType: 'json',
                data: {
                    id: dataid
                },
                success: function(data) {
                    if (data.code == 1) {
                        location.reload(true); 
                    } else {
                        alert("删除书签失败");
                    }
                }
            })
        })

        $(".modify").live("click", function() {

            var dataid = $(this).attr('data-id');
            var dtitle = $('#' + dataid).text().trim();
            var dhref = $('#' + dataid).attr('href');

            $("#m_title").val(dtitle);
            $("#m_href").val(dhref);
            $("#m_id").val(dataid);

            $('#modify_prompt').modal({
                relatedTarget: this,
                onConfirm: function(e) {
                    $.ajax({
                        url: 'http://localhost/webbookmarker/index.php/Home/BookMarker/Alter',
                        type: 'GET',
                        data: {
                            id: dataid,
                            title: e.data[0],
                            href: e.data[1]
                        },
                        success: function(data) {
                            location.reload(true);
                        }
                    })
                },
                onCancel: function(e) {}
            });
        })

    })
    </script>

</body>
</html> 
```