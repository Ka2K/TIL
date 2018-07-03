## day2 

- c9 프로젝트 생성

  - https://github.com/sinatra/sinatra

  - `app.rb`

  - ```ruby
    require 'sinatra'
    
    get '/' do
      'Hello world!'
    end
    ```

  - sinatra install

  - ```shell
    gem install sinatra
    ```

- run app

  - ```shell
    ruby app.rb -o $IP
    # $IP : 0.0.0.0
    # 0.0.0.0 : "나는 어떤 IP 주소로 서비스할 지 모르니 아무 IP 대역 다 서비스 해 줘."
    
    ruby app.rb -o $IP -p $PORT
    # 서버 구동
    ```

- html 태그를 포함시킬 수 있습니다.

  - ```ruby
    get '/htmltag' do
        "<h1> 태그가 들어갑니다. </h1>
        <ul>
            <li>1</li>
            <li>22</li>
            <li>333</li>
        </ul>
        "
    end
    ```

- welcome

  - ```ruby
    get '/welcome/:name' do
        "Welcome ! #{params[:name]}"
    end
    ```

- cube

  - ```ruby
    get '/cube/:num' do
        input = params[:num].to_i
        result = input ** 3
        "The result is #{result}"
    end
    ```

- html 파일 보내기

  - `app.rb` 

  - ```ruby
    get '/htmlfile' do
        send_file 'views/htmlfile.html'
    end
    ```

  - `mkdir views && touch views/htmlfile.html`

  - ```html
    <h1> html 파일을 보낼 수 있습니다. </h1>
    <ul>
        <li>1</li>
        <li>22</li>
        <li>333</li>
    </ul>
    ```

- erb 랜더링

  - ` app.rb`

  - ```ruby
    	get '/erbfile' do
        @abc = "변수를 넘겨줄 수 있습니다."
        abc = "이 변수는 넘겨줄 수 없습니다."
        erb :erbrile
    end
    ```

  - `touch views/erbfile.erb`

  - ```html
    <h1><%= @abc %></h1>
    ```

- sinatra/reloader

  - http://sinatrarb.com/contrib/reloader
  - `gem install sinatra-contrib` 

- lunch-array

  - ```ruby
    get '/lunch-array' do
        menu = ["짜장면", "볶음밥", "김밥"]
        @result = menu.sample
        erb :luncharray
    end
    ```

  - `touch views/luncharray.erb`

  - ```html
    <h1>오늘의 추천 메뉴는 <%= @result %> 입니다!!</h1>
    ```

- lunch-hash

  - ```ruby
    get '/lunch-hash' do
        menu = ["짜장면", "볶음밥", "김밥"]
        photo = {
            "짜장면" => "url",
            "볶음밥" => "url",
            "김밥" => "url"
        }
        @pick = menu.sample
        @photo = photo[@pick]
        erb :lunchhash
    end
    ```

  - `touch views/lunchhash.erb`

  - ```html
    <h1><%= @pick %></h1>
    <img src="<%= @photo %>"></img>
    ```

- lotto-sample

  - ```ruby
    get '/lotto-sample' do
        @lotto = (1..46).to_a.sample(6).sort
        erb :lottosample
    end
    ```

  - `touch views/lottosample.erb`

  - ```ruby
    <h1>행운의 번호</h1>
    <h1><%= @lotto %></h1>
    ```

