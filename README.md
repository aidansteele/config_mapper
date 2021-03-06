# ConfigMapper

ConfigMapper maps configuration data onto Ruby objects.

## Usage

Imagine you have some Ruby objects:

    class Position

      attr_reader :x
      attr_reader :y

      def x=(arg); @x = Integer(arg); end
      def y=(arg); @y = Integer(arg); end

    end

    class State

      def initialize
        @position = Position.new
      end

      attr_reader :position
      attr_accessor :orientation

    end

    state = State.new

and wish to populate/modify it, based on plain data:

    config_data = {
      "orientation" => "North",
      "position" => {
        "x" => 2,
        "y" => 4
      }
    }

ConfigMapper will help you out:

    require 'config_mapper'

    errors = ConfigMapper.set(config_data, state)
    state.orientation              #=> "North"
    state.position.x               #=> 2

It can even populate Hashes of objects, e.g.

    positions = Hash.new { |h,k| h[k] = Position.new }

    config_data = {
      "fred" => { "x" => 2, "y" => 4 },
      "mary" => { "x" => 3, "y" => 5 }
    }

    ConfigMapper.set(config_data, positions)
    positions["fred"].x            #=> 2
    positions["mary"].y            #=> 5

### Errors

`ConfigMapper.set` returns a Hash of errors encountered while mapping data
onto objects.  The errors are Exceptions (typically ArgumentError or NoMethodError),
keyed by a dot-delimited path to the offending data.  e.g.

    config_data = {
      "position" => {
        "bogus" => "flibble"
      }
    }

    errors = ConfigMapper.set(config_data, state)
    errors    #=> { "position.bogus" => #<NoMethodError> }

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

## Contributing

It's on GitHub; you know the drill.
