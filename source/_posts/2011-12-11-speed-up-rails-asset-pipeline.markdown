---
layout: post
title: "speed up rails asset pipeline"
date: 2011-12-11 20:48
comments: true
categories: rails
---

[Asset pipeline](http://guides.rubyonrails.org/asset_pipeline.html)是Rails 3.1里引入的一个特性, 用来帮助拼接，压缩js/css,同时对缓存asset提供了更好的支持.

Asset pipe本身只是对[Sprockets](https://github.com/sstephenson/sprockets)做了一层封装, 通过`Rails.application.assets`可以得到Sprockets自己的rack app.

如果application.js里用到了一堆的js, 在development模式下会发现页面很慢, 即使用了[active_reload](https://github.com/paneq/active_reload)来加速.在chrome console里看下network tab，每次js请求都在100ms以上。慢的原因在于每次请求都要过一次Rails/application里定义的middleware, 跳过这些middleware之后就快多了. 改下config.ru, 世界又和谐了:

```ruby
# This file is used by Rack-based servers to start the application.
require ::File.expand_path('../config/environment',  __FILE__)

map '/assets' do
  assets = Rails.application.assets
  run assets
end

map '/' do
  run Rails.application
end
```
