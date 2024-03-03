# 第 6 节 博客的文章管理和评论功能

## 实验介绍

本实验是[实验楼](https://www.shiyanlou.com)课程[《Rails 基础入门》](https://www.shiyanlou.com/courses/103)的第六节实验。本节实验中，将实现博客项目的用户登录功能以及各个视图。这节实验中，我们主要通过分析代码观察博客项目文章和评论功能是怎么实现的。

本节实验代码位于本课程[Rails 基础入门代码库](http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103)项目中`chapter6`目录，你可以克隆该项目到本地进行查看。

## 文章管理功能

在第五节实验中，我们已经实现了文章的相关管理页面，这一节中，我们将实现文章管理的后台逻辑。文章管理功能主要包含显示，编辑，删除和创建功能。分别对应了`PostsController`控制器中的`index`, `show`, `admin_index`, `create`, `edit`和`update`动作。在开始实现这些后台逻辑之前，我们需要调整下之前设置的路由，之前设置的路由效果如下图：

![此处输入图片的描述](img/0a3eed5e72da430134053781a8fa4d58.jpg)

仔细查看，会发现没有`/posts/:id`路由，但是我们需要这个路径用于显示文章。同时我们会发现`POST /posts/:id/comments` 这条路由没有名称，下面我们修改下`app/config/routes.rb`路由配置文件，将其修改为以下内容：

```rb
Rails.application.routes.draw do
  root 'posts#index'

  get '/login', to: 'users#login'
  post '/login', to: 'users#login'
  get '/logout', to: 'users#logout'
  get '/profile', to: 'users#show'
  patch '/profile', to: 'users#update'

  scope '/admin'  do
    resources :posts, except: [:index, :show]
  end

  get '/admin/posts', to: 'posts#admin_index'
  get '/posts/:id', to: 'posts#show', as: :show_post

  post '/posts/:id/comments', to: 'comments#create', as: "create_post_comment"

  scope '/admin' do
    resources :comments, only: [:destroy]
  end
end 
```

我们先来看看有视图的文章控制器方法是怎么实现的。需要显示页面地方主要有：博客项目首页，每一篇博文的详情页面，管理员管理博文的页面，新建博文页面以及更新博文页面，这五个页面分别对应了`index`，`show`，`admin_index`，`new`，`edit`方法，这五个方法对应的视图文件在`app/views/posts`目录下，视图名称和方法同名。五个方法具体实现如下：

```rb
 def index
    # 查询返回所有的文章，按创建时间倒序排序
    @posts = Post.order(created_at: :desc)
  end

  def show
    # 根据文章 id 查询文章
    @post = Post.find(params[:id])
    # 在视图中，会用到此实例对象
    @comment = Comment.new
  end

  # 管理文章的首页
  def admin_index
    @posts = Post.order(created_at: :desc)
  end

  # 创建新文章页面
  def new
    @post = Post.new
  end

  # 更新文章的页面 
  def edit
    @post = Post.find(params[:id])
  end 
```

可以看到，实现非常简单，只是简单的查询数据库操作，这些方法中的实例变量(以`@`开头的变量）都会传递到相应的视图中去，而这些方法默认渲染的视图就是和方法同名的视图，关于这一点我们已经在前面的实验中学习过了。我们在`show`方法中创建了一个`@comment`实例变量，之所以创建这个变量是由于我们会在`app/views/posts/show.html.erb`视图中使用它用于渲染发布评论的表单。`new`方法中的`@post`实例变量也是同样的作用，让我们看下`new`方法对应的视图文件`app/views/posts/new.html.erb`的代码：

```rb
<%- model_class = Post -%>
<div class="page-header">
  <h1><%=t '.title', :default => [:'helpers.titles.new', 'New %{model}'], :model => model_class.model_name.human.titleize %></h1>
</div>
<%= render :partial => 'form' %> 
```

以上就是`app/views/posts/new.html.erb`的代码，其主要作用是渲染了`app/views/posts/_form.html.erb`局部视图，我们接着看下这个局部视图的具体代码：

```rb
<%= form_for @post, :html => { :class => "form-horizontal post" } do |f| %>
    <% if @post.errors.any? %>
    <div id="error_expl" class="panel panel-danger">
      <div class="panel-heading">
        <h3 class="panel-title"><%= pluralize(@post.errors.count, "error") %> prohibited this post from being saved:</h3>
      </div>
      <div class="panel-body">
        <ul>
        <% @post.errors.full_messages.each do |msg| %>
          <li><%= msg %></li>
        <% end %>
        </ul>
      </div>
    </div>
  <% end %>

  <%= f.hidden_field :id %>
  <div class="form-group">
    <%= f.label :title, :class => 'control-label' %>
    <div class="controls">
      <%= f.text_field :title, :class => 'form-control' %>
    </div>    <%= error_span(@post[:title]) %>
  </div>
  <div class="form-group">
    <%= f.label :text, :class => 'control-label' %>
    <div class="controls">
        <%= f.text_area :text, :class => 'form-control', :rows =>"8" %>
    </div>
    <%= error_span(@post[:text]) %>
  </div>

  <div class="form-group">
  <%= f.submit nil, :class => 'btn btn-success' %>
  <%= link_to t('.cancel', :default => t("helpers.links.cancel")),
            posts_path, :class => 'btn btn-default' %>
  </div>
<% end %> 
```

这个局部视图的作用是根据`@post`变量渲染出表单，`form_for`方法是 Rails 内置用于渲染表单的帮助方法。这个表单渲染后生成的关键部分代码如下：

```rb
<form accept-charset="UTF-8" action="/admin/posts" class="form-horizontal post" id="new_post" method="post">
  <input id="post_id" name="post[id]" type="hidden">
  <input class="form-control" id="post_title" name="post[title]" type="text">    </div>
  <textarea class="form-control" id="post_text" name="post[text]" rows="8"></textarea>
  <input class="btn btn-success" name="commit" type="submit" value="Create Post">  <a class="btn btn-default" href="/admin/posts">Cancel</a>
</form> 
```

以上生成的表单，我们去掉了其他 HTML 代码，只留下了表单的关键的 HTML 代码。需要注意的是，`POST`方法提交的参数名是用形如`post[title]`的名称表示的，这样`POST`方法提交成功后，在 Rails 中，可以使用`params[:post][:title]`的形式访问到提交的`post[title]`参数。

接着，我们实现操作 Post 文章对象的方法：`create`, `update`, `destroy`方法，这些方法的具体代码如下：

```rb
 # 创建文章
  def create
    # 根据传递的参数创建文章
    @post = Post.new(post_params)
    # 如果创建成功则显示该文章
    if @post.save
      redirect_to show_post_path(@post)
    else
      # 创建失败，在渲染创建文章的页面，同时会显示相关的错误信息
      render 'new'
    end
  end

  # 更新文章
  def update
    @post = Post.find(params[:post][:id])
    # 更新成功则返回管理页面
    if @post.update(post_params)
      flash[:success] = "Update post with id: #{@post.id}  successfully"
      redirect_to admin_posts_path
    else
      # 更新失败则显示更新页面，同时会显示相关的错误信息
      render 'edit'
    end

  end

  # 删除文章
  def destroy
    post = Post.find(params[:id])
    post.destroy
    # 同时 flash 一条删除成功的信息
    flash[:danger] = "Dlete post with id: #{post.id} successfully"
    redirect_to admin_posts_path
  end

  private
  # 允许 params[:post][:title] 和 params[:post][:text] 被访问
  def post_params
    params.require(:post).permit(:title, :text)
  end 
```

以上几个方法的实现都大同小异。我们主要分析下`create`方法。在分析`new`方法时，我们知道`new`方法对应的视图将渲染一个表单，该表单提交的地址执行的控制器方法就是`create`方法。当我们提交`new`方法的表单时，在`create`方法中可以通过`params[:post]`访问到提交的所有参数。我们可以用`params[:post]`的值创建一个新的 `Post`对象，如下代码：

```rb
 # 创建文章
  def create
    # 根据传递的参数创建文章
    @post = Post.new(post_params)
    # 如果创建成功则显示该文章
    if @post.save
      redirect_to show_post_path(@post)
    else
      # 创建失败，在渲染创建文章的页面，同时会显示相关的错误信息
      render 'new'
    end
  end 
```

以上代码中，我们使用`new`对应的视图表单提交的参数创建一个新的 Post 对象，如果对象能保持成功，则显示新创建的 Post 对象，如果对象创建不成功（一般是由于验证不通过），则使用此时创建的`@post`实例（会包含一些错误信息）渲染`new`方法对应的视图，也就是`app/views/posts/new.html.erb`文件。这时候，在页面中就会显示相应的验证错误信息，这是由于在`app/views/posts/_form.html.erb`中，有如下代码的原因：

```rb
 <% if @post.errors.any? %>
    <div id="error_expl" class="panel panel-danger">
      <div class="panel-heading">
        <h3 class="panel-title"><%= pluralize(@post.errors.count, "error") %> prohibited this post from being saved:</h3>
      </div>
      <div class="panel-body">
        <ul>
        <% @post.errors.full_messages.each do |msg| %>
          <li><%= msg %></li>
        <% end %>
        </ul>
      </div>
    </div>
  <% end %> 
```

以上代码，会使用`@post.errors.any?`方法检查`@post`实例是否有错误信息，如果有的话则显示错误信息详情。

在分析`create`方法时，你可能有点奇怪？为什么没有直接使用形如`@post = Post.new(params[:post])`的代码去创建 Post 对象，而是使用了一个私有方法`post_params`。这是由于，默认情况下 Rails 的 [Strong Parameters](http://edgeguides.rubyonrails.org/action_controller_overview.html#strong-parameters) 机制会防止非法参数的使用，除非我们允许这些参数。所以我们定义了`post_params`函数：

```rb
 private
  # 允许 params[:post][:title] 和 params[:post][:text] 被访问
  def post_params
    params.require(:post).permit(:title, :text)
  end 
```

经过以上分析，相信大家对博客文章管理功能，已经一目了然了。如果你有什么疑惑的地方，建议仔细分析源代码，源代码中有比较详尽的注释。

## 评论功能实现

评论功能主要包括发表评论和删除评论。任何访问中都可以发表评论，只有管理员才能删除评论。所以我们在`CommentsController`中只需要，实现发表和删除评论的方法就可以完成以上功能，下面是`CommentsController`的代码（`app/controllers/comments_controller.rb`）的代码：

```rb
class CommentsController < ApplicationController
  include UsersHelper
  before_action :require_login, except: [:create]

  # 创建评论
  def create
    # 查询该评论应该关联的文章
    @post = Post.find(params[:id])
    # 通过关联关系创建评论
    @comment = @post.comments.create(comment_params)
    redirect_to show_post_path(@post)
  end

  # 删除评论
  def destroy
    @comment = Comment.find(params[:id])
    @post = @comment.post
    @comment.destroy
    redirect_to show_post_path(@post)
  end

  private
  def comment_params
    params.require(:comment).permit(:author, :content)
  end
end 
```

可以看到非常简单，和`PostsController`的代码非常相似。我们简单分析下`create`方法。在该方法中，我们首先根据传递的`params[:id]`参数查询对应的`@post`，之所以可以这么做，是因为，我们在路由中配置了`post '/posts/:id/comments', to: 'comments#create', as: "create_post_comment"` 这样的路由，其中`:id`参数就传递到`create`方法中。接着我们通过`@post.comments.create(comment_params)`的方式创建了评论，之所以可以这么做，是因为在之前的实验中，我们定义了`Post`和`Comment`模型之间的关联关系（使用`has_many`和`belong_to`方法）。

那在什么视图中调用这两个方法呢？我们主要在`app/views/posts/show.html.erb`视图中调用这个方法，也就是现实文章详情的页面。你可以在`app/views/posts/show.html.erb`中发现下面的代码（部分）：

```rb
<% @post.comments.each do |comment| %>
<!--省略了部分代码 -->
          <% if logged_in? %>
          <%= link_to comment_path(comment),
                      :method => :delete,
                      :class => "btn btn-xs btn-danger pull-right" do %>
          <i class="fa fa-trash-o"></i>
          <% end %>
          <% end %>
<% end %>
<%= form_for @comment, url: create_post_comment_path, :html => { :class => "form-horizontal comment" } do |f| %>
<!--省略了部分代码 --> 
```

以上代码主要用户再文章详情页面，显示文章相关的评论和渲染发表评论的表单。首先我们会通过`@post.comments.each do |coment|` 代码块显示所有相关的评论，代码块中还使用了`if logged_in?`方法判断管理员是否登陆，如果登陆的话，则通过`link_to`方法显示一个删除评论的按钮。`lin_to`是 Rails 内置的帮助方法，用于创建一个 `<a>`标签，同时我们可以自定义一些属性，比如 class。最后，我们又一次使用`form_for`方法渲染了创建评论的表单，这个表单的提交地址是`create_post_comment_path`，也就是`/posts/:id/comments`，对应到`CommentController`的`create`方法。

## 使用博客

博客的所有功能就已经实现了。现在我们开始试试它的功能吧。

本节实验所有相关代码位于本课程[Rails 基础入门代码库](http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103)项目的`chapter6`目录中。下面我们将`chapater6/blog`中的博客项目运行起来。

```rb
$ cd ~/Code
$ git clone  http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103.git rails_base 
```

以上命令我们克隆了本课程的代码到`rails_base`目录，接着我们进入`~/Code/rails_base/chapter6/blog`目录，开始安装博客项目依赖的 gem 包：

```rb
$ cd ~/Code/rails_base/chapter5/blog-v1
$ bundle install
$ rake db:migrate 
```

我们还需要创建一个管理员账户，可以通过`rails console`命令进行：

```rb
$ rails console
irb> User.create(name: "shiyanlou", email: "support@shiyanlou.com", password: "shiyanlou")
irb> exit 
```

接着启动博客项目：

```rb
$ rails server 
```

好了，让我们开始体验博客项目吧。

![此处输入图片的描述](img/fb4a132cd77e36dda4ca783543ff6949.jpg)

到目前为止，本课程[Rails 基础入门代码库](http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103)就全部结束啦。在本课程中，我们学习了 Rails 的控制器，路由，数据库模型和视图，并利用这些知识实现了一个基本功能完备的博客项目。相信通过认真学习，对于 Rails 现在你已经入门了。当然，Rails 是一个强大的 web 开发框架，本课程难以覆盖全部内容，希望你能将本课程作为起点，一步一步称为一个 Rails 高手。