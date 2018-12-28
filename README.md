deno-ws
===
An experimental websocket implementation for deno.ts

## Usage

!! Experimental !!

```ts
import {serve} from "https://deno.land/x/net/http.ts";
import {acceptWebSocket, isWebSocketCloseEvent, isWebSocketPingEvent} from "https://raw.githubusercontent.com/keroxp/deno-ws/master/ws.ts";

async function main() {
    console.log("websocket server is running on 0.0.0.0:8080");
    for await (const req of serve("0.0.0.0:8080")) {
        if (req.url === "/ws") {
            (async () => {
                const [err, sock] = await acceptWebSocket(req);
                if (err) return;
                console.log("socket connected!");
                for await (const ev of sock.receive()) {
                    if (typeof ev === "string") {
                        // text message
                        console.log("ws:Text", ev);
                        cosnt err = await sock.send(ev);
                        if (err) console.err(err);
                    } else if (ev instanceof Uint8Array) {
                        // binary message
                        console.log("ws:Binary", ev);
                    } else if (isWebSocketPingEvent(ev)) {
                        const [_, body] = ev;
                        // ping
                        console.log("ws:Ping", body);
                    } else if (isWebSocketCloseEvent(ev)) {
                        // close
                        const {code, reason} = ev;
                        console.log("ws:Close", code, reason);
                    }
                }
            })();
        }
    }
}


main();

```

## Author

[keroxp](https://github.com/keroxp)

## License

MIT