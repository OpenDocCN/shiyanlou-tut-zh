# 第 2 节 Rails 控制器与路由

## 实验介绍

本实验是[实验楼](https://www.shiyanlou.com)课程[《Rails 基础入门》](https://www.shiyanlou.com/courses/103)的第二节实验。本节实验中，我们将学习使用 Rails 的控制器与路由。

本节实验代码位于本课程[Rails 基础入门代码库](http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103)项目中`chapter2`目录，你可以克隆该项目到本地进行查看。

本课程基于[《Ruby on Rails 入门》](http://guides.ruby-china.org/)制作。本课程在其基础加入了大量说明，并针对实验楼环境进行了适当修改。希望通过学习本课程，你能熟悉 Rails 中的各种概念，并能使用 Rails 开发简单的项目。

## 控制器

Action Controller 是 MVC 中的 C（控制器）。路由决定使用哪个控制器处理请求后，控制器负责解析请求，生成对应的请求。Action Controller 会代为处理大多数底层工作，使用易懂的约定（记住：多约定，少配置），让整个过程清晰明了。

在大多数按照 REST 规范开发的程序中，控制器会接收请求，从模型中获取数据，或把数据写入模型，再通过视图生成 HTML。如果控制器需要做其他操作，也没问题，以上只不过是控制器的主要作用。

因此，控制器可以视作模型和视图（数据库）的中间人，让模型中的数据可以在视图中使用，把数据显示给用户，再把用户提交的数据保存或更新到模型中。

控制器其实也是一个数据枢纽。之所以这么说，是因为通过浏览器提交的数据比如：URL 查询字符串，POST 的数据等，又如从数据库中查询得到的数据，都会在控制器中做一些处理，然后转发给其他对象。比如，控制器从浏览器提交的数据中，解析出各种参数，然后根据这些参数在数据库中操作对象，最后将操作的结果通过视图展示给用户。

### 控制器组成

在 Rails 中，控制器其实就是一个类，继承自`ApplicationController`，在上一节的实验中，我们有看到`welcome`控制器，其代码如下：

```rb
class WelcomeController < ApplicationController
  def index
  end
end 
```

可以看到其也继承自`ApplicationController`。我们上面上面说到，Rails 应用接到请求后，路由决定执行哪个控制器和控制器的哪个动作。这里的动作，就是控制器的各种方法，比如上面代码中的`index`方法。同时在 `默认约定` 的情况下，当用户访问`/welcome/index`url 的时候，路由会默认执行 `WelcomeController`的`index`方法，可以看到这是和 URL 一一对应的。

现在假如，我们有一个控制器：

```rb
class ClientsController < ApplicationController
  def new
  end
end 
```

如果按照默认路由，当用户访问`/clients/new`新建客户，Rails 会创建一个`ClientsController`实例，运行`new`方法。同时我们注意到在上面这段代码中，即使`new`方法是空的也没办法，因为默认会渲染`new.html.erb`视图，除非指定执行其他操作。

### 控制器中的参数

控制器的动作中，往往需要获取用户发送的数据，或其他参数。在网页程序中参数分为两类。第一类随 URL 发送，即 URL 中 ? 符号后面的部分，一般被称为查询字符串。第二类经常称为`POST 数据`，一般来自用户填写的表单。之所以叫做`POST 数据`是因为，只能随 HTTP POST 方法发送。Rails 不区分这两种参数，在控制器中都可通过`params` Hash 获取，看下面的代码：

```rb
class ClientsController < ApplicationController
  # 这个动作可以接收查询字符串中的 status 参数，比如下面的这种请求方式
  # /clients?status=activated
  def index
    # 通过 params 哈希进行获取参数
    if params[:status] == "activated"
      @clients = Client.activated
    else
      @clients = Client.inactivated
    end
  end

  # 这个方法可以接收 POST 方法提交的参数
  def create
    @client = Client.new(params[:client])
    if @client.save
      redirect_to @client
    else
      render "new"
    end
  end
end 
```

在上面的代码中，我们定义了一个`ClientsController`控制器，它的`idnex`方法（动作）可以接收形如`/clients?status=activated`的参数，第二个方法可以接收通过 POST 请求提交的参数。可以看到不管是通过查询字符串还是 POST 方法提交的参数我们都可以通过`param`哈希获取。那有什么办法可以判断请求的方法呢？我们在控制器动作中还可以访问`request`对象，通过该对象可以获取到请求的方法：通过`request.method`可以获取请求的方法，`request.get?`可以判断请求的方法是不是`GET`。关于`request`对象的更多内容可以参考 Rails API 文档: http://api.rubyonrails.org/classes/ActionDispatch/Request.html 。

有时候传递数组给 Rails，有什么办法呢？实际上`params` 哈希不局限于只能使用一维键值对，其中可以包含数组和嵌套的哈希。如果要想发送数组参数给 Ralis，只需要向下面这样请求就可以了：

```rb
GET /clients?ids[]=1&ids[]=2&ids[]=3 
```

这样访问`params[:id]`就可以获取到`["1", "2", "3"]`数组了。

如果想要发送嵌套的哈希参数，只需要在方括号中指定键名就可以了，比如下面是一个通过`POST`方法提交的表单:

```rb
<form accept-charset="UTF-8" action="/clients" method="post">
  <input type="text" name="client[name]" value="Acme" />
  <input type="text" name="client[phone]" value="12345" />
  <input type="text" name="client[address][postcode]" value="12345" />
  <input type="text" name="client[address][city]" value="Carrot City" />
</form> 
```

如果我们向 Rails 提交上面的表单，`params[:client]`的值是

```rb
{
  "name" => "Acme",
  "phone" => "12345",
  "address" => {
    "postcode" => "12345",
    "city" => "Carrot City"
  }
} 
```

可以看到`params[:client][:address`是一个嵌套的哈希。

在 Rails 还可以访问路由传递的参数，所谓路由传递的参数是 URL 的一部分，或者通过在`routes.rb`（该文件是 Rails 项目的路由配置文件）文件中的路由条目指定的参数。假如在`routes.rb`文件中有以下一项条目：

```rb
get '/clients/:status' => 'clients#index', foo: 'bar' 
```

在这个例子中，当用户访问`/clients/active`的时候，`params[:status]`的值就是`"active"`。同时`params[:foo`的值也被设置为`"bar"`。

## 路由

Rails 路由能识别 URL，将其分发给控制器的动作进行处理，还能生成路径和 URL，无需直接在视图中硬编码字符串。简单来说，路由第一个作用是根据请求的 URL 执行相应控制器的方法，第二个作用是我们可以根据路由生成的路径，类似于 Flask 框架中的 `url_for` 产生的效果。

当 Rails 程序收到如下请求时：

```rb
GET /patients/17 
```

会查询路由，执行相应控制器的方法。如果在`routes.rb`中首个匹配的路由是：

```rb
get '/patients/:id', to: 'patients#show' 
```

那么这个请求就交给`patients`控制器的`show`方法处理，并把`{id: '17'}`穿入`params`中。

上面我们说到路由还可以生成路径和 URL。如果把前面的的路由修改成

```rb
get '/patients/:id', to 'patients#show', as: 'patient' 
```

在控制器在有如下代码：

```rb
@patient = Patient.find(17) 
```

相应的视图中有如下代码：

```rb
<%= link_to 'Patient Record', patient_path(@patient) %> 
```

那么路由就会生成路径`/patients/17`，也就是`patient_path(@patient`的计算结果。

Rails 项目的路由配置文件位于 `Rails 应用目录/config/routes.rb`，以下内容是一个 Rails 项目的`routes.rb`文件：

```rb
Rails.application.routes.draw do
  get 'welcome/index'

  # The priority is based upon order of creation: first created -> highest priority.
  # See how all your routes lay out with "rake routes".

  # You can have the root of your site routed with "root"
  root 'welcome#index'

  # Example of regular route:
  #   get 'products/:id' => 'catalog#view'

  # Example of named route that can be invoked with purchase_url(id: product.id)
  #   get 'products/:id/purchase' => 'catalog#purchase', as: :purchase

  # Example resource route (maps HTTP verbs to controller actions automatically):
  resources :photos

  # Example resource route with options:
  #   resources :products do
  #     member do
  #       get 'short'
  #       post 'toggle'
  #     end
  #
  #     collection do
  #       get 'sold'
  #     end
  #   end

  # Example resource route with sub-resources:
  #   resources :products do
  #     resources :comments, :sales
  #     resource :seller
  #   end

  # Example resource route with more complex sub-resources:
  #   resources :products do
  #     resources :comments
  #     resources :sales do
  #       get 'recent', on: :collection
  #     end
  #   end

  # Example resource route with concerns:
  #   concern :toggleable do
  #     post 'toggle'
  #   end
  #   resources :posts, concerns: :toggleable
  #   resources :photos, concerns: :toggleable

  # Example resource route within a namespace:
  #   namespace :admin do
  #     # Directs /admin/products/* to Admin::ProductsController
  #     # (app/controllers/admin/products_controller.rb)
  #     resources :products
  #   end
end 
```

这个的注释非常有用，比如路由匹配的优先级，以及其他一些使用方法都在注释中有说明。在接下来讲解路由的内容中，请经常回顾这些代码。

### 资源路由

Rails 同时是一个 REST 框架，所以根据需要操作的资源，我们只需要一行代码就可以产生操作资源的各种路由。比如在`routes.rb`文件中有这样一行代码：

```rb
resources :photos 
```

那么我们现在就有了操作`photos`资源（这里是复数资源，也就是需要通过 id 访问的资源）的一系列路由，并且这些路由会按照约定映射到控制器的相关动作上。按照约定，当我们以`resources :photos`定义路由后，会自动有如下的映射关系：

```rb
|HTTP 方法    |路径 |控制器#动作 |作用 |
|:----------|:------|:--------------|:----|
|GET  |    /photos|    photos#index|   显示所有图片|
|GET  |    /photos/new|    photos#new| 显示新建图片的表单|
|POST |    /photos|    photos#create|  新建图片|
|GET  |    /photos/:id|    photos#show|    显示指定的图片|
|GET  |    /photos/:id/edit|   photos#edit|    显示编辑图片的表单|
|PATCH/PUT|    /photos/:id|    photos#update|  更新指定的图片|
|DELETE    | /photos/:id|  photos#destroy| 删除指定的图片|
```

同时当使用 `rsources :photos`定义路由后，我们可以使用一些帮助方法，生成对应的路径：

*   `photos_path` 方法返回 `"/photos"` 路径
*   `new_photo_path` 方法返回 `"/photos/new"` 路径
*   `edit_photo_path(:id` 方法返回 `"/photos/:id/edit"`路径，比如 `edit_photo_path(10)`返回 `"/photos/10/edit"`
*   `photo_path(:id)`方法返回`"/photos/:id"`路径，例如`photo_path(10)`返回路径`"/photos/10"`

以上方法都有对应的 `_url`形式，也就是将`_path`替换为`_url`后形成的方法。比如`photos_path`有对应的`photos_url`方法，前者生成的是站内的路径，后者是生产的带域名，端口一级路径的 URL，也就是我们平时访问的网址。

### 单数资源路由

有的时候我们希望不通过 ID 就能访问资源，比如`/profile`页面显示当前登入用户的个人信息。针对这种需求，可以使用单数资源，把对`/profile`页面的访问映射到控制器的`show`方法。比如我们可以添加以下路由：

```rb
get 'profile', to: 'users#show' 
```

将对`/profile`页面的访问映射到`users`控制器的`show`方法上。我们还可以使用以下方式定义单数资源路由：

```rb
resources :geocoder 
```

这样会生成六个路由，全部映射到`GeocodersController`控制器:

```rb
|HTTP 方法    |路径 |控制器#动作 |作用 |
|:----------|:------|:--------------|:----|
|GET  |    /gecoder/new|   geocoders#new | 显示新建 geocoder 的表单|
|POST |    /geocoder|  geocoders#create|   新建 geocoder|
|GET  |    /geocoder|  geocoders#show| 显示唯一的 geocoder 资源|
|GET  |    /geocoder/edit| geocoders#edit| 显示编辑 geocoder 的表单|
|PATCH/PUT|    /geocoder|  geocoders#update|   更新唯一的 geocoder 资源|
|DELETE| /geocoder|    geocoders#destroy|  删除 geocoder 资源|
```

### 带命名空间的控制器的路由

你可能想把一系列控制器放在一个命名空间内，最常见的是把管理相关的控制器放在 `Admin::`命名空间内（通过`rails generate controller Admin::controllern_name action`方式的创建） 。但是我们怎么访问让 URL 能匹配到这些控制器呢？ 可以在 `routes.rb`文件中做如下路由声明：

```rb
namespace :admin do
  resources :posts, :comments
end 
```

上述代码会为`posts`和`comments`生成很多路由。对`Admin::PostsController`来说， Rails 会生成：

```rb
|HTTP 方法    |路径 |控制器#动作 |相应的用于生成路径的方法 |
|:----------|:------|:--------------|:----|
|GET   |    /admin/posts|   admin/posts#index|  admin*posts*path|
|GET   |    /admin/posts/new|   admin/posts#new|    new*admin*post*path|
|POST  |    /admin/posts|   admin/posts#create| admin*posts*path|
|GET   |    /admin/posts/:id|   admin/posts#show|   admin*post*path(:id)|
|GET   |    /admin/posts/:id/edit|  admin/posts#edit|   edit*admin*post*path(:id)|
|PATCH/PUT|    /admin/posts/:id|   admin/posts#update| admin*post*path(:id)|
|DELETE |    /admin/posts/:id|   admin/posts#destroy|    admin*post*path(:id)|
```

以上方式会将`/admin/posts`映射到`Admin:PostsController`控制器上，但是有的时候，我们想将没有`/admin`前缀的路径也映射到`Admin:PostsController`可以这么做：

```rb
scope module: 'admin' do
  resources :posts
end 
```

这样访问`/posts`的时候也会映射到`Admin:PostsController`。又有的时候，我们不想要使用命名空间，也想把`/admin/posts`映射到普通控制器上，那么可以这么做：

```rb
scope '/admin' do
  resources :posts
end 
```

这样访问`/admin/posts`时就会映射到控制器`PostsController`上了。

### 嵌套资源

有的时候有些资源不会单独显示，总是跟随者另外一个资源一起显示的。比如博客文章的评论，评论总是属于某一篇博文。这时候该怎么样使用路由标示这样的层级关系呢？

```rb
resources :magazines do
  resources :ads
end 
```

这样一来，我们就为创建了嵌套路由。上诉路由声明会生成以下路由：

```rb
|HTTP 方法    |路径 |控制器#动作 |相应的用于生成路径的方法 |
|:----------|:------|:--------------|:----|
|GET   |/magazines/:magazine*id/ads|    ads#index|  显示指定杂志的所有广告|
|GET   |/magazines/:magazine*id/ads/new| ads#new| 显示新建广告的表单，该告属于指定的杂志|
|POST   |/magazines/:magazine*id/ads| ads#create| 创建属于指定杂志的广告|
|GET   |/magazines/:magazine*id/ads/:id|    ads#show|   显示属于指定杂志的指定广告|
|GET   |/magazines/:magazine*id/ads/:id/edit|    ads#edit|   显示编辑广告的表单，该广告属于指定的杂志|
|PATCH/PUT| /magazines/:magazine*id/ads/:id| ads#update| 更新属于指定杂志的指定广告|
|DELETE| /magazines/:magazine_id/ads/:id|    ads#destroy|    删除属于指定杂志的指定广告|
```

上述路由还会生成 `magazine_ads_url` 和 `edit_magazine_ad_path` 等路由帮助方法。这些帮助方法的第一个参数是 `Magazine` 实例，例如`magazine_ads_url(@magazine)`。

在以上路由声明中，所有`ads`的所有路由都嵌套在`magazines`下面，有的时候，我们只需要`ads`对应控制器的部分方法嵌套在`magazines`资源下面。比如`/magazines/:magazine_id/ads`可以用于查看具体杂志的所有广告，但是当我们需要删除一个广告时只需要知道这个广告的 id 就行，不需要知道具体的杂志，所以也不需要`DELETE /magazine/:magazine_id/ads/:id`这样的路由，那该怎么做呢？看下面的路由声明：

```rb
resources :posts do
  resources :comments, only: [:index, :new, :create]
end
resources :comments, only: [:show, :edit, :update, :destroy] 
```

以上的路由声明中，只把`Comments`控制器的`index`, `new`和`create`方法嵌套在`Posts`资源下，而其他的方法则不进行嵌套。

关于路由内容就暂时介绍到这里，更多的详细内容可以参考：http://guides.rubyonrails.org/routing.html

## 博客项目的路由和控制器

在本课程第一个实验中，我们创建了一个博客项目，但是没有实现真正有用的功能。从本节实验开始我们将实现一些有用的功能。

在本系列课程中，我们将实现一个功能基本完备的博客项目，具体功能包括：

*   博客是一个单用户博客
*   管理员能发布，删除和修改博客文章
*   管理员可以删除评论
*   管理员能个人信息
*   博客文章可以随便查看
*   可以任意发表评论

具体来说，博客的增删改功功能，以及评论的删除功能都是属于管理员功能，我们将这部分功能相关的控制器放在`Admin::`命令空间下，而评论随博客一起显示。而管理员修改密码则位于`/profile`页面，通过执行`User`控制器的相应代码实现。所以目前，我们需要有三个控制器，分别是`PostsController`， `CommentsController`以及`UsersController`。下表是我们设计的 URL 以及对应的控制器方法。

```rb
|HTTP 方法    |路径 |控制器#动作 |用途 |
|:----------|:------|:--------------|:----|
|GET | /posts | posts#index |用户浏览的博文列表|
|GET | /posts/:id| posts#show| 用户浏览某一篇博文|
|GET | /admin/posts | posts#admin_index |管理员浏览的博文列表|
|POST| /admin/posts | posts#create | 管理员创建博文 |
|GET | /admin/posts/:id/edit| posts#edit |管理员修改博文页面|
|PATCH| /admin/posts/:id| posts#update| 管理员修改博文|
|DELETE| /admin/posts/:id| posts#destroy| 管理员删除博文|
|POST| /posts/:id/comments| comments#create| 任意用户创建评论|
|DELETE| /admin/comments/:id | comments#destroy |管理员删除评论
|GET | /login | users#login|管理员登录页面|
|POST| /login | users#login|管理员登录|
|GET| /logtou | users#logout|管理员登出|
|GET| /profile | users#show|管理员信息编辑页面|
|PATCH| /profile | users#update| 更新管理员信息，如密码|
```

下面我们在第一节项目的基础上创建它们。

进入目录 `/home/shiyanlou/Code/blog`目录，分别执行以下命令：

```rb
$ rails g controller posts index show admin_index create edit update destroy
$ rails g controller comments create destroy 
$ rails g controller users login logout show update 
```

以上命令我们分别创建了`PostsController`， `CommentsController`以及`UsersController`控制器，其中`rails g`是命令`rails generate`的缩写。接着我们在`/home/shiyanlou/Code/blog/config/routes.rb`中输入以下内容:

```rb
Rails.application.routes.draw do
  # 首页
  root 'posts#index'

  # 用户相关路由
  get '/login', to: 'users#login'
  post '/login', to: 'users#login'
  post '/logout', to: 'users#logout'
  get '/profile', to: 'users#show'
  patch '/profile', to: 'users#update'

  # 管理员增删改博文路由
  scope '/admin' do
    resources :posts, except: [:index, :show]
  end

  get '/admin/posts', to: 'posts#admin_index'

  # 博文评论的路由
  post '/posts/:id/comments', to: 'comments#create'

  scope '/admin' do
    resources :comments, only: [:destroy]
  end
end 
```

在以上代码中，我们安装上文中表格声明各项路由。为了查看路由配置的效果，我们可以在项目中使用命令`rake routes`查看生成的路由：

![此处输入图片的描述](img/dc3c843120f49ed2c9b175e22929ed38.jpg)

可以看到符合我们表格中的设计。

好了，本节实验就到这里，下一节中我们将学习 Rails 中的 `Active Record`。