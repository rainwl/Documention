# libhv

## Overview
Like libevent, libev, and libuv, libhv provides event-loop with non-blocking IO and timer, but simpler api and richer protocols.

```Bash
https://github.com/ithewei/libhv
```
```Bash
https://hewei.blog.csdn.net/article/details/113733758
```


Features
: * Cross-platform (Linux, Windows, macOS, Android, iOS, BSD, Solaris)
: * High-performance EventLoop (IO, timer, idle, custom)
: * TCP/UDP client/server/proxy
: * TCP supports heartbeat, reconnect, upstream, MultiThread-safe write and close, etc.
: * Built-in common unpacking modes (FixedLength, Delimiter, LengthField)
: * RUDP support: WITH_KCP
: * SSL/TLS support: (via WITH_OPENSSL or WITH_GNUTLS or WITH_MBEDTLS)
: * HTTP client/server (support https http1/x http2 grpc)
: * HTTP supports static service, indexof service, forward/reverse proxy service, sync/async API handler
: * HTTP supports RESTful, router, middleware, keep-alive, chunked, SSE, etc.
: * WebSocket client/server
: * MQTT client

## Compile and Build

Because I use Windows11,so I only introduce how to build with `CMake GUI`,
and because `CMake GUI` is so easy,so I skip it.

we only need `hv.dll` in `/bin/` and `/hv/` in `/include/`,if you want,
the `hv.lib` and `hv_static.lib` can also be stored.

## Link lib and include

new a directory under solution and named `include`,put `/hv/` in here.

new a directory under solution and named `lib`,put `hv.dll` or `hv.lib` and `hv_static.lib` here,put all here is also all right.

and in properties,`VC++ directories`'s `Library Directories`,fill the `/lib/`.
`Include Directories` put the `/include/`,or put the `include` in `Additional Include Directories`.

in `Linker`, `Additional Dependencies`,write the `hv.lib` here.

and put the `hv.dll` under the root directory(I forget this is VS auto or my manual operation).

After these things ,don't forget add existing items for .h in includes.

and the test code is :

```C++
#include "UdpClient.h"
#include "htime.h"
#include <iostream>

using namespace hv;

int main(int argc, char *argv[])
{
    std::cout << "Client\n";
    if (argc < 2)
    {
        printf("Usage: %s port\n", argv[0]);
        return -10;
    }
    int port = atoi(argv[1]);

    UdpClient cli;
    int sockfd = cli.createsocket(port);
    if (sockfd < 0)
    {
        return -20;
    }
    printf("client sendto port %d, sockfd=%d ...\n", port, sockfd);
    cli.onMessage = [](const SocketChannelPtr &channel, Buffer *buf)
    {
        printf("< %.*s\n", (int)buf->size(), (char *)buf->data());
    };
    cli.onWriteComplete = [](const SocketChannelPtr &channel, Buffer *buf)
    {
        printf("> %.*s\n", (int)buf->size(), (char *)buf->data());
    };
    cli.start();

    // sendto(time) every 3s
    cli.loop()->setInterval(3000, [&cli](TimerID timerID)
    {
        char str[DATETIME_FMT_BUFLEN] = {0};
        datetime_t dt = datetime_now();
        datetime_fmt(&dt, str);
        cli.sendto(str);
    });

    // press Enter to stop
    while (getchar() != '\n');
    return 0;
}
```

ensure there's no error.

Don't forget to modify the `command Arguments` in `Properties->Debugging`,this only represent port,so fill it with four
numbers like "5678".

Here's all configuration,enjoy it!

## Protocol Buffer

This will be more complex but a little.

In directory `/include/`,we need to put `/google/` `/handler/` `/generated/` here,and two .h `protorpc.h` `router.h`.
don't forget to put the `protorpc.c` under the root directory and add it to project.And in `/lib/`,we also need to put some
protocol lib here,include `libprotobuf.dll` ` libprotobuf.lib` `libprotoc.dll` `libprotoc.lib`.

In project `properties`,in C++ `additional include directorie`s,we can put the include and its son files here.

In `linker input`,`addition dependencies` ,add the `libprotobuf.lib` and `libprotoc.lib`.'

Don't forget to add a line of code in `*.pb.h`,like this:

```C++
#ifndef GOOGLE_PROTOBUF_INCLUDED_base_2eproto
#define GOOGLE_PROTOBUF_INCLUDED_base_2eproto
#define PROTOBUF_USE_DLLS
#include <limits>
#include <string>
```

All right, in this way,we complete configuration.

The test server code :