- lotto-result

  - `gem install rest-client`

    - 잼파일 설치후에는 서버를 껐다가 켜야 합니다.

  - ```ruby
    require 'rest-client'
    require 'json' # 기본
    
    get '/lotto-result' do
        url = "http://www.nlotto.co.kr/common.do?method=getLottoNumber&drwNo=809"
        lotto = RestClient.get(url)
        lottoHash = JSON.parse(lotto)
        
        # 이번 주 추첨 번호를 저장할 drw_numbers 배열 생성.
        @drw_numbers = []
        
        # 보너스 번호를 제외한 번호 6개를 drw_numbers 배열에 저장.
        lottoHash.each do |k, v|    # lottoHash 해시의 모든 key-value 를 돌며,
            if k.include?('drwtNo')    # key 에 'drwtNo' 라는 문자열이 있으면(추첨 번호면) 
                @drw_numbers << v          # 그 value(번호) 를 drw_num 에 저장. 
            end
        end
        
        @drw_numbers.sort!    #drw_numbers 배열을 오름차순 정렬.
        
        # 보너스 번호를 bonus_number 에 저장.
        @bonus_number = lottoHash["bnusNo"]
        
        # 내가 추첨한 로또 번호 6개를 오름차순 정렬하여 my_numbers 배열에 저장.
        @my_numbers = [*1..45].sample(6).sort
        
        # drw_numbers 과 my_numbers 중 겹치는 숫자들(배열)을 match_numbers 에 저장.
        @match_numbers = @my_numbers & @drw_numbers
        
        # match_numbers 의 갯수를 match_cnt(count) 에 저장.
        match_cnt = @match_numbers.count    
        
        # 이번주 번호와 내가 추첨한 번호 비교, 분석하기. (if/else 구문)
        if(match_cnt == 6)
            @result = '1등'
        elsif((match_cnt == 5) && @my_numbers.include?(@bonus_number))
            @result = '2등'
        elsif match_cnt == 5 
            @result = '3등'
        elsif match_cnt == 4
            @result = '4등'
        elsif match_cnt == 3
            @result =  '5등'
        else
            @result = '꽝'
        end
        erb :lottoresult
    end
    ```

    ```ruby
    if match_cnt == 6 then @result = '1등'
    elsif match_cnt == 5 && my_numbers.include?(bonus_number) then result = '2등'
    elsif match_cnt == 5 then @result = '3등'
    elsif match_cnt == 4 then @result = '4등'
    elsif match_cnt == 3 then @result =  '5등'
    else @result = '꽝'
    end
    ```

    ```ruby
    @result = 
      case [match_cnt, my_numbers.include?(bonus_number)]
      when [6, false]        then "1등"
      when [5, true]         then "2등"
      when [5, false]        then '3등'
      when [4, true||false]  then '4등'
      when [3, true||false]  then '5등'
      else '꽝'
      end
    ```

  - `touch views/lottoresult.erb`

  - ```html
    <h1>1등 번호는 <%= @drw_numbers %> 보너스 번호는 <%= @bonus_number %></h1>
    <h1>나의 번호는 <%= @my_numbers %></h1>
    <h1>겹치는 번호는 <%= @match_numbers %></h1>
    <h1>결과는 <%= @result %></h1>
    ```

- layout

  - `touch views/layout.erb`

  - views 폴더안의 layout.erb 파일을 자동으로 인식한다.

    ```html
    <h1>MY PAGE</h1>
    <a href="/lunch-array">점심추천</a>
    <a href="/lotto-sample">로또추천</a>
    <a href="/lotto-result">로또결과</a>
    <%= yield %>
    ```

- op.gg 검색 폼

  - ```ruby
    get '/lol' do
        erb :lol
    end
    ```

  - `touch views/lol.erb`

  - ```html
    <h1>LOL 전적 검색</h1>
    <form action="/search">
        아이디 : <input type="text" name="userName">
        <input type="submit" value="검색">
    </form>
    ```

- op.gg 검색

  - `gem install httparty nokogiri`

  - ```ruby
    require 'httparty'
    require 'nokogiri'
    require 'uri'
    require 'date'
    require 'csv'
    
    get '/search' do
        url = "http://www.op.gg/summoner/userName="
        @id = params[:userName]
        keyword = URI.encode(@id)
        res = HTTParty.get(url + keyword)
        text = Nokogiri::HTML(res.body)
        @win = text.css('#SummonerLayoutContent > div.tabItem.Content.SummonerLayoutContent.summonerLayout-summary > div.SideContent > div.TierBox.Box > div.SummonerRatingMedium > div.TierRankInfo > div.TierInfo > span.WinLose > span.wins')
        @lose = text.css('#SummonerLayoutContent > div.tabItem.Content.SummonerLayoutContent.summonerLayout-summary > div.SideContent > div.TierBox.Box > div.SummonerRatingMedium > div.TierRankInfo > div.TierInfo > span.WinLose > span.losses')
        
        #File.open("log.txt", 'a+') do |f|
        #    f.write("#{@id}, #{@win.text}, #{@lose.text}, " + Time.now.to_s + "\n")
        #end
        
        CSV.open('log.csv', 'a+') do |csv|
            csv << [@id, @win.text, @lose.text, Time.now.to_s]
        end
        
        erb :search
    end
    ```

- 검색 로그

  - ```ruby
    get '/log' do
        @log = []
        CSV.foreach('log.csv') do |row|
            @log << row
        end
        erb :log
    end    
    ```

  - `touch views/log.erb`

  - ```html
    <h1>LOL 전적 검색 로그</h1>
    <table>
      <tr>
        <th>아이디</th>
        <th>승</th> 
        <th>패</th>
        <th>날짜</th>
      </tr>
      <% @log.each do |row| %>
      <tr>
        <% row.each do |elem| %>
        <td><%= elem %></td>
        <% end %>
      </tr>
      <% end %>
    </table>
    ```

    

