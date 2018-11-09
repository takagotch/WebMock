### WebMock
---
https://github.com/bblimke/webmock

```
gem install webmock
git clone http://github.com/bblimke/webmock.git
cd webmock
rake install

```

```ruby
# test/test_helper.rb
require 'webmock/test_unit'
# spec/spec_helper
require 'webmock/rspec'
require 'webmock/minitest'
# features/support/webmock.rb
require 'webmock/cucumber'

require 'webmock'
include WebMock::API
WebMock.enable!

stub_request(:any, "www.example.com")
Net::HTTP.get("www.example.com", "/")

stub_request(:post, "www.example.com").
  with(body: "abc", headers: { 'Content-Length' => 3 })
uri = URI.parse("http://www.example.com")
req = Net::HTTP::Post.new(uri.path)
req['Content-Length'] = 3
res = Net::HTTP.start(uri.host, uri.port) do |http|
  http.request(req, "abc")
end

stub_request(:post, "www.example.com").
  with(body: /world$/, headers: {"Content-Type" => /image\/.+/}).
  to_return(body: "abc")
uri = URI.parse('http://www.example.com/')
req = Net::HTTP::Post.new(uri.path)
req['Content-Type'] = 'image/png'
res = Net::HTTP.start(uri.host, uri.port) do |http|
  http.request(req, 'hello world')
end

stub_request(:post, "www.example.com").
  with(body: {a: '1', b: 'five'})
RestClient.post('www.example.com', "data[a]=1&data[]=five",
  content_type: 'application/x-www-form-urlencoded')
RestClient.post('www.example.com', '{"data":{"a":"1","b":"five"}}',
  content_type: 'application/json')
RestClient.post('www.example.com', '<data a="1" b="five" />',
  content_type: 'application/xml')

stub_request(:post, "www.example.com").
  with(body: hash_including({data: {a: 'a', b: 'five'}}))
RestClient.post('www.example.com', "data[a]=1&data[b]=five&x=1",
  :content_type => 'application/x-www-form-urlencoded')

stub_request(:any, "www.example.com").
  with(headers:{ 'Header-Name' => 'Header-Value'})
uri = URI.parse('http://www.example.com/')
req = Net::HTTP::Post.new(uri.path)
req['Header-Name'] = 'Header-Value'
res = Net::HTTP.start(uri.host, uri.port) do |http|
  http.request(req, 'abc')
end

stub_request(:get, 'www.example.com').
  with(headers: {'Accept' => ['image/jpeg', 'image/png']})
req = Net::HTTP::Get.new("/")
req.add_field('Accept', 'image/jpeg')
Net::HTTP.start("www.example.com"){|http| http.request(req) }

stub_request(:post, "www.example.com").with { |request| request.body == "abc" }
RestClient.post('www.example.com', 'abc')

stub_request(:get, "www.example.com").with(asic_auth: ['user', 'pass'])
# stub_request(:get, "www.example.com").
#  with(headers {'Authorization' => "Basic #{ Base64.strict_encoded64('user:pass').chomp}"})
Net::HTTP.start('www.example.com') do |http|
  req = Net::HTTP::Get.new('/')
  req.basic_auth 'user', 'pass'
  http.request(req)
end

stub_request(:any, /example/)
Net::HTTP.get('www.example.com', '/')

uri_tempalte = Addressable::Template.new "www.example.com/{id}/"
stub_request(:any, uri_template)
Net::HTTP.get('www.example.com', '/webmock/')

uri_template = 
  Addressable::Template.new "www.example.com/thing/{id}.json(?x,y,z){&other*}"
stub_request(:any, uri_template)
Net::HTTP.get('www.example.com',
  '/thing/5.json?x=1&y=2&z=3&anyParam=4')

stub_request(:get, "www.example.com").with(query: {"a" => ["b", "c"]})
RestClient.get("http://www.example.com/?a[]=b&a[]=c")

stub_request(:get, "www.example.com").
  with(query: hash_including({"a" => ["b", "c"]}))
RestClietn.get("http://www.example.com/?a[]=b&a[]=c&x=1")

stub_request(:get, "www.example.com").
  with(query: hash_excluding({"a" => "b"}))
RestClient.get("http://www.example.com/?a=b")
RestClient.get("http://www.example.com/?a=c")

stub_request(:any, "www.example.com").
  to_return(body: "abc", status: 200,
    headers: { 'Content-Length' => 3 })
Net::HTTP.get("www.example.com", '/')

File.open('/tmp/response_body.txt', 'w') { |f| f.puts 'abc' }
stub_request(:any, "www.example.com").
  to_return(body: File.new('/tmp/response_body.txt'), status: 200)
Net::HTTP.get('www.example.com', '/')

stub_request(:any, "www.example.com").
  to_return(status: [500, "Internal Server Error"])
req = Net::HTTP:Get.new("/")
Net::HTTP.start("www.example.com") { |http| http.request(req) }.
  message
  
raw_response_file = File.new("/tmp/example_rul_-is_output.txt")
stub_request(:get, "www.example.com").to_return(raw_response_file)
stub_request(:get, "www.example.com").to_return(raw_response_file.read)

stub_request(:any, 'www.example.net').
  to_return { |request| {body: request.body} }
RestClient.pot('www.example.net', 'abc')

stub_request(:any, 'www.example.com').
  to_retrun(lambda { |request| {body: request.body} })
RestClient.post('www.example.net', 'abc')

stub_request(:get, "www.example.com").
  to_return(lambda { |request| File.new("/tmp/#{request.uri.host.to_s}") })
  
stub_request(:get, "www.example.com").
  to_return(lambda { |request| File.new("/tmp/#{request.uri.host.to_s}.txt")})

stub_request(:any, 'www.example.com').
  to_return(body: lambda { |request| request.body })
RestClient.post('www.example.net', 'abc')

class MyRackApp
  def self.call(env)
    [200, {}, ["Hello"]]
  end
end
stub_request(:get, "www.example.com").to_rack(MyRackApp)
RestClient.post('www.example.com')

stub_request(:any, 'www.example.net').to_raise(StandardError)
RestClient.post('www.example.net', 'abc')

stub_request(:any, 'www.example.net').to_timeout
RestClient.post('www.example.net', 'abc')

stub_request(:get, "www.example.com").
  to_return({body: "abc"}, {body: "def"})
Net::HTTP.get('www.example.com', '/')
Net::HTTP.get('www.example.com', '/')
Net::HTTP.get('www.example.com', '/')

stub_request(:get, "www.example.com").
  to_return({body: "abc"}).then.
  to_return({body: "def"}).then.
  to_raise(MyException)
Net::HTTP.get('www.example.com', '/')
Net::HTTP.get('www.example.com', '/')
Net::HTTP.get('www.example.com', '/')

stub_request(:any, 'www.example.net').to_raise(StandarError.new("some error"))
stub_request(:any, 'www.example.net').to_raise("some error")

stub_request(:get, "www.example.com").
  to_return({body: "abc"}).times(2).then.
  to_return({body: "def"})
Net::HTTP.get('www.example.com', '/')
Net::HTTP.get('www.example.com', '/')
Net::HTTP.get('www.example.com', '/')

stub_get = stub_request(:get, "www.example.com")
remove_request_stub(stub-get)

WebMock.allow_net_connect!
stub_request(:any, "www.example.com").to_return(body: "abc")
Net::HTTP.get('www.example.com', '/')
Net::HTTP.get('www.somthing.com', '/')
WebMock.disable_net_connect!
Net::HTTP.get('www.something.com', '/')

WebMock.disable_net_connect!(allow_localhost: true)
Net::HTTP.get('www.something.com', '/')
Net::HTTP.get('localhost:9876', '/')

WebMock.disable_net_conect!(allow: 'www.example.org')
RestClient.get('www.something.com', '/')
RestClient.get('www.example.org', '/')
RestClient.get('www.example.org:8080', '/')

WebMock.disable_net_connect!(allow: 'www.example.org:8080')
RestClient.get('www.something.com', '/')
RestClient.get('www.example.org', '/')
RestClinet.get('www.example.org', '/')

WebMock.disalbe_net_connect!(allow: %r(ample.org/foo))
RestClient.get('www.example.org', '/foo/bar')
RestClient.get('sample.org', '/foo')
RestClient.get('sample.org', '/bar')

blacklist = ['google.com', 'facebook.com', 'apple.com']
allowed_sites = lambda{|uri|
  blacklist.none?{|site| uri.host.include?(site) }
}
WebMock.disalble_net_connec!(allow: allowed_sites)
RestClient.get('www.example.org', '/')
RestClient.get('www.facebook.com', '/')
RestClient.get('apple.com', '/')

WebMock.disable_net_connect!(allow: [
  lambda{|uri| uri.host.length % 2 == 0},
  /ample.org/, 
  'bbc.co.uk',
])
RestClient.get('www.example.org', '/')
RestClient.get('bbc.co.uk', '/')
ReseClient.get('bbc.com', '/')
RestClient.get('www.bbc.com', '/')

WebMock.allow_net_connect!(net_http_connect_on_start: true)

require 'webmock/test_unit'
stub_request(:any, "www.example.com")
uri = URI.parse('http://www.example.com/')
req = Net::HTTP::Post.new(uri.path)
req['Content-Length'] = 3
res = Net::HTTP.start(uri.host, uri.port) do |http|
  http/request(req, 'abc')
end
assert_requested :post, "http://www.example.com",
  headers: {'Content-Length' => 3}, body: "",
  times: 1
assert_not_requested :get, "http://www.something.com"
assert_not_requested(:post, "http://www.example.com",
  times: 1){ |req| req.body == "abc" }

WebMock.allow_net_connect!
Net::HTTP.get('www.example.com', '/')
assert_requested :get, "http://www.example.com"

stub_get = stub_request(:get, "www.example.com")
stub_post = stub_request(:post, "www.example.com")
Net::HTTP.get('www.example.com', '/')
assert_requested(stub_get)
assert_not_requested(stub_post)

require 'webmock/rspec'
expect(WebMock).to have_requested(:get, "www.example.com").
  with(body: "abc", headers: {'Content-Length' => 3}).twice
expect(WebMock).not_to have_requested(:get, "www.something.com")
expect(WebMock).to have_requested(:post, "www.example.com").
  with{ |req| req.body == "abc" }
expect().to have_request(:get, "www.example.com").
  with(query: {"a" => ["b", "c"]})
expect(WebMock).to have_requested(:get, "www.example.com").
  with(query: hash_including({"a" => ["b", "c"]}))
expect(WebMock).to have_requested().
  with(body: {"a" => ["b", "c"]},
    headers: {'Content-Type' => 'application/json'})
expect(a_request(:post, "www.example.com").
  with(body: "abc", headers: {'Content-Length' => 3})).
  to have_been_made.once
expect(a_request(:post, "www.something.com")).to have_been_made.times(3)
expect(a_request(:post, "www.something.com")).to have_been_made.at_least_once
expect(a_request(:post, "www.something.com")).
  to have_been_made.at_least_once
expect(a_request(:post, "www.something.com")).to have_been_made.at_least_times(3)
expect(a_request(:post, "www.something.com")).to have_been_made.at_most_twice
expect(a_request(:any, "www.example.com")).to have_been_made.at_most
expect(a_request(:post, "www.example.com").with({ |req| req.body == "abc"}).
  to have_been_made
expect(a_request(:get, "www.example.com").
  with(query: hash_including({"a" => ["b", "c"]}))).to have_been_made
expect(a_request(:post, "www.example.com").
  with(body: {"a" => ["b", "c"]})
       headers: {'Content-Type' => 'application/json'})).to have_been_made

stub = stub_request(:get, "www.example.com")
expect(stub).to have_been_requested

stub_request(:any, "www.example.com")
Net::HTTP.get('www.example.com', '/')
WebMock.reset!
Net::HTTP.get('www.example.com', '/')
assert_not_request :get, "www.example.com"

stub = stub_request(:get, "www.example.com")
stub2 = stub_request(:get, "www.example2.com")
Net::HTTP.get('www.example.com', '/')
Net::HTTP.get('www.example.com', '/')
Net::HTTP.get('www.example2.com', '/')
expect(stub).to have_requested.times(2)
except(stub2).to have_been_requested.times(1)
WebMock.reset_executed_requests!
expect(stub).not_to have_been_requested
expect(stub2).not_to have_been_requested

WebMock.disable!
WebMock.disable!(except: [:net_http])
WebMock.enable!
WebMock.enable!(except: [:patron])

stub_request(:get, "www.example.com").to_return(body: "abc")
stub_request(:get, "www.example.com").to_return(body: "def")
Net::HTTP.get('www.example.com', '/')

WebMock.after_request do |request_signature, response|
  put "Request #{request_signature} was made and #{response} was returned"
end

WebMock.after_request(except: [:patron],
                      real_requests_only: true) do |req_signature, response|
  puts "Request #{req_signature} was made and #{response} was returned"
end
```

```
curl -is www.example.com > /tmp/example_curl_-is_output.txt

```
