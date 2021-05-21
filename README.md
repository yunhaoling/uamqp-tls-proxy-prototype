## major steps

1. install the [1.4.0b1 uamqp wheel](https://github.com/yunhaoling/uamqp-tls-proxy-prototype/tree/main/uamqp_1.4.0b1_tls_proxy_test_wheels) which matches OS and Python version.

```bash
pip install uamqp-1.4.0b1-xxxxxx.whl
```

2. update the http proxy configuration dict in the application code to include `proxy_verify` and `proxy_cert`

- `proxy_verify` should be the path to the proxy server certificate file
- `proxy_cert` should be a tuple of both client side certificate and private key files’ paths

Notes:
- the wheels are built based on the draft PR: https://github.com/Azure/azure-uamqp-python/pull/232
- there is no change in azure-eventhub, the http proxy config is passed through to the uamqp untouched.

## sample code:

### EventHubProducerClient and EventHubConsumerClient

```python
import os
from azure.eventhub import EventData, EventHubConsumerClient, EventHubProducerClient

CONNECTION_STR = os.environ["EVENT_HUB_CONN_STR"]
EVENTHUB_NAME = os.environ['EVENT_HUB_NAME']

# proxy host name
proxy_hostname = "<host_name>"  # replace with your proxy host name
# proxy port
proxy_port = 443  # replace with your proxy port
# path to the proxy server certificate file
proxy_verify = r"<server_cert.pem>"  # optional, replace with path to your proxy server cert
# a tuple of both client side certificate and private key files’ paths
proxy_cert = (r"<client_cert.pem>", r"<client_private_key.pem>")  # optional, replace with paths to your client side cert and private key


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


### ServiceBusClient

```python
import os
from azure.servicebus import ServiceBusClient

CONNECTION_STR = os.environ['SERVICE_BUS_CONNECTION_STR']


# proxy host name
proxy_hostname = "<host_name>"  # replace with your proxy host name
# proxy port
proxy_port = 443  # replace with your proxy port
# path to the proxy server certificate file
proxy_verify = r"<server_cert.pem>"  # optional, replace with path to your proxy server cert
# a tuple of both client side certificate and private key files’ paths
proxy_cert = (r"<client_cert.pem>", r"<client_private_key.pem>")  # optional, replace with paths to your client side cert and private key


HTTP_PROXY = {
    'proxy_hostname': proxy_hostname,
    'proxy_port': proxy_port,
    "proxy_verify": proxy_verify,
    "proxy_cert": proxy_cert
}


servicebus_client = ServiceBusClient.from_connection_string(
    conn_str=CONNECTION_STR,
    http_proxy=HTTP_PROXY
)


# application code
```