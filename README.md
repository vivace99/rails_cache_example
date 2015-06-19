<h1>캐쉬 사용법</h1>

<h3>Gemfile 에 아래 내용 추가</h3>
<pre>
gem 'actionpack-page_caching'
gem 'actionpack-action_caching'
</pre>

<h3>application_controller.rb 에 아래 내용 추가</h3>
<pre>
self.page_cache_directory = "#{Rails.root.to_s}/public/page_cache"
</pre>
컨트롤러별로 따로 경로 설정해도 좋을 듯

<h3>posts_controller.rb 에 아래 내용 추가</h3>
- 캐쉬할 페이지 설정
<pre>
	caches_page :show
</pre>
- 캐쉬 액션 설정(expire)
<pre>
	caches_action :show, #:cache_path => { :project => 1 },
		:expires_in => 1.minute
</pre>
- 캐쉬 액션 설정(request type)
<pre>
	caches_action :index, :if => proc do
		!request.format.json?  # cache if is not a JSON request
	end
</pre>
- expire 함수(원하는 위치에 삽입)
<pre>
	expire_page :action => :show
</pre>

- 예제로 추가한 부분
route.rb
<pre>
get '/posts/:id/expire' => 'posts#expire'
</pre>

<pre>
posts_controller.rb
  # GET /posts/1/expire
  def expire
    expire_page :action => :show
    respond_to do |format|
      format.all { render json: { :success => true } }
    end
  end
</pre>