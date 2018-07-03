### Rails ajax 구현

#### 글 생성시 ajax 구현

1. data-remote = true (html 태그 기준)

   ```javascript
   <form action="/posts/<%= @post.id%>/comments" method="post" data-remote = "true">
     <input type="text" name="content" /><br />
     <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">
     <input type="submit" value="댓글 쓰기" />
   </form>
   <hr />
   ```

2. CommentsController => create action

   ```ruby
     def create
       @comment = Post.find(params[:post_id]).comments.new(comment_params)
       @comment.user_id = current_user.id
       if @comment.save
         respond_to do |format|# 요청에 따라 html.erb 혹은 js erb파일을 리턴
           format.html {redirect_to :back}
           format.js {render 'create_temp'}
         end
       else
         redirect_to :back
       end
     end
   ```

3. create.js.erb 작성

   ```javascript
   // comments controller에서 format.js의 render를 받아주기 위해 만든 페이지.
   // 뒤에 render를 따로 정해주지 않으면 저절로 그 class(def)명과 같은 형식으로 만들어짐
   // format.js 이므로 create.js.erb로 만들어 준 것임.
   
   $('div#comments').append(`<p> <%= escape_javascript(@comment.content) %> <%= escape_javascript(link_to '삭제하기', destroy_comment_path(escape_javascript(@comment.id)), remote: true, class: 'delete_comment', method: :delete, data: {confirm: '댓글을 지우시겠습니까?'}) %> </p><hr />`)
   
   // escape_javascript == j
   // 'escape_javascript' prevents from double_quoter problem in console
   
   ```

4. `show.html.erb`파일에 ajax 요청에 따른 event handler 작성

   ```javascript
   <script>
     $('form').on('ajax:success', function(){
       $('input[name="content"]').val('');
     });
   </script>
   ```

#### 글 삭제시 ajax 구현

1. remote: ture (rails 코드 기준)

   ```erb
   <%= link_to '삭제하기', destroy_comment_path(comment.id), remote: true, method: :delete, class:"delete_comment", data: {confirm: '댓글을 지우시겠습니까?'}%>
   ```

2. CommentsController => destroy action

   ```ruby
     def destroy
       @comment = Comment.find(params[:comment_id])
       @comment.destroy
       respond_to do |format|
         format.html {redirect_to :back} # html type이면 back
         format.js {} # js type이면 {} => destroy.js.erb
       end
     end
   ```

3. destroy.js.erb 작성

   ```javascript
   var parent = $('a[href="/comments/<%= params[:comment_id"] %>]').parent(); //<p></p>
   var hr = parent.next();
   parent.remove();
   hr.remove();
   
   ```

#### jQuery로 ajax 구현

##### 글 생성시 ajax 구현

1. script 생성

   ```javascript
   <script>
      $('input[type="submit"]').on('click', function(e) {
       e.preventDefault();
       alert('start!')
       $.ajax({
         url: $('form').attr('action'),
         type: "POST" ,
         data: {content: $('input[name="content"]').val(),
               authenticity_token: $('[name="csrf-token"]').attr('content')},
         dataType: "script",
         success: function(){
           alert('success!')
           $('input[name="content"]').val('');
         },
         error: function(){
           alert('fail!');
         }
       });
     });
   </script>
   ```

2. 이후 rails의 ajax 구현순서와 동일

##### 글 삭제시 ajax 구현

1. script 생성

   ```javascript
   <script>
       $('.delete_comment').on('click', function(e){
       e.preventDefault();
       alert("start_delete")
       $.ajax({
         url: this.href //<a></a>,
         type: "DELETE",
         data: {authenticity_token: $('[name="csrf-token"]').attr('content')},
         dataType: "script",
         success: function(){
           alert("delete_complete!")
         },
         error: function(){
           alert("delete_error!")
         }
       })
     })
   </script>
   ```

2. 이후 rails의 ajax 구현순서와 동일