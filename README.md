# Fork notice

This repo is a fork from https://github.com/lucidstack/ex-portmidi by @thbar, with continuous integration enabled (against MacOS), targetting Elixir 1.12 and OTP 23. It contains fixes for all the warnings I could find, and updated test dependencies.

It may or may not diverge from the original fork in the future, and has not been published to Hex at this point, so you'll have to install from GitHub.

[![Build Status](https://github.com/thbar/ex-portmidi/actions/workflows/elixir.yml/badge.svg)](https://github.com/thbar/ex-portmidi/actions)

# ex-portmidi

**Need to catch up?** Have a look at the [changelog](/CHANGELOG.md)! 🚀

ex-portmidi is a wrapper for the [PortMidi C library](http://portmedia.sourceforge.net/portmidi/),
that provides some nice abstractions to (write to|listen on) MIDI devices.

## Installation

Add portmidi to your list of dependencies in `mix.exs`, and ensure
that `portmidi` is started before your application:
```
def deps do
  [{:portmidi, "~> 5.0"}]
end

def application do
  [applications: [:portmidi]]
end
```

## Configuration

If needed, the input buffer size can be set, in your `config.exs`:

```
  config :portmidi, buffer_size: 1024
```

By default, this value is 256.

## Usage

To send MIDI events to a MIDI device:
```
iex(1)> {:ok, output} = PortMidi.open(:output, "Launchpad Mini")
{:ok, #PID<0.172.0>}

iex(2)> PortMidi.write(output, {176, 0, 127})
:ok

iex(3)> PortMidi.write(output, {{176, 0, 127}, 123}) # with timestamp
:ok

iex(4)> PortMidi.write(output, [
iex(5)>   {{176, 0, 127}, 123},
iex(6)>   {{178, 0, 127}, 128}
iex(7)> ]) # as a sequence of events (more efficient)
:ok

iex(8)> PortMidi.close(:output, output)
:ok
```

To listen for MIDI events from a MIDI device:
```
iex(1)> {:ok, input} = PortMidi.open(:input, "Launchpad Mini")
{:ok, #PID<0.103.0>}

ex(2)> PortMidi.listen(input, self)
:ok

iex(3)> receive do
...(3)>   {^input, event} -> IO.inspect(event)
...(3)> end
{144, 112, 127}

iex(4)> PortMidi.close(:input, input)
:ok
```

To list all connected devices:
```
ex(1)> PortMidi.devices
%{input: [%PortMidi.Device{input: 1, interf: "CoreMIDI", name: "Launchpad Mini",
    opened: 0, output: 0}],
  output: [%PortMidi.Device{input: 0, interf: "CoreMIDI",
    name: "Launchpad Mini", opened: 0, output: 1}]}
```

For more details, [check out the Hexdocs](https://hexdocs.pm/portmidi/PortMidi.html).
