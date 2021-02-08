---
title: IP and UDP
unit: Drone Backend
order: 2
---

Let's get your drone in the air. This is a little more complex than it sounds. The drones are connected via WIFI to a wireless router. You will need to connect to your drone via this router and send commands using the UDP protocol.

## IP Addresses

The Internet Protocol (IP) is used for addressing devices that are networked together. While you are likely familiar with IP Addresses, this video will help us understand what the numbers represent.

{% include responsive.html source="https://www.youtube.com/embed/LIzTo6e4FgY" %}

## Port Numbers

Servers/Computers often run several applications that need to listen for incoming messages. Port numbers are a way for the server to know whichto send a particular message to as it arrives. 

{% include responsive.html source="https://www.youtube.com/embed/RDotMcs0Erg" %}

## TCP and UDP

The transport layer of our network dictacts how messages are broken down and sent. We will be using the User Datagram Protocol (UDP) to communicate with the drone.

{% include responsive.html source="https://www.youtube.com/embed/cA9ZJdqzOoU" %}

![UPD Packets](https://media.geeksforgeeks.org/wp-content/uploads/UDP-header.png)
<small>Image Source: [https://www.geeksforgeeks.org/user-datagram-protocol-udp/](https://www.geeksforgeeks.org/user-datagram-protocol-udp/)</small>

## Putting It All Together

As we send message to the drone, it will flow through each layer of our network. Take a second to think about how the data will flow from our computer to the drone. Open the journal in Canvas and record your response to the following.

<div style="border: 1px solid #ccc; border-radius: 15px; padding: 0.5em 2em 1em 2em;">
  <p class="text-xl"><strong>✍️ Journal:</strong> How does data flow from our computers to the drone? Describe each step of the process.</p>
</div>

## Using Netcat to Send UDP Messages

So how do we send a message to the drones? Netcat is a TCP/IP tool that allows you to send/receive TCP/UDP packets. It is widely available and preinstalled on most UNIX based operating systems. Netcat sends/receives TCP packets by default, but it can also do UDP packets with the -u command line flag.

### Sender:

```bash
# Sending ‘hello world!’ as UDP packet(s) to localhost on port 12345.
$ echo 'hello world' | nc -u localhost 12345
```

### Receiver:
```bash
$ nc -ul 12345 # Tell netcat to listen to port 12345 for UDP packets
```
