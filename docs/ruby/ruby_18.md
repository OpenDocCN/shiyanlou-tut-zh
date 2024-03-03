# 第 5 节 博客项目用户登录功能与视图的实现

## 实验介绍

本实验是[实验楼](https://www.shiyanlou.com)课程[《Rails 基础入门》](https://www.shiyanlou.com/courses/103)的第五节实验。本节实验中，将实现博客项目的用户登录功能以及各个视图。

本节实验代码位于本课程[Rails 基础入门代码库](http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103)项目中`chapter5`目录，你可以克隆该项目到本地进行查看。

## 视图页面的实现

在之前的课程内容中，我们设计了博客项目的控制器，数据库模型，并在博客项目中安装了 Bootstrap 框架，同时借助`rials g bootstrap:themed Posts`命令生成了`PostsControoler`相关的视图，这一节中我们实现了博客项目中的各个视图。由于篇幅有限，我们就不讲解具体每个视图的实现过程了，下面直接让我们看下实现子页面后的效果。

本小节实验的代码位于: http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103/src/master/chapter5/blog 目录中。我们可以通过以下命令启动博客项目：

```rb
$ cd ~/Code
$ git clone  http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103.git rails_base 
```

以上命令我们克隆了本课程的代码到`rails_base`目录，接着我们进入`~/Code/rails_base/chapter5/blog`目录，开始安装博客项目依赖的 gem 包：

```rb
$ cd ~/Code/rails_base/chapter5/blog
$ bundle install
$ rake db:migrate 
```

接着启动博客项目：

```rb
$ rails s 
```

接着在浏览器中访问`http://0.0.0.0:3000`，可以看到首页效果如下：

![此处输入图片的描述](img/c6743cd3671eca8e725a4ee7e73398ff.jpg)

接着我们依次，点击 `Manage`，`Profile`和`Login` 按钮，会有如下效果：

*   `Manage` 页面，用于管理员管理博文 ![此处输入图片的描述](img/8797714a99ce3eebb087b5a7b0749ff8.jpg)

*   `Profile`页面，用于管理员修改个人登录信息

![此处输入图片的描述](img/5d1ba5da8fe4e6929ebedca7eb11ef86.jpg)

你会发现 `Profile`页面显示是错误的，这是由于我们没有添加用户数据，导致的。在我们的博客项目中，我们只有一个管理员用户，这个用户用于创建博文。可以通过下面的方法添加这个用户。

在项目目录中执行命令`rails console`，然后通过`User.create(name: "shiyanlou", email: "support@shiyanlou.com", password: "shiyanlou")`命令创建用户。创建过程截图如下：

![此处输入图片的描述](img/8e2209d1d2e772b609a1fa302fefc931.jpg)

在上图中，我们创建用了一个名称和密码都为`shiyanlou`的用户，他的邮箱是`support@shiyanlou.com`，当我们创建完成后还使用`User.first`方法对该用户进行了查询操作。用户创建成功后，再次访问`Profile`页面，可以看到以下页面

![此处输入图片的描述](img/56b85f2f1c1cb0994031566d5d8cb39e.jpg)

*   `Login`页面，用于登录

![此处输入图片的描述](img/129f2a3ce5836b27fc82efb1700f65ee.jpg)

如果你比较仔细的话，会发现每个页面下方都包含一些`debug`信息，显示了访问页面时执行的控制器和控制器方法的名称，就如上图红框中信息一样。这是因为我们在博客项目的布局文件`app/views/layouts/applications.erb.html`文件中加入了以下一行代码：

```rb
<%= debug(params) if Rails.env.development? %> 
```

到这里整个博客项目的所有视图就实现完成了，如果你要查看具体的代码，可以查看`app/views/`目录下的视图文件源码。如果你够细心的话，应该能发现`Manage`页面按钮和`Login`页面按钮应该不能同时出现的，管理员未登录时，应该显`Login`登录按钮，而管理员登录后，只应该显示`Manage`管理按钮和`Logout`登出按钮。这就涉及到判断用户登录状态了，下一节中我们将实现这些功能。

## 用户登录功能

### `has_secure_password`方法

为了实现用户登录功能，肯定需要一个地方用户存储用户的密码。在之前实验中我们已经使用命令`rails g model User name:string email:string password:string`创建了用户数据库模型，在这个模型中，我们使用`password`字段存储用户的密码。但是显然，这样的处理方式是不合适的，因为用的密码是明文存储的，如果数据库被拖库，那么任何人就可以得到管理员的密码。好在我们可以在用户数据模型中使用 Rails 中的 [has*secure*password](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html)方法。使用这个方法后，会自动添加以下功能：

*   在数据库中的用户表的 `password_digest` 列存储安全的密码哈希值；
*   获得一对虚拟属性`password`和`password_confirmation`，之所以是虚拟的是因为这对属性不会存储到数据库中；
*   获得`authenticate`方法，如果密码正确，则返回对应的用户对象，否则放回 false；

要使用 `has_scecure_password` 方法，唯一的要求是用户模型中需要有名为`password_digest`的属性。也就是说我们需要将之前生成的用户模型中的`password`字段修改为`password_digest`。

修改数据模型的字段，我们可以采用在第三节实验中学习果的数据迁移的方法。首先用以下方法，创建一个迁移：

```rb
$ rails g migration RenamePasswordColumnInUser 
```

以上命令会生成一个形如`db/migrate/20151105103638_rename_password_column_in_user.rb`的文件，编辑这个文件，修改成以下内容：

```rb
class RenamePasswordColumnInUser < ActiveRecord::Migration
  def change
    rename_column :users, :password, :password_digest
  end
end 
```

接着运行命令`rake db:migrate`：

```rb
 $ rake db:migrate                                                                                        
== 20151105103638 RenamePasswordColumnInUser: migrating =======================
-- rename_column(:users, :password, :password_digest)
   -> 0.0501s
== 20151105103638 RenamePasswordColumnInUser: migrated (0.0502s) ============== 
```

这样，字段`password`就修改成`password_digest`啦。

同时使用`has_secure_password`，需要安装`bcrypt` gem。编辑 `Gemfile`，添加一行以下内容:

```rb
gem 'bcrypt', '~> 3.1.7' 
```

然后执行以下命令安装该 gem：

```rb
$ bundle intall 
```

目前为止，`has_secure_password` 方法就可以使用了。

### 用户登录功能的实现

用户登录功能的实现依赖于`session`的使用，如果用户登录认证成功后，就将用户认证信息写入到`session`中，这样用户随后访问各个页面时，就会都被认为登录成功。同时用户退出登录后，将用户认证信息从`session`中删除，这样用户在再次访问其他页面时就会被人会已经退出。用户登录的基本原理就是如此。至于为什么用户在访问其他页面时，也会携带认证信息，是由于写入`session`的数据，会自动保存到浏览器的`cookie`中。

在上一节式样中，我们已经准备好使用 Rails 的`has_secure_password`了，首先在`app/models/user.rb`文件中的`User`模型中，增加`has_secure_password`方法，现在`app/models/user.rb`的内容为以下代码：

```rb
class User < ActiveRecord::Base
  validates :name, presence: true, length: {minimum: 2, maximum: 20}, uniqueness: true
  validates :email, presence: true, uniqueness: true, format: { :with => /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/ }
  validates :password, presence: true, length: {minimum: 6, maximum: 16}
  has_many :posts

  has_secure_password
end 
```

上面的代码相对于我们在之前实验修改的代码区别在于不仅仅增加了`has_secure_password`方法，还修改了对`email`字段的验证方法。

由于我们会在视图中判断用户是否已经登录，所以需要我们写一些帮助方法，这些帮助方法写在`app/helpers/users_helper.rb`文件中，以下就是我们编写的代码，代码的含义请看注释：

```rb
module UsersHelper
  # 一旦用户名和密码正确，则登录成功，同时在 session 中记录用户的认证信息
  # 这里我们只是简单的记录用户的 id
  def log_in(user)
    session[:user_id] = user.id
  end

  # 获取当前登录用户
  def current_user
    # 判断 session 是否有用户 ID
    if (user_id = session[:user_id])
      @current_user ||= User.find(user_id)
    end
  end

    # 用来判断用户是否登录
    def logged_in?
      !current_user.nil?
    end

    # 退出登录
    def log_out
      session.delete(:user_id)
      @current_user = nil
    end

    # before_action, 用于检测用户是否登录，未登录的话则跳转到登录界面
    def require_login
      if !logged_in?
        redirect_to login_path
        return
      end
    end

end 
```

上面的代码创建了一些列方法用于登录和登出用户，`log_in`方法用于登录用户，`log_out`用于登出用户，`logged_in?`方法用于在视图中判断用户是否已经登录，比如只有已登录用户才能看到`Profile`页面的链接按钮。同时我们在代码最后添加了一个`require_login`方法，该方法用于控制哪些只有登录用户才能执行的控制器方法行。同时我们修改了`UsersController`的代码，`app/helpers/users_helper.rb`修改后的代码如下：

```rb
class UsersController < ApplicationController
  # 使用 app/helpers/users_helper.rb 中方法
  include UsersHelper

  # 除了登录页面，其他页面都需要在登录后访问
  before_action :require_login, except: [:login]

  # 登录用户
  def login
    if logged_in?
      redirect_to admin_posts_path
      return
    end
    # 只有 POST 访问时，才对用户进行认证
    if request.post?
      name = params[:user][:name]
      password = params[:user][:password]
      user = User.find_by(name: name)
      # 用户可以使用用户名或者邮箱登录
      if !user
        user = User.find_by(email: name)
      end
      if user && user.authenticate(password)
        log_in(user)
        # 登录成功后，跳转到博文页面
        redirect_to admin_posts_path
        return
      else
        flash.now[:danger] = "Invalid name or password"
      end
    end
  end

  # 退出登录
  def logout
    log_out if logged_in?
    redirect_to root_path
  end

  def show
    @user = User.first
  end

  # 用于更新管理员信息
  def update
    @user = User.find(params[:user][:id])
    if @user.update(user_params)
      flash[:success] = "Your password is: " + params[:user][:password]
      redirect_to profile_path
    else
      # 如果更新失败，则显示相关错误信息
      render 'show'
    end
  end

  private
    def user_params
      params.require(:user).permit(:name, :email, :password)
    end
end 
```

代码中`login`动作用于显示登录页面和接收登录的参数，同时支持使用用户名和邮箱登录，其中`update`方法用于更新用户的信息。

在上一小节实验中可以看到`Manage`，`Profile` 页面未登录情况下也是能访问的，现在我们更改为只允许登录后才能访问。首先，更改`app/views/layouts/application.html.erb` 中显示菜单的部分代码为如下：

```rb
 <ul class="nav navbar-nav pull-right">
            <% if logged_in? %>
              <li><%= link_to "Manage", admin_posts_path  %></li>
              <li><%= link_to "Profile", profile_path  %></li>
              <li><%= link_to "Logout", logout_path %></li>
            <% else %>
              <li><%= link_to "Login", login_path %></li>
            <% end %>
          </ul> 
```

以上代码中使登录和未登录用户显示了不同的菜单。在模板中我们使用了`app/helpers/users_helper.rb`文件中 `logged_in?`方法，这是怎么做到的呢？因为在`app/helpers/application_helper.rb`有以下一行代码：

```rb
include UsersHelper 
```

这行代码，将`app/helpers/users_helper.rb`中定义的方法加入到了`ApplicationHelper`中，而`ApplicationHelper`默认就可以在视图文件中使用。

上述的内容只是做到了显示不同的菜单，显然我们还要限制控制器的相关方法只有登录用户才能访问。

在 `UsersController`也就是`app/controllers/users_controller.rb`文件中加入以下代码：

```rb
include UsersHelper
before_action :require_login, except: [:login] 
```

以上代码中，第一行`include UserHelper`将`app/helpers/users_helper.rb`定义的方法加入到`UsersController`中，第二行中，我们使用`before_action`方法指定了，除了执行`login`动作前不执行`require_login`方法外，其他动作执行前都需要执行`require_login`方法。也就是说，其他方法都要求用户登录。

类似的，我们需要分别在`PostsController`和`CommentsController`中加入相关代码，只不过`befreo_action`的第二个参数`except`的值有所不同。

经过以上步骤，我们的用户登录功能就完成了。该小节中所有相关代码位于本课程[Rails 基础入门代码库](http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103)项目的`chapter5/blog-v1`目录中。下面我们将`chapater5/blog-v1`中的博客项目运行起来。

```rb
$ cd ~/Code
$ git clone  http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103.git rails_base 
```

以上命令我们克隆了本课程的代码到`rails_base`目录，接着我们进入`~/Code/rails_base/chapter5/blog-v1`目录，开始安装博客项目依赖的 gem 包：

```rb
$ cd ~/Code/rails_base/chapter5/blog-v1
$ bundle install
$ rake db:migrate 
```

我们还需要创建一个管理员账户，可以通过`rails console`命令进行：

```rb
$ rails console
irb> User.create(name: "shiyanlou", email: "support@shiyanlou.com", password: "shiyanlou") 
```

上面的命令中，我们通过`User.create`方法创建了管理员用户，还记得我们之前讲过的`has_secure_password`方法吗？使用该方法后，用户数据模型将有一个虚拟的字段`password`，这里我们使用这个字段来指定用户的密码是`shiyanlou`。上面的命令执行效果如下图：

![此处输入图片的描述](img/efb7f5eab38cd116ab50f23c80fb0bde.jpg)

可以看到，我们指定的密码`shiyanlou`经过加密后存储在`password_digest`字段。到这里，我们可以启动博客项目了：

```rb
$ rails server 
```

然后访问博客项目首页试试看，未登录前，我们只能看到这样的菜单：

![此处输入图片的描述](img/b5d667f0491184ca6e0b2c9d0e1dc749.jpg)

点击`Login`，输入我们之前创建的用户名`shiyanlou`，以及密码`shiyanlou`后发现登录成功，这时候菜单如下图所示：

![此处输入图片的描述](img/fa2dd52b482a1aff7ac4c779efa9a3c4.jpg)

现在我们可以点击`Profile`页面，修改我们的个人信息了:

![此处输入图片的描述](img/dfc9848a4adc17894a6d42b6d7c11248.jpg)

当我们填写的数据被`User`数据模型中定义的验证方法验证失败时，会显示相关错误：

![此处输入图片的描述](img/f3d7f78948d36c1aa2d4c33a54a4f37c.jpg)

你还可以点击`Logout`退出，登录然后再次访问`http://localhost:3000/profile`页面，会发现，总是会重定向到`/login`页面，这是由于我们之前添加了类似`before_action :require_login, except: [:login]`这样的代码导致的。

本节实验中，我们实现了博客项目的各个视图以及用户登录功能。下一节实验中，我们将实现博文的发布和评论功能。