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

stub_request().
  with()
RestClient.post('www.example.com', "",
  :content_type => '')

stub_request().
  with()
req = Net::HTTP::Get.new()
req[] = []
req.add_field()
Net::HTTP.start(){}

stub_request().with {}
RestClient.post()

stub_request().with()
# stub_request().
#  with()
Net::HTTP.start() do |http|
end










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
```
