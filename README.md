# traceRoute-with-Golang
implementation of traceroute in Go


Traceroute is a tool that can be used to track the route that a packet takes from a source to a destination. It works by sending packets with increasing time-to-live (TTL) values, and then measuring the time it takes for the packets to reach their destination or be returned as "time exceeded" messages.

Here is a basic implementation of traceroute in Go:

Copy code

   ``` package main

    import (
        "fmt"
        "net"
        "os"
        "time"
    )

    func main() {
        target := os.Args[1]
        fmt.Println("Tracerouting", target)

    for i := 1; i <= 30; i++ {
        conn, err := net.Dial("udp", target+":33434")
        if err != nil {
            fmt.Println("Failed to connect:", err)
            return
        }
        defer conn.Close()

        conn.SetDeadline(time.Now().Add(time.Second))

        _, err = conn.Write([]byte{1, byte(i), 0, 0, 0, 0, 0, 0})
        if err != nil {
            fmt.Println("Failed to send data:", err)
            return
        }

        reply := make([]byte, 1024)
        _, addr, err := conn.ReadFrom(reply)
        if err != nil {
            fmt.Println("Failed to receive data:", err)
            return
        }

        fmt.Printf("%d: %s\n", i, addr.String())
    }
}

```
This program takes a single command-line argument, which is the target host to trace the route to. It then repeatedly sends UDP packets to the target with incrementing TTL values, and waits for a reply. The address of the host that replied is printed out along with the TTL value that was used.
