# ScientiaMobile WURFL Cloud Client for Ruby
This software is the Copyright of ScientiaMobile, Inc.

Please refer to the LICENSE.txt file distributed with the software for licensing information.

About
----------------

The WURFL Cloud Service by ScientiaMobile, Inc., is a cloud-based mobile device detection service that can quickly and accurately detect over 500 capabilities of visiting devices.  It can differentiate between portable mobile devices, desktop devices, SmartTVs and any other types of devices that have a web browser.

In order to use the WURFL Cloud client for Ruby you need to obtain an API key from [Scientiamobile](http://www.scientiamobile.com/ "Scientiamobile"). Once you've chosen your level of service and the capabilities that you'll generally query, install the python WURFL Cloud client and create a configuration file.

Minimum Requirements
----------------

* Ruby 1.9.2
* rubygems
* json
* rack

Rack Setup
----------------

If you want to use the cookie cache you need to configure the CacheManager middleware:

```ruby
Rack::Builder.new do

  use WurflCloud::Rack::CacheManager

  run SomeApp
end
```

Rails setup
----------------

You need to add the gem to the Gemfile:

```ruby
gem 'wurfl_cloud_client', :require=>'wurfl_cloud'
```

Then if you use the Cookie cache method you will need to add the CacheManager middleware adding this to an initializer:

```ruby
Rails.configuration.middleware.use WurflCloud::Rack::CacheManager
```

Client configuration
----------------

You can configure the client passing a block to the WurflCloud.configure method 

```ruby
WurflCloud.configure do |config|
  config.host = 'api.wurflcloud.com'
  config.api_key = '100000:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
end
```

these are the configuration parameters available: 

* <code>api_key</code>: The API key to access the WurflCloud api (defaults to "100000:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx").
* <code>api_user</code>: The API user id decoded from the api_key
* <code>api_password</code>: The API password decoded from the api_key
* <code>schema</code>: The schema of the URI for the api server (defaults to http).
* <code>host</code>: The host to connect to (defaults to api.wurflcloud.com).
* <code>port</code>: The port on which your WurflCloud server runs (defaults to 80).
* <code>path</code>: The path (URL) for the API endpoint at the host (defaults to /v1/json).
* <code>api_type</code>: The API Type, defaults to http (*not used*)
* <code>search_parameter</code>: The search parameter format (defaults to "search:(%{capabilities})")
* <code>search_parameter_separator</code>: The search parameter capabilities separator (defaults to ",")
* <code>cache_class</code>: The cache class to be used (defaults to a WurflCloud::Cache::Null )
* <code>cache_options</code>: The cache_options to be used (defaults to {})

Install
----------------

Download the latest gem of WURFL Cloud Client for Ruby library from ScientiaMobile.com portal site. Then, run the following command:

```
gem install wurfl_cloud_client-0.X.Y.gem
```

Usage
----------------

To detect a device you can use the wurfl_detect_device method including WurflCloud::Helper and passing the current http environment:

```ruby
include WurflCloud::Helper
@device = wurfl_detect_device(env)
```

Then you can use the @device to get its id or its capabilities:

```ruby
@device['is_wireless_device']
```

In a ruby on rails application the wurfl_detect_device method is available both in the controllers and in the views

Example
----------------

There is an example Ruby on Rails application in the <code>wurfl_cloud_client_example</code> folder that demonstrates the usage of the WURFL Cloud Client for Ruby. Please refer to the wurfl_cloud.rb initializer and the demo_controller details to see how visitor's devices are detected.

Cache classes
----------------

You can choose among the following cache classes:

### WurflCloud::Cache::Null 

This is the *non-caching* class, i.e. you should use this if you want to disable caching (not advisable)

### WurflCloud::Cache::Cookie

This class uses a cookie to store on the client all the capabilities detected by the WurflCloud API. To be able to use this class you'll need to add the ```WurflCloud::Rack::CacheManager``` middleware.

Example configuration for ```WurflCloud::Cache::Cookie``` (if using in a rails app put it in and initializer)

```ruby
WurflCloud.configure do |config|
  config.host = 'api.wurflcloud.com'
  config.api_key = '000000:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx' # add you api key here
  config.cache_class = WurflCloud::Cache::Cookie
end

Rails.configuration.middleware.use WurflCloud::Rack::CacheManager
```

### WurflCloud::Cache::Rails

This class uses the ```Rails.cache``` class to store the capabilities read from the API (and thus uses any cache backend configured inyour rails app)

Example configuration for ```WurflCloud::Cache::Rails``` (if using in a rails app put it in and initializer)

```ruby
require 'wurfl_cloud/cache/rails'
WurflCloud.configure do |config|
  config.host = 'api.wurflcloud.com'
  config.api_key = '000000:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx' # add you api key here
  config.cache_class = WurflCloud::Cache::Rails
  config.cache_options = {:prefix=>'my_app'}
end
```

The (optional) ```prefix``` parameter is used to namespace the keys used by the WurflCloud client .

This cache uses the ```mtime``` parameter returned from the API to do cache invalidation: the current mtime is stored in the cache and used as a prefix for the device key such that when a new mtime is read from the WurflCloud APIit replaces the old key prefix and all the old keys are invalidated.

### WurflCloud::Cache::Mamcached

This class uses the dalli high performance memcached ruby client to store the capabilities read from the API in a mamcached server. To use this cache you need to have installed the dalli gem and pass the connection string in the ```cache_options``` configuration. 

Example configuration for ```WurflCloud::Cache::Mamcached``` (if using in a rails app put it in and initializer)

```ruby
require 'wurfl_cloud/cache/memcached'
WurflCloud.configure do |config|
  config.host = 'api.wurflcloud.com'
  config.api_key = '000000:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx' # add you api key here
  config.cache_class = WurflCloud::Cache::Memcached
  config.cache_options = {:server=>'localhost:11211', :prefix=>'my_app'}
end
```

The ```server``` parameter is required and should contain the memcached server (or array of servers) thet should be used by the client.

The (optional) ```prefix``` parameter is used to namespace the keys used by the WurflCloud client.

Any other option that is passed in the ```cache_options``` configuration parameter is passed through to the ```Dalli::Client``` object e.g. you can pass the ```expires_in``` option to set a global TTL for the cached objects (the default TTL is 1 day).

This cache uses the ```mtime``` parameter returned from the API to do cache invalidation: the current mtime is stored in the cache and used as a prefix for the device key such that when a new mtime is read from the WurflCloud APIit replaces the old key prefix and all the old keys are invalidated.


License
========================================
### SCIENTIAMOBILE CONFIDENTIAL

  2012 ScientiaMobile Incorporated
  All Rights Reserved.

NOTICE:  All information contained herein is, and remains the property of ScientiaMobile Incorporated and its suppliers, if any.  The intellectual and technical concepts contained herein are proprietary to ScientiaMobile Incorporated and its suppliers and may be covered by U.S. and Foreign Patents, patents in process, and are protected by trade secret or copyright law. Dissemination of this information or reproduction of this material is strictly forbidden unless prior written permission is obtained from ScientiaMobile Incorporated.