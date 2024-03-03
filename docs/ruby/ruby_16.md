# 第 3 节 Rails 数据库模型

## 实验介绍

本实验是[实验楼](https://www.shiyanlou.com)课程[《Rails 基础入门》](https://www.shiyanlou.com/courses/103)的第三节实验。本节实验中，我们将学习使用 Rails 的数据库模型。

本节实验代码位于本课程[Rails 基础入门代码库](http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103)项目中`chapter3`目录，你可以克隆该项目到本地进行查看。

本课程基于[《Ruby on Rails 入门》](http://guides.ruby-china.org/)制作。本课程在其基础加入了大量说明，并针对实验楼环境进行了适当修改。希望通过学习本课程，你能熟悉 Rails 中的各种概念，并能使用 Rails 开发简单的项目。

## Active Record 数据库模型

Rails 中的数据库模型叫做 `Active Record`，也是 MVC 模型中的`M`部分。Active Record 负责创建和使用需要持久存入数据库中的数据。其实也可以认为 Active Record 是一种 ORM，也就对象映射关系系统，类似于 Python 中的 [SQLAlchemy](http://www.sqlalchemy.org/)，Golang 中的 [gorm](https://github.com/jinzhu/gorm)。

对象关系映射（ORM）是一种技术手段，把程序中的对象和关系型数据库中的数据表连接起来。使用 ORM，程序中对象的属性和对象之间的关系可以通过一种简单的方法从数据库获取，无需直接编写 SQL 语句，也不过度依赖特定的数据库种类。

Active Record 提供了许多功能，主要有以下几个：

*   表示模型和其中的数据
*   表示模型之间的关系
*   通过相关联的模型表示继承关系
*   持久存入数据库之前，验证模型
*   以面向对象的方式处理数据库操作

### Active Record 中的约定

我们在第一节实验中就强调了 Rails 框架中的`多约定，少配置`原则，在 Active Record 中同样适用。如果我们遵循 Rails 的约定，创建 Active Record 模型时就非常简单。

#### 命名约定

默认情况下， Active Record 使用一些命名约定，查找模型和数据库表之间的映射关系。Rails 把模型类名转换成复数，然后查找对应的数据表。例如，模型类名为 Book，数据表就是 books。Rails 提供的单复数变形功能很强大，常见和不常见的变形方式都能处理。如果类名由多个单词组成，应该按照 Ruby 的约定，使用驼峰式命名法，这时对应的数据表将使用下划线分隔各单词。因此：

*   数据表名：复数，下划线分隔单词（例如 book_clubs）
*   模型类名：单数，每个单词的首字母大写（例如 BookClub）

#### 模式约定

根据字段的作用不同，Active Record 对数据表中的字段命名也做了相应的约定：

*   外键 - 使用 singularized*table*name*id 形式命名，例如 item*id，order_id。创建模型关联后，Active Record 会查找这个字段；
*   主键 - 默认情况下，Active Record 使用整数字段 id 作为表的主键。使用 Active Record 迁移创建数据表时，会自动创建这个字段；

还有一些可选的字段，能为 Active Record 实例添加更多的功能：

*   created_at - 创建记录时，自动设为当前的时间戳；
*   updated_at - 更新记录时，自动设为当前的时间戳；
*   lock_version - 在模型中添加乐观锁定功能；
*   type - 让模型使用单表继承；
*   (table*name)*count - 缓存关联对象的数量。例如，posts 表中的 comments_count 字段，缓存每篇文章的评论数；

### 创建 `Active Record`模型

创建 ACtive Record 模型非常简单，只需要继承 `ActiveRecord::Base`类就可以了：

```rb
class Product < ActiveRecord::Base
end 
```

上面的代码会创建`Product`模型，对应于数据库中的`products`表。同时`products`表中的字段也会映射到`Product`模型实例的属性上。假如`products`表由下面的 SQL 语句创建：

```rb
CREATE TABLE products (
   id int(11) NOT NULL auto_increment,
   name varchar(255),
   PRIMARY KEY  (id)
); 
```

按照这样的数据表结构，可以编写出下面的代码：

```rb
p = Product.new
p.name = "Some Book"
puts p.name 
```

但是一般情况下，我们不用编写`Porduct`模型，我们可以通过模型生成器创建。 例如以下代码：

```rb
$ rails generate modle Product name:string 
```

将创建 `Product`模型，并生成相应的迁移文件， 其中`rails generate model`就是调用模型生成器。

### CRUD 操作数据

CURD 是四种数据操作的简称：C 表示创建，R 表示读取，U 表示更新，D 表示删除。Active Record 自动创建了处理数据表中数据的方法。

#### 创建

Active Record 对象可以用哈希创建，在块中创建，或者创建后手动设置属性。`new`方法创建一个新对象，`create`方法创建新对象，并将其存入数据库。

例如 `User`模型中有两个属性，`name`和`occupation`。调用`create`方法会创建一个新记录，并存入数据库：

```rb
user = User.create(name: "David", occupation: "Code Artist") 
```

使用`new`方法，可以实例化一个新对象，但不会保存：

```rb
user = User.new
user.name = "David"
user.occupation = "Code Artist" 
```

#### 查询

Active Record 为查询数据库也提供了丰富的 API，如下面代码所示：

```rb
# 查询所有用户
users = User.all

# 查询第一个用户
user = User.first

# 根据字段值查询
user = User.find_by(name: "David")

# 根据条件查询
user = User.where(name: 'David', occupation: 'Code Artist').order('created_at DESC') 
```

#### 更新

更新 Active Record 对象，可以先查询对象，然后修改属性后再存入数据库的方式实现：

```rb
user = User.find_by(name: 'David')
user.name = 'Dave'
user.save 
```

#### 删除

删除 Active Record 对象，通过`destroy`方法进行：

```rb
user = User.find_by(name: 'David')
user.destroy 
```

### 数据验证

数据验证能确保只有合法的数据才会存入数据库。例如，程序可能需要用户提供一个合法的 Email 地址和邮寄地址。在模型中做验证是最有保障的，只有通过验证的数据才能存入数据库。数据验证和使用的数据库种类无关，终端用户也无法跳过，而且容易测试和维护。在 Rails 中做数据验证很简单，Rails 内置了很多帮助方法，能满足常规的需求，而且还可以编写自定义的验证方法。这是项很重要的功能，比如你可能需要验证用户提交的数据，是否满足模型的约束，毕竟用户提交的任何数据都是不可信的。

把数据存入数据库之前进行验证是十分重要的步骤，所以调用 `create`、`save`、`update` 这三个方法时会做数据验证，验证失败时返回 false，此时不会对数据库做任何操作。这三个方法都有对应的严格方法（`create!`，`save!`，`update!`），爆炸方法要严格一些，如果验证失败，会抛出 `ActiveRecord::RecordInvalid` 异常。下面是个简单的例子：

```rb
class User < ActiveRecord::Base
  validates :name, presence: true
end 
```

在上面的代码中，我们定义了模型`User`，并且他有一个针对`name`字段的验证方法，具体约束条件是，`name`字段必须要要值。所以当执行`User.create` 方法时，会返回`false`，因为请我们没有对`name`字段赋值。同时执行`User.create！`会触发`ActiveRecord::RecordInvalid`异常。

Active Record 还提供了许多数据验证方法，更多内容参考：http://guides.rubyonrails.org/active*record*validations.html 。

### 迁移

当我们修改或者创建数据库模型的，这些改变怎么应用到数据库中去呢？比如我们创建了一个数据库模型，我可以通过 SQL 语句在数据库中直接创建相应的数据表，但是这样操作非常低效而且后期难以维护。好在 Rails 框架为我们提供了非常丰富的数据库迁移功能。所谓迁移，也就是说通过 Rails 的迁移 `DSL`（领域特定语言）来处理数据库模式，这些 DSL 记录在特定的文件中，通过 `rake` 命令调用，这样我们就不用手动编写 SQL 语句，就可以操作和修改数据库。同时通过`DSL`我们可以不用关心，所用的数据库纠结是类型（因为数据库类型不同可能实现相同功能的 SQL 语句不同）。

一般情况下，我们可以通过两种方式创建迁移， 一种是当我们创建数据库模型的时候 Rails 自动创建相关的迁移文件。另一种，是我们通过`rails generate migration` 命令手创建。

#### 随数据库模型一起创建的迁移

在使用模型生成器创建模型的时候会自动生成迁移，这些迁移中包含创建相应数据表的代码。如果在生成器中，指定了代码，还会生成相应的代码。例如，运行下面的命令：

```rb
$ rails generate model Product name:string description:text 
```

会生成下面的迁移：

```rb
class CreateProducts < ActiveRecord::Migration
  def change
    create_table :products do |t|
      t.string :name
      t.text :description

      t.timestamps
    end
  end
end 
```

以上的迁移代码会存储在一个迁移文件中，这个文件名由采用 UTC 时间戳后加以下划线分隔的迁移名组成，迁移文件存储在 `app_name/db/migrate`目录中

#### 单独创建迁移

除了随模型生成器一起生成迁移外，我们还可以手动生成迁移。通过迁移生成器`rails genrate migration`就可以做到。比如：

```rb
$ rails generate migration AddPartNumberToProDucts part_number:string 
```

在以上的命令中，我们创建了一个名为`AddPartNumberToProDucts`的迁移，在这个迁移中，我们将为`products`数据表，增加字段`part_number`，字段类型是`string`，生成的迁移文件内容如下：

```rb
class AddPartNumberToProducts < ActiveRecord::Migration
  def change
    add_column :products, :part_number, :string
  end
end 
```

由于篇幅有限，关于 Rails 的数据模型迁移内容就讲到这里，更多内容可以参考： http://guides.rubyonrails.org/active*record*migrations.html 。

## 博客项目的数据库模型设计

又到了实践环节啦。在上一节实验中，我们实现了博客项目的控制器和路由设计，在这一节中我们将设计实现博客项目的数据库模型。

在上一节实验中，我们知道博客项目将支持博文发布，评论的发布以及用户登录功能。所以很容易想到，我们需要三个数据库模型，分别是：

*   用户模型用于存储管理员信息，包含：名称，邮箱，密码等信息；
*   博文模型用于存储博客文章，应该包含： 标题，内容，作者 id 字段；
*   评论模型用于存储评论，应该包含：评论作者名字，命令内容；

从上面的分析中，可以看到一些关联关系：多篇博文关联到管理员，多项评论关联到某一篇博文。下面让我们在前一节博客项目代码的基础上开始创建模型。

### 博客项目模型定义

上一节实验实验的代码位于: http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103/src/master/chapter2/blog 目录中。我们可以通过以下命令克隆该项目：

```rb
$ cd ~/Code
$ git clone  http://git.shiyanlou.com/shiyanlou/shiyanlou_cs103.git rails_base 
```

以上命令我们克隆了本课程的代码到`rails_base`目录，接着我们进入`~/Code/rails_base/chapter2/blog`目录，开始安装博客项目依赖的 gem 包：

```rb
$ cd ~/Code/rails_base/chapter2/blog
$ bundle install
$ rake db:migrate 
```

目前为止，整个项目恢复回来了，我们开始创建模型，首先创建 `User` 模型：

```rb
$ rails g model User name:string email:string password:string 
```

下图是该命令的输出：

![此处输入图片的描述](img/ba2984a2eaf04ce6d9799dbe94b615c0.jpg)

可以看到`rails g model`模型生成器，不仅仅会生成数据模型，还会生成相应的迁移文件，也就是图中的`db/migrate/20151103072907_crate_users.rb`文件。

下面让我们创建博文模型：

```rb
$ rails generate model Post title:string  text:text user:references 
```

下图是输出结果：

![此处输入图片的描述](img/5511729d12f6714e53fb5d65fb80e688.jpg)

这里有点点区别，我们创建博文模型`Post`的时候指定了关联到`User`模型。也就是之前我们说的，管理员可有多篇博文。下面让我们看看，模型生成器自动创建的迁移文件`db/migrate/*_create_posts.rb`，其内容如下：

```rb
class CreatePosts < ActiveRecord::Migration
  def change
    create_table :posts do |t|
      t.string :title
      t.text :text
      t.references :user, index: true, foreign_key: true

      t.timestamps null: false
    end
  end
end 
```

上面的代码也就是之前我们讲解过的 Active Record 迁移代码，在代码中，将创建`title`, `text`, `user_id`字段，其中`user_id`是一个外键，关联到`User`模型，最后的`t.timestamps`方法将为我们的表自动创建`created_at`和`updated_at`两个字段，分别代表了记录的创建和更新时间。

下面让我们创建最后一个模型:

```rb
$ rails g model Comment author:string content:text post:references 
```

以上命令我们创建了`Comment`模型，该模型关联到`Post`模型。让我们看看 Rails 生成的模型定义，文件 `app/models/comment.rb` 其内容如下：

```rb
class Comment < ActiveRecord::Base
  belongs_to :post
end 
```

其中有一行代码`belongs_to :post`，当我们使用`rails g model`创建模型指定关联时会生成，其作用是用于建立 Active Record 关联。同样的如果查看`app/models/post.rb`文件，会看到这样一行代码：`belongs_to :user`。

目前为止我们创建了三个模型，并指定了相应的关联关系，但是我们发现关联关系都是单向的，也就是说我们将`Post`关联到了`User`，但是我们并没有在`User`模型的定义中关联到`Post`，下面让我们完成。

编辑 `app/models/user.rb`，将其修改成以下内容：

```rb
class User < ActiveRecord::Base
  has_many :posts
end 
```

同样的，我们需要将`has_many :comments`代码加入到`app/models/post.rb`文件中：

```rb
class Post < ActiveRecord::Base
  belongs_to :user
  has_many :comments
end 
```

### 博客项目模型的创建

之前我们已经定义好了博客项目的数据库模型。现在我们可以开始创建了模型了。创建模型，我们可以通过`rake db:migrate`命令。其中，[rake](http://rake.rubyforge.org/)命令相当于`make`命令，它根据`Rakefile`文件定义的各种操作可以完成各种任务。在 Rails 中，我们可以通过`rake db:migrate`命令完成，数据库的迁移任务。

在之前的模型定义中，产生了几个数据库迁移文件，也就是位于`db/migrate`目录下的文件，我们可以通过`rake db:migrate`命令执行这些文件，生成相应的数据库表， 下面开始执行：

```rb
$ rake db:migrate 
```

下图是该命令的输出:

![此处输入图片的描述](img/6545e9708bfaf44a3e223baa165ec5c5.jpg)

可以看到成功的创建了数据库表。我们可以通过`rails dbconsole`命令，进入数据库的 console 进行验证，由于我们使用的 sqlite3 数据库，所以验证过程执行的命令如下：

```rb
$ rails dbconsole
sqlite> .table
sqlite> .schema users
sqlite> .schema posts
sqlite> .schema comments 
```

以上命令的结果如下图：

![此处输入图片的描述](img/32f626e441adf4710276f056cb1fe6c2.jpg)

### 博客项目数据库模型的数据验证

当我们将数据存入到以上定义的数据库模型`User`, `Post`, `Comment`的时候，我们肯定希望能对数据做一些验证，只有满足一定约束的数据才能存入到数据库中。我们设计三个模型需要满足的约束如下所示：

*   `User.name`: 长度大于 2 且不小于 20， 且唯一
*   `User.email`: 合法的 email 地址，且唯一
*   `Post.title`: 不能为空，长度小于 50
*   `Post.text`: 长度大于 50 且小于 20000
*   `Comment.author`: 长度大于 1 且小于 20
*   `Comment.content`: 长度大于 5 且小于 500

按照以上需求，我们需要分别修改三个模型的定义代码，分别添加数据验证约束。添加约束后的内容分别如下：

*   `app/model/user.rb`

```rb
class User < ActiveRecord::Base
  validates :name, presence: true, length: {minimum: 2, maximum: 20}, uniqueness: true
  validates :email, presence: true, email: true, uniqueness: true
  validates :password, presence: true, length: {minimum: 6, maximum: 16}
  has_many :posts
end 
```

*   `app/model/post.rb`

```rb
class Post < ActiveRecord::Base
  belongs_to :user
  has_many :comments
  validates :title, presence: true, length: {maximum: 20}, uniqueness: true
  validates :text, presence: true, length: {minmum: 50, maximum: 20000}
end 
```

*   `app/model/comment.rb`

```rb
class Comment < ActiveRecord::Base
  belongs_to :post
  validates :author, presence: true, length: {minmum: 1, maximum: 20}
  validates :content, length: {minmum: 5, maximum: 500}
end 
```

现在数据验证就添加完了，我们可以通过`rails console`命令进入 irb 中验证。`rails console`会将我们博客项目的代码加载到 irb 环境中，这样我们就可以直接使用定义的模型了。

```rb
$ rails console
irb> user = User.new(name: 'q')
irb> user.valid?
irb> user.errors.messages 
```

以上命令我们分别创建了一个`name`字段为`q`的`User`模型对象，显然该对象不满足我们的数据验证约束。接着我们通过`user.valid?`方法查看是否满足约束，最后还通过`user.errors.messages`查看验证失败的错误信息。以上命令的输出结果如下图所示。

![此处输入图片的描述](img/d3dcb73e7846837c6beadc3ac94919fc.jpg)

到目前为止，本节的实验内容就结束了。这一节中，我们学习了 Rails 中的数据模型 `Active Record`。并在最后将这节实验学习到的知识应用到我们的博客项目中。下一节中，我们将学习 Rails 中的视图。