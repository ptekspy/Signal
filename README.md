# Signal

A small, zero-dependency Luau Signal implementation providing a lightweight event system.

Features
- Lightweight, allocation-friendly event dispatch
- Safe coroutine reuse for listener invocation
- Connection objects with `Disconnect()` semantics
- Variadic, generic event payloads via Luau generics

Installation

Install with `wally` (or include directly in your project):

```bash
wally add ptekspy/Signal
```

API

- `local Signal = require(path.to.Signal.init)`
- `local s = Signal.New()` — create a new `Signal` instance

Signal methods
- `s:Connect(fn)` — Connect a listener. Returns a `Connection` object. `fn` will be called with the arguments passed to `Fire`.
- `s:Fire(...)` — Invoke all connected listeners with the provided arguments.
- `s:Wait()` — Yields until the next event and returns the arguments passed to that event.
- `s:FireUntil(continue_callback, ...)` — Fire listeners and stop early if `continue_callback()` returns a non-true value.

Connection
- `connection:Disconnect()` — Disconnect the listener so it will no longer receive events.

Examples

Basic usage:

```lua
local Signal = require(path.to.Signal.init)

local s = Signal.New()

local conn = s:Connect(function(player, amount)
	print(player, "received", amount)
end)

s:Fire("Player1", 10)

conn:Disconnect()
```

Waiting for the next event:

```lua
local s = Signal.New()
spawn(function()
	task.wait(0.1)
	s:Fire("done")
end)

local result = s:Wait()
print(result)
```

Notes
- Listener invocation order is not guaranteed — the current implementation invokes in reverse connection order.
- The implementation uses a coroutine runner reuse pattern to minimize allocations when dispatching handlers.

License

See `LICENSE.md` for licensing information.

Contributing

PRs and issues are welcome. Keep changes small and include tests where appropriate.
