## 20180611_sinatra_pt.2

### 게시판 만들기

#### 준비사항

* 필수 gem 설치

```ruby
$ gem install sinatra
```

```ruby
$ gem install sinatra-reloader
```

* 시작 페이지 만들기(routing설정 및  대응되는 view설정)

  ```ruby
  #app.rb
  require 'sinatra'
  require 'sinatra/reloader'
  
  get '/' do # routing 설정
      send_file 'index.html' # 해당하는 view 설정
  end
  
  get '/lunch' do # routing 설정
      menu = ["짜장면", "짬뽕", "김밥"]
      @lunch = menu.sample # 해당하는 view 설정
      
      erb :lunch
  end
  ```

#### layout

```ruby
<html>
    <head>
    </head>
    <body>
    	<%= yield %>
	</body>
</html>
```

```ruby
def hello
    puts "hello"
    yield
    puts "bye"
end
# {} : block (코드 덩어리)
hello {puts "KIM"}
# => hello KIM bye
```



#### erb에서 루비 코드 활용하기

```ruby
#app.rb
get '/lunch' do
    @lunch =["바스버거"]
    erb = lunch
end
```

```erb
<!--lunch.erb-->
<%= @lunch %>
```

#### params

1. variable routing (url을 통해 받는 법)

   ```ruby
   #app.rb
   get '/hello/:name' do
   	@name = params[:name]
   	erb :name
   end
   ```

2. `form` tag를 통해서 받는 법

   ```ruby
   <form action ="/posts/create">
       제목 : <input name="title">
   </form>
   ```

   ```ruby
   # app.rb
   # params는
   # {title: "asdf"}로 넘어오고 있음
   get '/posts/create' do
       @title = params[:title]
   end
   ```

   

#### ORM: object relation mapper

* 객체지향언어(ruby)와 데이터베이스(sqlite)를 연결하는 것을 도와주는 도구

##### datamapper sinatra

* table을 객체지향 언어에서 더 쓰기 편하도록 도와주는(mapping 해주는)

* http://recipes.sinatrarb.com/p/models/data_mapper

* datamapper 사용

  ###### 사전준비

  ```ruby
  $ gem install datamapper
  $ gem install dm-sqlite-adapter #data mapper와 sqlite를 연결시켜주는 adapter
  ```

  ```ruby
  gem 'json', '~>1.6' # c9에서 json 라이브러리 충돌로 인해 버전을 고정시키는 코드
  require 'data_mapper' 
  
  # blog.db 세팅
  DataMapper::setup(:default, "sqlite3://#{Dir.pwd}/blog.db")
  # Post (Object)객체 생성
  
  class Post
    include DataMapper::Resource
    property :id, Serial #id와 created_at은 저절로 생성됨
    property :title, String
    property :body, Text
    property :created_at, DateTime
  end
  
  # Perform basic sanity checks and initialize all relationships
  # Call this when you've defined all your models
  DataMapper.finalize
  
  # Post table 생성 (ORM중 Relation에 해당)
  Post.auto_upgrade!
  ```

###### 데이터 조작

* 기본

* C(Create)

  ```ruby
  #1
  Post.create(title: "test", body: "test")
  #2
  p = Post.new
  p.title = "test"
  p.body = "test"
  p.save # p.save를 통해 실제로 db에 저장
  ```

 * R(Read)

   ```ruby
   Post.get(1) # get(id)
   ```

* U(Update)

  ```ruby
  #1
  Post.get(1).update(title: "test", body: "test")
  #2
  p = Post.get(1)
  p.title = "test"
  p.body = "test"
  p.save # p.save를 통해 실제로 db에 저장
  ```

* D(Destroy)

  ```ruby
  Post.get(1).destroy
  ```

  

#### CRUD 만들기

###### Create :action이 2개 필요

```ruby
# 사용자에게 입력받는 창
get '/posts/new' do
end
# 실제로 db에 저장
get 'posts/create' do
    Post.create(title: params[:title], body: params[:body])
end
```

###### Read

```ruby
#app.rb
get '/posts/:id' do
    Post.get(params[:id])
end
```

###### Destroy

```ruby
#app.rb
get '/posts/destroy' do
     Post.get(params[:id]).destroy
end

```

##### bundler를 통한 gem 관리

> 어플리케이션의 의존성(dependency)을 관리해줌

1. bundler 설치

   ```ruby
   $ gem install bundler
   ```

2. `gemfile`작성: 루트 디렉토리에 만들기

   ```ruby
   source 'https://rubygems.org'
   gem 'sinatra'
   gem 'sinatra-reloader'
   gem 'datamapper'
   ```

3. `gem` 설치

   ```ruby
   $ bundle install
   ```

   