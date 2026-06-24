# CMDandWeb-Messaging

A small self-hosted messaging server with both a web client and a command-line client. It is designed to run on modern Windows systems while still offering client options for older machines, including Windows 9x/ME and MS-DOS 6.22.

The server runs on Node.js, and users can connect through:

- The browser-based web client
- The Node.js command prompt/terminal client
- A legacy Windows 95/98/ME C client
- An MS-DOS 6.22 client built with Watt-32

## Features

- Public chat with all connected users
- Direct messages
- Group chats
- Friends
- Basic account registration
- Saved previous sessions
- Web client accessible from other devices on your network
- Legacy command-line clients for older operating systems

## Compatibility

| Component | Supported systems | Notes |
| --- | --- | --- |
| Server | Windows XP through Windows 11, depending on Node.js availability | Requires Node.js v5.12.0 or newer |
| Web client | Any device with a compatible browser | Served by the Node.js server |
| Node.js terminal client | Systems that can run Node.js v5.12.0 or newer | Recommended for Windows XP and newer |
| Legacy Windows client | Windows 95, 98, ME, and Windows 2000 | Built from `legacy-client-win9x.c` |
| MS-DOS client | MS-DOS 6.22 | Requires a packet driver and Watt-32 |

Windows 9x cannot run modern Node.js, so use the legacy C client on those systems. MS-DOS does not include TCP/IP or Winsock, so the DOS client uses a packet driver plus the Watt-32 TCP/IP library.

## Requirements

- Node.js v5.12.0 or newer
- npm
- A configured `config.json`
- TCP/IP networking between the server computer and client computers

For optional legacy builds:

- Visual C++ 6.0, Open Watcom, or MinGW for the Windows 9x/ME client
- Open Watcom and Watt-32 for the MS-DOS client

## Quick Start

1. Edit `config.json`.
2. Install dependencies:

   ```bat
   npm install
   ```

3. Start the server:

   ```bat
   node server.js
   ```

   Or use:

   ```bat
   run-server.bat
   ```

4. Start the command-line client:

   ```bat
   node client.js
   ```

   Or use:

   ```bat
   run-client.bat
   ```

5. Open the web client in a browser using the configured web host and port.

   Example:

   ```text
   http://192.168.1.100:8080
   ```

## Configuration

The server and command-line client both read settings from `config.json`. Configure this file before starting the server.

Example:

```json
{
  "serverPort": 5190,
  "serverHost": "192.168.1.100",
  "clientHost": "192.168.1.100",
  "clientPort": 5190,
  "webPort": 8080,
  "webHost": "192.168.1.100"
}
```

Use the server computer's LAN IP address for `clientHost` when connecting from another computer. Do not use `localhost` unless the client and server are running on the same machine.

## Building EXE Files

On Windows 10 or Windows 11, you can build executable versions of the server and Node.js command-line client with:

```bat
build.bat
```

This installs dependencies and runs the npm build script.

You can also build manually:

```bat
npm install
npm run build
```

Windows XP through Windows 8.1 users may need to run the JavaScript files directly with `run-server.bat` and `run-client.bat`, because the full build process may not complete correctly on those operating systems.

## Windows 95/98/ME Client

The Windows 9x/ME client is built from:

```text
legacy-client-win9x.c
```

Build it on a machine with Visual C++ 6.0, Open Watcom, or MinGW:

```bat
"build-legacy-client-win9x&win2000.bat"
```

Then copy these files to the Windows 9x/ME computer:

- `legacy-client-win9x.exe`
- `config.json`

The server can stay on a modern computer.

The legacy client reads `clientHost` and `clientPort` from `config.json`. You can also provide the host and port directly:

```bat
legacy-client-win9x.exe 192.168.1.100 5190
```

Make sure TCP/IP and Winsock are installed on the Windows 9x/ME machine.

### Manual Windows 9x Build Commands

If `build-legacy-client-win9x&win2000.bat` does not run correctly in `COMMAND.COM`, run one of these commands from the project folder instead.

Visual C++ 6.0:

```bat
cl /O2 /W3 /DWIN32 legacy-client-win9x.c /link /subsystem:console wsock32.lib /out:legacy-client-win9x.exe
```

Open Watcom:

```bat
wcl386 -q -bt=nt -l=nt legacy-client-win9x.c wsock32.lib -fe=legacy-client-win9x.exe
```

MinGW:

```bat
gcc -O2 -Wall -o legacy-client-win9x.exe legacy-client-win9x.c -lwsock32
```

## MS-DOS 6.22 Client

The DOS client source is:

```text
dosclient.c
```

It builds to:

```text
DOSCHAT.EXE
```

The DOS client reads `DOSCHAT.CFG` instead of `config.json` because MS-DOS 6.22 uses 8.3 filenames.

To build with Open Watcom:

1. Install and build Watt-32.
2. Set `WATT_ROOT` to your Watt-32 folder.
3. Run:

   ```bat
   build-dos.bat
   ```

If your Watt-32 build uses a different library filename or memory model, edit the library path in `build-dos.bat`.

After building, copy these files to the MS-DOS computer:

- `DOSCHAT.EXE`
- `DOSCHAT.CFG`

Edit `DOSCHAT.CFG` so `HOST` points to the LAN IP address of the computer running `server.js`:

```text
HOST=192.168.1.100
PORT=5190
```

On the MS-DOS computer, load your network card packet driver and make sure Watt-32 can find `WATTCP.CFG`. Then run:

```bat
DOSCHAT
```

You can also override the config file from the command line:

```bat
DOSCHAT 192.168.1.100 5190
```

## Network Notes

- Use the server computer's LAN IP address for local network clients.
- Open or allow the configured ports in your firewall if other devices cannot connect.
- Keep the server on a modern machine when using Windows 9x or MS-DOS clients.
- Avoid using a public IP address unless you understand the security risks of exposing the server to the internet.

## Security Notes

This project has very basic account registration and authentication. It is best used for local networks, retro-computing experiments, and personal testing. Do not treat it as a hardened public chat service without reviewing and improving the security model first.
