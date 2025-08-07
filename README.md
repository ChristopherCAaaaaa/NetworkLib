# NetworkLib
# NetworkLib

A high-performance, feature-rich networking library for Roblox that simplifies client-server communication with built-in caching, rate limiting, and error handling.

## Features

### 🚀 Core Functionality
- **Promise-based API** - Modern async/await-style programming
- **Automatic Remote Management** - Creates and manages RemoteEvents/RemoteFunctions automatically
- **Client & Server Support** - Works seamlessly on both sides

### ⚡ Performance & Reliability
- **Built-in Caching** - Configurable response caching with TTL support
- **Rate Limiting** - Prevents spam and abuse with per-player limits
- **Automatic Retries** - Configurable retry logic with exponential backoff
- **Timeout Protection** - Prevents hanging requests
- **Performance Metrics** - Track success rates, response times, and cache hits

### 🛡️ Security & Validation
- **Request Validation** - Custom validation functions for incoming data
- **Rate Limit Protection** - Server-side protection against request flooding
- **Error Handling** - Comprehensive error management and recovery

## Quick Start

### Installation
1. Download the `NetworkLib.lua` file
2. Place it in `ReplicatedStorage` or `ServerScriptService/Modules`
3. Require it in your scripts:

```lua
local NetworkLib = require(path.to.NetworkLib)
```

### Basic Usage

#### Server Side
```lua
-- Handle incoming events
NetworkLib:OnServerEvent("PlayerJoined", function(player, data)
    print(player.Name .. " joined with data:", data)
end)

-- Handle function calls with caching
NetworkLib:HandleFunction("GetPlayerData", function(player, data)
    return {
        coins = 1000,
        level = 5,
        inventory = {"sword", "potion"}
    }
end, {
    rateLimit = {limit = 10, window = 60}, -- 10 requests per minute
    validation = function(player, data)
        return typeof(data) == "table"
    end
})

-- Fire to specific client
NetworkLib:FireClient(player, "UpdateUI", {coins = 500})

-- Fire to all clients
NetworkLib:FireAllClients("ServerMessage", "Welcome to the game!")
```

#### Client Side
```lua
-- Fire events to server
NetworkLib:FireServer("PlayerAction", {action = "jump", timestamp = tick()})

-- Listen for server events
NetworkLib:OnClientEvent("UpdateUI", function(data)
    -- Update UI with data
    print("Received UI update:", data)
end)

-- Make function calls with caching and error handling
NetworkLib:InvokeServer("GetPlayerData", {playerId = 12345}, {
    timeout = 10,
    retries = 2,
    cache = {maxAge = 60, ttl = 300} -- Cache for 5 minutes, consider stale after 1 minute
}):andThen(function(result)
    print("Player data:", result)
end):catch(function(error)
    warn("Failed to get player data:", error)
end)
```

## API Reference

### Client Methods

#### `FireServer(eventName, data, options)`
Fires a RemoteEvent to the server.

#### `InvokeServer(eventName, data, options)`
Invokes a RemoteFunction on the server. Returns a Promise.

**Options:**
- `timeout` (number) - Request timeout in seconds (default: 30)
- `retries` (number) - Number of retry attempts (default: 3)
- `cache` (table) - Caching configuration
  - `maxAge` (number) - Maximum age for cached responses
  - `ttl` (number) - Time to live in cache

#### `OnClientEvent(eventName, handler, options)`
Listens for RemoteEvents from the server.

### Server Methods

#### `FireClient(player, eventName, data, options)`
Fires a RemoteEvent to a specific client.

#### `FireAllClients(eventName, data, options)`
Fires a RemoteEvent to all connected clients.

#### `OnServerEvent(eventName, handler, options)`
Listens for RemoteEvents from clients.

**Options:**
- `rateLimit` (table) - Rate limiting configuration
  - `limit` (number) - Maximum requests per window
  - `window` (number) - Time window in seconds
- `validation` (function) - Custom validation function

#### `HandleFunction(eventName, handler, options)`
Handles RemoteFunction calls from clients.

### Utility Methods

#### `GetStats()`
Returns performance statistics including:
- Total/failed requests
- Average response time
- Cache hits/size
- Rate limit entries
- Startup time

#### `ClearCache()`
Clears the response cache.

## Advanced Features

### Automatic Cleanup
NetworkLib automatically cleans up expired cache entries and old rate limit data to prevent memory leaks.

### Performance Monitoring
Built-in performance tracking helps you monitor your networking performance:

```lua
local stats = NetworkLib:GetStats()
print("Success rate:", (stats.totalRequests - stats.failedRequests) / stats.totalRequests * 100 .. "%")
print("Average response time:", stats.avgResponseTime .. "ms")
print("Cache hit rate:", stats.cacheHits / stats.totalRequests * 100 .. "%")
```

### Error Resilience
- Automatic retries with exponential backoff
- Graceful degradation on failures
- Comprehensive error reporting

## Best Practices

1. **Use caching wisely** - Cache responses that don't change frequently
2. **Set appropriate rate limits** - Balance user experience with server protection
3. **Validate all inputs** - Always validate data from clients
4. **Handle errors gracefully** - Use Promise chains to handle failures
5. **Monitor performance** - Regularly check stats to optimize your networking

## License

MIT License - Feel free to use in your projects!

## Contributing

Contributions welcome! Please feel free to submit issues and pull requests.

---

*Built for the Roblox development community* 🎮
