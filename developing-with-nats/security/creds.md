# Authenticating with a Credentials File

The 2.0 version of NATS server introduced the idea of JWT-based authentication. Clients interact with this new scheme using a user JWT and the private key from an NKey pair. To help make connecting with a JWT easier, the client libraries support the concept of a credentials file. This file contains both the private key and the JWT and can be generated with the `nsc` tool. The contents will look like the following and should be protected because it contains a private key. This creds file is unused and only for example purposes.

```text
-----BEGIN NATS USER JWT-----
eyJ0eXAiOiJqd3QiLCJhbGciOiJlZDI1NTE5In0.eyJqdGkiOiJUVlNNTEtTWkJBN01VWDNYQUxNUVQzTjRISUw1UkZGQU9YNUtaUFhEU0oyWlAzNkVMNVJBIiwiaWF0IjoxNTU4MDQ1NTYyLCJpc3MiOiJBQlZTQk0zVTQ1REdZRVVFQ0tYUVM3QkVOSFdHN0tGUVVEUlRFSEFKQVNPUlBWV0JaNEhPSUtDSCIsIm5hbWUiOiJvbWVnYSIsInN1YiI6IlVEWEIyVk1MWFBBU0FKN1pEVEtZTlE3UU9DRldTR0I0Rk9NWVFRMjVIUVdTQUY3WlFKRUJTUVNXIiwidHlwZSI6InVzZXIiLCJuYXRzIjp7InB1YiI6e30sInN1YiI6e319fQ.6TQ2ilCDb6m2ZDiJuj_D_OePGXFyN3Ap2DEm3ipcU5AhrWrNvneJryWrpgi_yuVWKo1UoD5s8bxlmwypWVGFAA
------END NATS USER JWT------

************************* IMPORTANT *************************
NKEY Seed printed below can be used to sign and prove identity.
NKEYs are sensitive and should be treated as secrets.

-----BEGIN USER NKEY SEED-----
SUAOY5JZ2WJKVR4UO2KJ2P3SW6FZFNWEOIMAXF4WZEUNVQXXUOKGM55CYE
------END USER NKEY SEED------

*************************************************************
```

Given a creds file, a client can authenticate as a specific user belonging to a specific account:

{% tabs %}
{% tab title="Go" %}
```go
nc, err := nats.Connect("127.0.0.1", nats.UserCredentials("path_to_creds_file"))
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
            server("nats://localhost:4222").
            authHandler(Nats.credentials("path_to_creds_file")).
            build();
Connection nc = Nats.connect(options);

// Do something with the connection

nc.close();
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// credentials file contains the JWT and the secret signing key
let credsFile = path.join(confDir, 'credsfile.creds');

let nc = NATS.connect({
    url: server.nats,
    userCreds: credsFile
});
```
{% endtab %}

{% tab title="Python" %}
```python
nc = NATS()

async def error_cb(e):
    print("Error:", e)

await nc.connect("nats://localhost:4222",
                 user_credentials="path_to_creds_file",
                 error_cb=error_cb,
                 )

# Do something with the connection

await nc.close()
```
{% endtab %}

{% tab title="TypeScript" %}
```typescript
// credentials file contains the JWT and the secret signing key
let credsFile = path.join(confDir, 'credsfile.creds');

let nc = await connect({
    url: server.nats,
    userCreds: credsFile
});
```
{% endtab %}
{% endtabs %}

