

- Add this line to immich's host's advanced tab in npm

```
client_max_body_size 50000M;
proxy_request_buffering off;
proxy_max_temp_file_size 0;
proxy_read_timeout 600s;
proxy_send_timeout 600s;
client_body_buffer_size 1024k;
```

