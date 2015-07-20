---
layout: post
title:  第六天
date:   2015-07-19
excerpt: 图片上传，登陆验证
---

经过几天的开发，我们的商城已经具备了核心的功能：商品管理（增删改查）、商品验证、购物车、订单生成。
当我们使用了一段时间后，发现商品新建或编辑时，商品图片的操作太麻烦了。还有一个严重的安全问题，任何人只要输入网址，就能更改商品，这让人寝食难安。
现在，我们来做两个功能以让我们吃口放心的饭。

**图片上传** 和 **登陆验证**

### 图片上传

1、添加 gem ，修改项目目录下的 Gemfile 文件

```
source 'https://ruby.taobao.org/'
```

```
gem 'mini_magick'
gem 'carrierwave'
```

```bash
bundle install
```

2、生成上传功能的文件

```bash
rails g uploader Image
```

3、修改上传文件(app/uploaders/image_uploader.rb)，使之符合我们商城的需求。

```
# encoding: utf-8

class ImageUploader < CarrierWave::Uploader::Base

  include CarrierWave::MiniMagick

  storage :file

  def store_dir
    time = Time.now
    "uploads/#{model.class.to_s.underscore}/#{time.year}/#{time.month}"
  end

  def extension_white_list
    %w(jpg jpeg gif png)
  end

  def filename
    if original_filename
      time = Time.now
      "img_#{time.to_i}.#{file.extension}"
    end
  end
end
```

4、修改商品模型(app/models/product.rb)，使之可以上传图片

```
mount_uploader :image, ImageUploader
```


5、修改页面(app/views/products/_form.html.erb 和 app/views/products/show.html.erb)

```
<%= image_tag(@product.image.url) %><br>
<%= f.file_field :image %>
```

```
<%= image_tag(@product.image.url) %><br>
```

现在，我们就可以再添加、编辑商品时，可以上传商品图片了。接下来我们就要解决安全问题了。

### 登陆验证

我们不想任何人都能修改我们的商品，只希望有权限的人才能修改，并且需要提供账号、密码。

1、使用脚手架(scaffold)添加 Seller ，作为管理人员，并且需要他登录之后才能修改商品。

```bash
rails g scaffold seller name:string password_hash:string salt:string
```
```bash
rake db:migrate
```

2、我们如果把密码明文存储，也可能遇到安全隐患，所以，不能明文存储，需要加密。
所以我们的字段叫做 `password_hash` ，并且添加一个随机生成6位字符串的字段 `salt`，将我们输入的密码加上随机字符串共同加密。

`password_hash` 作为密文字段存储，可是我们需要输入我们可以记住的密码，但是输入的这个密码我们不需要存储在数据库，这里我们可以添加一个虚拟属性 password 就可以了。

```ruby
class Seller < ActiveRecord::Base
  attr_accessor :password
  validates :name, presence: true, uniqueness: true, length: 6..20
  validates :password, presence: true, length: 6..20, confirmation: true, on: :create

  CODE_ARRAY = ('a'..'z').to_a

  before_save :encrypt_password

  def encrypt(code1, code2)
    Digest::SHA1.hexdigest("#{code1}-jingxi-#{code2}")
  end

  private

  def encrypt_password
    return if self.password.blank?
    self.salt = Seller::CODE_ARRAY.sample(6).join('')
    self.password_hash = self.encrypt(self.password, self.salt)
  end
end
```

修改表单页面(app/views/sellers/_form.html.erb)

```
<div class="field">
  <%= f.label :password %><br>
  <%= f.password_field :password %>
</div>
<div class="field">
  <%= f.label :password_confirmation %><br>
  <%= f.password_field :password_confirmation %>
</div>
```

修改控制器(app/controllers/sellers_controller.rb)

```ruby
params.require(:seller).permit(:name, :password, :password_confirmation)
```

3、验证

管理人员现在可以添加了，并且是以密文密码的形式。现在，我们来做登陆验证。

创建登陆控制器和登陆方法

```
rails g controller admins login
```

此命令会创建一个 app/controllers/admins_controller.rb 控制器文件和一个 app/views/admins/login.html.erb 模板文件。我们将他们进行修改。

首先修改模板文件

```
<%= notice %>
<%= form_tag(logined_path) do %>
  <div>
    <label>账号：</label>
    <input type="text" name="seller[name]" />
  </div>
  <div>
    <label>密码：</label>
    <input type="password" name="seller[password]" />
  </div>
  <div>
    <input type="submit" value="登陆" />
  </div>
<% end %>
```

修改路由文件（config/routes.rb），添加如下代码：

```
post 'admins/logined', as: 'logined'
```

修改控制器文件（app/controllers/admins_controller.rb）

```
def logined
  seller = Seller.auth(params[:seller][:name], params[:seller][:password])
  if seller
    session[:seller_id] = seller.id

    redirect_to products_path
  else
    flash[:notice] = 'password wrong!'
    render action: :login
  end
end
```

修改 seller 模型文件（app/models/seller.rb）

```
  def self.auth(name, password)
    seller = Seller.where(name: name).first
    !seller.nil? && password == seller.encrypt(password, seller.salt) ? seller : nil
  end
```

4、访问控制

修改 app/controllers/application_controller.rb

```
  def seller_auth
    if session[:seller_id]
      @seller = Seller.find(session[:seller_id])
    else
      flash[:notice] = '请登陆'
      redirect_to 'admins/login'
    end
  end
```

修改 app/controllers/products_controller.rb

```
  before_action :seller_auth, only: [:index, :new, :craete, :edit, :update, :destroy]
```

5、退出登陆

修改 app/controllers/admins_controller.rb

```
def logout
  session[:seller_id] = nil
  redirect_to 'admins/login'
end
```

修改路由文件

```
 get 'admins/logout'
```