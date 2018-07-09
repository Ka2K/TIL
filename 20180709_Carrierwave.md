## Carrierwave

> 파일 혹은 이미지 업로드의 경우 클라이언트로 부터 `<input type="file">`을 통해 받을 수 있다.
>
> 서버에서 파일을 저장 및 관리하기 위해서는 `carrierwave`가 필요하다.

>내 서버에 직접 파일들을 저장할 수 있지만, `fog-aws`를 통해서 AWS의 S3에 저장을 할 수 도 있다

1. Gem 설치

   ```ruby
   gem 'carrierwave'
   ```

2. uploader 설정 파일 생성

   ```
   $ bundle install
   $ rails g uploader Img
   ```

3. `app/uploaders/Img_uploader.rb`

   ```ruby
   class ImgUploader < CarrierWave::Uploader::Base
     # Include RMagick or MiniMagick support:
     # include CarrierWave::RMagick
     include CarrierWave::MiniMagick
   
     # Choose what kind of storage to use for this uploader:
     storage :fog
     # storage :fog
   
     # Override the directory where uploaded files will be stored.
     # This is a sensible default for uploaders that are meant to be mounted:
     def store_dir
       "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
     end
   
     # Provide a default URL as a default if there hasn't been a file uploaded:
     # def default_url(*args)
     #   # For Rails 3.1+ asset pipeline compatibility:
     #   # ActionController::Base.helpers.asset_path("fallback/" + [version_name, "default.png"].compact.join('_'))
     #
     #   "/images/fallback/" + [version_name, "default.png"].compact.join('_')
     # end
   
     # Process files as they are uploaded:
     # process scale: [200, 300]
     #
     # def scale(width, height)
     #   # do something
     # end
   
     # Create different versions of your uploaded files:
     version :thumb do
       process resize_to_fit: [250, 250]
     end
     # 큰 길이를 기준으로 비율 맞춰 줄임. 예) 1000,800 => 250,200
     version :thumb do
       process resize_to_fill: [250, 250]
     end
     # 크기에 맞춰 그냥 잘라버림. 예) 1000,800 => 250, 250
   
     # Add a white list of extensions which are allowed to be uploaded.
     # For images you might use something like this:
     # 이런 확장자명만 받겠다. %w는 배열 만들어 주는 것.
     def extension_whitelist
       %w(jpg jpeg gif png)
     end
   
     # Override the filename of the uploaded files:
     # Avoid using model.id or version_name here, see uploader/store.rb for details.
     # def filename
     #   "something.jpg" if original_filename
     # end
   end
   
   ```

4. 이미지 저장을 할 column 추가

   ```
   $ rails g migration AddImgToPosts img:string
   $ rake db:migrate
   ```

5. `model` 설정

   ```ruby
   # app/models/post.rb
   mount_uploader :img, ImgUploader
   ```

6. `input` 설정

   ```ruby
   # app/views/posts/_form.html.erb
   <%= simple_form_for @post, html: {multipart: true} do |f| %>
   <!--multipart: true를 해주는 순간 file로 인식하고 file로 넘어감-->
     ..
     <%= f.input :img %>
     ..
   <% end %>
   ```

7. `controller` 설정

   ```ruby
   # app/controllers/post_controller.rb
   ..
       def post_params
       	params.require(:post).permit(:title, :contents, :img) # img 추가
       end
   ```

8. `public/uploads/...` 파일이 업로드 됨!



## 이미지 버전 만들기

> 실제로 이미지 활용에 따라 각기 다른 버전의 이미지를 잘라서 저장할 수 있다. 
>
> 이 때 `mini_magick`이라는 gem이 사용되며 이를 활용하기 위해서는 반드시 image magick을 설치해야 함.

0. OS별 설치 방법

   ```
   # ubuntu
   $ sudo apt-get update
   $ sudo apt-get install -y imagemagick
   
   # MacOS
   $ brew install imagemagick
   ```

1. Gemfile

   ```
   gem 'mini_magick'
   ```

2. `app/uploaders/img_uploader.rb`

   ```ruby
   ..
   include CarrierWave::MiniMagick
   ..
   version :thumb do
     process resize_to_fit: [250, 250]
   end
   # 큰 길이를 기준으로 비율 맞춰 줄임. 예) 1000,800 => 250,200
   version :thumb do
     process resize_to_fill: [250, 250]
   end
   # 크기에 맞춰 그냥 잘라버림. 예) 1000,800 => 250, 250    
   ```

3. 활용

   ```erb
   <%= image_tag @post.img.thumb_fill.url %>
   <%= image_tag @post.img.thumb_fit.url %>
   ```

## AWS S3에 업로드 하기

1. Aws 가입 및 IAM 설정 하기

2. S3 bucket 만들기

3. **`figaro` 설정!!**

   ```
   gem 'figaro'
   ```

   ```
   $ bundle install
   $ bundle exec figaro install
   ```

   ```ruby
   # app/config/application.yml
    AWS_ACCESS_KEY_ID: asdfasdfs
    AWS_SECRET_ACCESS_KEY_ID: asdfasdfs
    AWS_REGION: ap-northeast-2
    S3_BUCKET_NAME: fake-insta-kaka
   
   ```

4. Gemfile

   ```
   gem 'fog-aws'
   ```

5. `config/initializers/fog-aws.rb`생성

   ```ruby
   require 'fog'
   CarrierWave.configure do |config|
     config.fog_provider = 'fog/aws'                   
       # required
     config.fog_credentials = {
       provider:              'AWS',                     
       # required
       aws_access_key_id:     ENV["AWS_ACCESS_KEY_ID"],                      
       # required unless using use_iam_profile
       aws_secret_access_key: ENV["AWS_SECRET_ACCESS_KEY_ID"],         
       # required unless using use_iam_profile
       use_iam_profile:       true,                       
       # optional, defaults to false
       region:                ENV["AWS_REGION"],                
       # optional, defaults to 'us-east-1'
       # host:                  's3.example.com',             # optional, defaults to nil
       # endpoint:              'https://s3.example.com:8080' # optional, defaults to nil
     }
     config.fog_directory  = ENV["S3_BUCKET_NAME"]                        
     # required
     # config.fog_public     = false                      
     # optional, defaults to true
     # config.fog_attributes = { cache_control: "public, max-age=#{365.days.to_i}" } # optional, defaults to {}
   end
   
   ```

6. `app/uploaders/img_uploader.rb`

   ```ruby
   ..
   storage :file => storage :fog
   ..
   ```

7. S3 버킷 확인!