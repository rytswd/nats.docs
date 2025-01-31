# Turning Off Echo'd Messages

By default a NATS connection will echo messages if the connection also has interest in the published subject. This means that if a publisher on a connection sends a message to a subject any subscribers on that same connection will receive the message. Clients can opt to turn off this behavior, such that regardless of interest the message will not be delivered to subscribers on the same connection.

The NoEcho option can be useful in BUS patterns where all applications subscribe and publish to the same subject. Usually a publish represents a state change that the application already knows about, so in the case where the application publishes an update it does not need to process the update itself.

![](../../.gitbook/assets/noecho.svg)

Keep in mind that each connection will have to turn off echo, and that it is per connection, not per application. Also, turning echo on and off can result in a major change to your applications communications protocol since messages will flow or stop flowing based on this setting and the subscribing code won't have any indication as to why.

{% tabs %}
{% tab title="Go" %}
```go
// Turn off echo
nc, err := nats.Connect("demo.nats.io", nats.Name("API NoEcho Example"), nats.NoEcho())
if err != nil {
	log.Fatal(err)
}
defer nc.Close()

// Do something with the connection
```
{% endtab %}

{% tab title="Java" %}
```java
Options options = new Options.Builder().
                            server("nats://demo.nats.io:4222").
                            noEcho(). // Turn off echo
                            build();
Connection nc = Nats.connect(options);

// Do something with the connection

nc.close();
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
let nc = NATS.connect({
    url: "nats://demo.nats.io:4222",
    noEcho: true
});
```
{% endtab %}

{% tab title="Python" %}
```python
ncA = NATS()
ncB = NATS()

await ncA.connect(no_echo=True)
await ncB.connect()

async def handler(msg):
   # Messages sent by `ncA' will not be received.
   print("[Received] ", msg)

await ncA.subscribe("greetings", cb=handler)
await ncA.flush()
await ncA.publish("greetings", b'Hello World!')
await ncB.publish("greetings", b'Hello World!')

# Do something with the connection

await asyncio.sleep(1)
await ncA.drain()
await ncB.drain()
```
{% endtab %}

{% tab title="Ruby" %}
```ruby
NATS.start("nats://demo.nats.io:4222", no_echo: true) do |nc|
  # ...
end
```
{% endtab %}

{% tab title="TypeScript" %}
```typescript
let nc = await connect({
    url: "nats://demo.nats.io:4222", noEcho: true});
```
{% endtab %}
{% endtabs %}

