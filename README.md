# IPXRouter
A simple IPX-over-UDP emulator and router, designed for playing IPX LAN games over Internet. Tested on Red Alert 2.

## Architecture
IPXRouter is implemented in two parts: client and server. 

The client part is a `wsock32.dll` that handles IPX emulation, converts IPX to UDP and communicates with server. The server part is a golang application that listens on a UDP port and forwards packet among clients. 

The client part is modified modified from [ipxemu](http://ipxemu.sourceforge.net/).

## Usage
Download the client and the server from GitHub Actions build artifacts. 

The server is built in both Linux and Windows versions (add `.exe` suffix to `server` for Windows). Just run the binary, no configuration is required. Use `-p` parameter to specify UDP port number (the default port number is 8899); for verbose logging, use `-v`.You only have to run one server for all clients.

Put the client `wsock32.dll` to your game directory. For wine users, you have to set a DLL override rule `wsock32(native)`. You must specify the server address by creating a `ipxemu-config.txt` on the game directory, containing two lines: the first line is the server domain name or IP address; the second line is the port number.

After client and server is ready, just launch the game and enjoy.

## How it works
The client prepends IPX source and destination node number and socket number before payload data, and then sends them to the server. Packets from server also include the node and socket numbers that the client interprets and tells the game. The node number is generated by the client upon entry. IPX network number is fixed to zero.

For each incoming packet, the server records the correspondence of source node+socket number and UDP address. Then, for unicast packets, the server forwards the packet to UDP address corresponding to the destination node+socket number; for broadcast packets, the server forwards the packet to all clients on that socket number.

.
