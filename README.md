## read-only-cerebro

Sample nginx + [cerebro](https://github.com/lmenezes/cerebro) config to allow nondestructive (i.e. GET) and prevent all destructive (POST/PUT/DELETE) requests to cerebro.

Lets you run cerebro with a production ES cluster without fear of destructive actions. nginx is run as a reverse proxy that sits in front of cerebro:

```
user/internet  --> cerebro --> ES
```

