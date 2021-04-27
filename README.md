## major steps

1. install the 1.4.0b1 uamqp wheel matching OS and python.

```bash
pip install uamqp-1.4.0b1-xxxxxx.whl
```

2. update the http proxy configuration dict in the application code to include `proxy_verify` and `proxy_cert`

- `proxy_verify` should be the path to the proxy server certificate file
- `proxy_cert` should be a tuple of both client side certificate and private key files’ paths

Note: there is no change in azure-eventhub, the http proxy config is passed through to the uamqp untouched.

## sample code:

```python
import os
from azure.eventhub import EventData, EventHubConsumerClient, EventHubProducerClient

CONNECTION_STR = os.environ["EVENT_HUB_CONN_STR"]
EVENTHUB_NAME = os.environ['EVENT_HUB_NAME']

# proxy host name
proxy_hostname = "<host_name>"
# proxy port
proxy_port = 443
# path to the proxy server certificate file
proxy_verify = r"<server_cert.pem>"
# a tuple of both client side certificate and private key files’ paths
proxy_cert = (r"<client_cert.pem>", r"<client_private_key.pem>")


HTTP_PROXY = {
    'proxy_hostname': proxy_hostname,
    'proxy_port': proxy_port,
    "proxy_verify": proxy_verify,
    "proxy_cert": proxy_cert
}

producer_client = EventHubProducerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    eventhub_name=EVENTHUB_NAME,
    http_proxy=HTTP_PROXY
)

consumer_client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group='$Default',
    eventhub_name=EVENTHUB_NAME,
    http_proxy=HTTP_PROXY
)

# application code
```
