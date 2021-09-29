# Logjoy

Logjoy makes some changes to Rails default `ActiveSupport::LogSubscriber`s in order
to provide streamlined request logs for use in production.

The name is an homage to the [lograge gem](https://github.com/roidrage/lograge)
which is no longer maintained and which this gem is intended to replace.

See
[LOGRAGE_README](https://github.com/pathouse/logjoy/blob/main/LOGRAGE_README.md)
for more information about the differences between this gem and lograge.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'logjoy'
```

And then execute:

    $ bundle install

## Usage

Logjoy can be configured with the following options:

`config/initializers/logjoy.rb`

```ruby
Rails.application.configure do |config|
  config.logjoy.enabled = true

  config.logjoy.customizer = CustomizerClass
  # or
  config.logjoy.customizer = ->(event) { ... }
end
```

The customizer configuration may be a class that implements a `.call` method, or a lambda.
The customizer will receive an `ActiveSupport::Notification` event for the
`process_action.action_controller` event.
It should return a hash that will be added to the log in a `:custom` field.

More documentation about this event can be found here:
https://guides.rubyonrails.org/active_support_instrumentation.html#process-action-action-controller

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
https://github.com/pathouse/logjoy. This project is intended to be a safe,
welcoming space for collaboration, and contributors are expected to adhere to
the [code of
conduct](https://github.com/[USERNAME]/logjoy/blob/master/CODE_OF_CONDUCT.md).

## License

The gem is available as open source under the terms of the [MIT
License](https://opensource.org/licenses/MIT).