```C++
/*
 * proto rpc server
 *
 * @build   make protorpc
 * @server  bin/protorpc_server 1234
 * @client  bin/protorpc_client 127.0.0.1 1234 add 1 2
 *
 */

#include "TcpServer.h"

using namespace hv;

#include "protorpc.h"
#include "router.h"
#include "handler/handler.h"
#include "handler/calc.h"
#include "handler/login.h"

protorpc_router router[] = {
    {"add", calc_add},
    {"sub", calc_sub},
    {"mul", calc_mul},
    {"div", calc_div},
    {"login", login},
};
#define PROTORPC_ROUTER_NUM  (sizeof(router)/sizeof(router[0]))

class ProtoRpcServer : public TcpServer {
public:
    ProtoRpcServer() : TcpServer()
    {
        onConnection = [](const SocketChannelPtr& channel) {
            std::string peeraddr = channel->peeraddr();
            if (channel->isConnected()) {
                printf("%s connected! connfd=%d\n", peeraddr.c_str(), channel->fd());
            }
            else {
                printf("%s disconnected! connfd=%d\n", peeraddr.c_str(), channel->fd());
            }
            };
        onMessage = handleMessage;
        // init protorpc_unpack_setting
        unpack_setting_t protorpc_unpack_setting;
        memset(&protorpc_unpack_setting, 0, sizeof(unpack_setting_t));
        protorpc_unpack_setting.mode = UNPACK_BY_LENGTH_FIELD;
        protorpc_unpack_setting.package_max_length = DEFAULT_PACKAGE_MAX_LENGTH;
        protorpc_unpack_setting.body_offset = PROTORPC_HEAD_LENGTH;
        protorpc_unpack_setting.length_field_offset = PROTORPC_HEAD_LENGTH_FIELD_OFFSET;
        protorpc_unpack_setting.length_field_bytes = PROTORPC_HEAD_LENGTH_FIELD_BYTES;
        protorpc_unpack_setting.length_field_coding = ENCODE_BY_BIG_ENDIAN;
        setUnpack(&protorpc_unpack_setting);
    }

    int listen(int port) { return createsocket(port); }

private:
    static void handleMessage(const SocketChannelPtr& channel, Buffer* buf) {
        // unpack -> Request::ParseFromArray -> router -> Response::SerializeToArray -> pack -> Channel::write
        // protorpc_unpack
        protorpc_message msg;
        memset(&msg, 0, sizeof(msg));
        int packlen = protorpc_unpack(&msg, buf->data(), buf->size());
        if (packlen < 0) {
            printf("protorpc_unpack failed!\n");
            return;
        }
        assert(packlen == buf->size());
        if (protorpc_head_check(&msg.head) != 0) {
            printf("protorpc_head_check failed!\n");
            return;
        }

        // Request::ParseFromArray
        protorpc::Request req;
        protorpc::Response res;
        if (req.ParseFromArray(msg.body, msg.head.length)) {
            printf("> %s\n", req.DebugString().c_str());
            res.set_id(req.id());
            // router
            const char* method = req.method().c_str();
            bool found = false;
            for (int i = 0; i < PROTORPC_ROUTER_NUM; ++i) {
                if (strcmp(method, router[i].method) == 0) {
                    found = true;
                    router[i].handler(req, &res);
                    break;
                }
            }
            if (!found) {
                not_found(req, &res);
            }
        }
        else {
            bad_request(req, &res);
        }

        // Response::SerializeToArray + protorpc_pack
        protorpc_message_init(&msg);
        msg.head.length = res.ByteSize();
        packlen = protorpc_package_length(&msg.head);
        unsigned char* writebuf = NULL;
        HV_ALLOC(writebuf, packlen);
        packlen = protorpc_pack(&msg, writebuf, packlen);
        if (packlen > 0) {
            printf("< %s\n", res.DebugString().c_str());
            res.SerializeToArray(writebuf + PROTORPC_HEAD_LENGTH, msg.head.length);
            channel->write(writebuf, packlen);
        }
        HV_FREE(writebuf);
    }
};

int main(int argc, char** argv) {
    if (argc < 2) {
        printf("Usage: %s port\n", argv[0]);
        return -10;
    }
    int port = atoi(argv[1]);

    ProtoRpcServer srv;
    int listenfd = srv.listen(port);
    if (listenfd < 0) {
        return -20;
    }
    printf("protorpc_server listen on port %d, listenfd=%d ...\n", port, listenfd);
    srv.setThreadNum(4);
    srv.start();

    while (1) hv_sleep(1);
    return 0;
}
```

