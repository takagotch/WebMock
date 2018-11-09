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



```

```
```
