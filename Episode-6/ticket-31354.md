# 🚀 Space Reviewers 👾 - Episode 6

## Details

- Date: [2025-07-12 4.30pm UTC](https://time.is/compare/1630_12_July_2025_in_UTC)
- Ticket: [#31354](https://code.djangoproject.com/ticket/31354)
- PR: [#18835](https://github.com/django/django/pull/18835)
- Stream: TBD

## Problem

`HttpRequest.get.host()` don't consider a specific port in the header `HTTP_X_FORMWARDED_PORT` when using a reverse proxy.

### Example 
```
server{
    listen 8443 ssl;
    server_name localhost.example.com
    
    location / {
        proxy_pass http://localhost:8001;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Port $server_port;
    }
}
```

## Summarized issue discussion

[First approach](https://github.com/django/django/pull/12550) was made by **dgcgh** to modify `get_host()` to include the port when it's not the default one.
`X-Forwarded-Host` already contains a port so it shoulden't attach `X-Forwarded-Port` so [apollo13 suggested](https://github.com/django/django/pull/12550#discussion_r391258985) if in the settings if the option `USE_X_FORWARDED_PORT`
is true and the host already contains a port throws an error.
[felixxm agreed](https://github.com/django/django/pull/12550#issuecomment-598687597) on having both supported `USE_X_FORWARDED_HOST` and `USE_X_FORWARDED_PORT`.

[The next approach](https://github.com/django/django/pull/12844) was made by **apollo13** by creating a new function `_parsed_host_header()`

The current [PR](https://github.com/django/django/pull/18835) is following the previous approach.

## Appendix
### Documentation
[Nginx configuration](https://nginx.org/en/docs/http/ngx_http_v3_module.html)
[HttpRequest.get_port()](https://docs.djangoproject.com/en/5.2/ref/request-response/#django.http.HttpRequest.get_port)
[USE_X_FORWARDED_HOST](https://docs.djangoproject.com/en/5.2/ref/settings/#use-x-forwarded-host)
[USE_X_FORWARDED_PORT](https://docs.djangoproject.com/en/5.2/ref/settings/#use-x-forwarded-port)
