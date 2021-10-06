# Logjoy

## Overview

Logjoy makes some changes to Rails default `ActiveSupport::LogSubscriber`s in order
to provide streamlined request logs for use in production.

The name is an homage to the [lograge gem](https://github.com/roidrage/lograge)
which is no longer maintained and which this gem is intended to replace.

See
[LOGRAGE_README](https://github.com/DogParkLabs/logjoy/blob/main/LOGRAGE_README.md)
for more information about the differences between this gem and lograge.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'logjoy'
```

And then execute:

    $ bundle install

## Configuration

Logjoy can be configured with the following options:

### enabled

`config.logjoy.enabled = true`

You will likely only want to enable Logjoy in production, e.g. `config.logjoy.enabled = Rails.env.production?`

### customizer

You can provide a lambda:

`config.logjoy.customizer = ->(event) { ... }`

or a class that implements a `.call` method:

`config.logjoy.customizer = CustomizerClass`

The customizer will receive an `ActiveSupport::Notification` event for the
`process_action.action_controller` event.
It should return a hash that will be added to the log message.

More documentation about this event can be found here:
https://guides.rubyonrails.org/active_support_instrumentation.html#process-action-action-controller

### filters

`config.logjoy.filters = ['/paths', '/to', '/ignore']`

The filters configuration can be used to ignore requests with the given path to
reduce log noise from things like health checks.

### Example of full configuration

`config/initializers/logjoy.rb`

```ruby
Rails.application.configure do |config|
  config.logjoy.enabled = true

  config.logjoy.customizer = CustomizerClass
  # or
  config.logjoy.customizer = ->(event) { ... }

  config.logjoy.filters = ['/health_check']
end
```

## Log Format

Log messages are JSON formatted.

```json
{
  "controller": "PagesController",
  "action": "index",
  "format": "html",
  "method": "GET",
  "path": "/",
  "status": 200,
  "view_runtime": 123.456,
  "db_runtime": 123.456,
  "duration": 1234.567,
  "params": {},
  "request_id": "95cb397a-df23-4548-b641-ae8eef9d3a4e",
  "event": "process_action.action_controller",
  "allocations": 123456
}
```

If you are using `Logger::Formatter` to format your logs the output should look something like:

`I, [timestamp]  INFO -- : [request_id] {"controller":"PagesController", ...}`

If you define your own formatter keep in mind that `msg` is already JSON formatted in the `#call` method that you will define:

```
class MyFormatter < Logger::Formatter
  def call(severity, time, progname, msg)
    # msg is a JSON string
  end
end
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run
`rake test` to run the tests. You can also run `bin/console` for an interactive
prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To
release a new version, update the version number in `version.rb`, and then run
`bundle exec rake release`, which will create a git tag for the version, push
git commits and the created tag, and push the `.gem` file to
[rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at
https://github.com/DogParkLabs/logjoy. This project is intended to be a safe,
welcoming space for collaboration, and contributors are expected to adhere to
the [code of
conduct](https://github.com/DogParkLabs/logjoy/blob/master/CODE_OF_CONDUCT.md).

## License

The gem is available as open source under the terms of the [MIT
License](https://opensource.org/licenses/MIT).
