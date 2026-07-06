# remote-beyondatc
Run BeyondATC on a second PC. I wrote this up after finding tidbits of information and a little while trying to get this to work. The devs rightfully do not want to assist as this may imply ongoing support.

**This is completely unsupported and unafilliated to BATC.**

1. You'll need a [spoofed Flight Simulator executable](#MSFS-2024-Spoofer), as BATC checks for this on startup.
2. You need to [set up SimConnect to work over the network](#SimConnect).
3. You need the traffic models [available on the second PC](#Traffic-Models), accessible on the same path as on the MSFS PC.

> Tested working on BATC version 1.8.14.EA and MSFS 2024 SU5.1

> [!Warning]
> BATC do not support running their application on a second PC, they have made it very clear they will not offer any help at all so this is all at your own risk/enjoyment.
> 
> Please don't go to the devs with problems!
> 
> Your milage may vary - especially as the application is changing regularly in early access. You have been warned...


---

# MSFS 2024 Spoofer

A minimal Windows executable that idles indefinitely under the process name `FlightSimulator2024.exe`. Used for tricking BATC, which checks for a running MSFS 2024 instance before it will start. This may work with MSFS 2020 (rename to `FlightSimulator.exe`), I've not tested this.

The process uses zero CPU (blocked on `Sleep(INFINITE)`) and a few MB of RAM.

## Files

- `stub.c` -- source file
- `FlightSimulator2024.exe` -- if you just want to run it

## Using the pre-built binary

Download `FlightSimulator2024.exe` and run it on the BATC/second PC. It will appear in Task Manager under that name with no visible window. Kill it from Task Manager when you're done.

## Building it

Two options here, I used Visual Studio:

### Option A - Visual Studio

You need the **Desktop development with C++** workload installed.

1. Open **Developer PowerShell for VS**
2. Navigate to the folder containing `stub.c`:
   ```powershell
   cd C:\path\to\your\folder
   ```
3. Compile:
   ```
   cl /O2 /DNDEBUG stub.c /link /SUBSYSTEM:WINDOWS /OUT:FlightSimulator2024.exe
   ```
4. `FlightSimulator2024.exe` will appear in the same folder.

### Option B - Tiny C Compiler

TCC should also work, it is a very small download with no installer:
1. Download from https://repo.or.cz/tinycc.git or mirrored on github at https://github.com/Tiny-C-Compiler/tinycc-mirror-repository
2. From a command prompt in the folder containing `stub.c`:
   ```
   tcc -Wl,-subsystem=windows stub.c -o FlightSimulator2024.exe
   ```

## Running it

No admin needed, just run it. The process has no window and no tray icon. As it uses basically no resources I have it run on system startup.

## Stopping it

If needed you can end it from Task Manager.

---
# SimConnect

https://docs.flightsimulator.com/html/Programming_Tools/SimConnect/SimConnect_SDK.htm

## On the MSFS PC

1. Find the `SimConnect.xml`:
- Steam Version: `%APPDATA%\Microsoft Flight Simulator 2024\SimConnect.xml`
- MS Store Version: `%LOCALAPPDATA%\Packages\Microsoft.Limitless_8wekyb3d8bbwe\LocalCache\SimConnect.xml`
2. Find the Static IPV4 port section, and change to:
  ```
    <SimConnect.Comm>
      <Descr>Static IP4 port</Descr>
      <Protocol>IPv4</Protocol>
      <Scope>global</Scope>
	  <Address>LOCAL_IP_ADDRESS_OF_THE_MSFS_PC</Address>
      <Port>500</Port>
      <MaxClients>256</MaxClients>
      <MaxRecvSize>41088</MaxRecvSize>
    </SimConnect.Comm> 
  ```

## On the BATC PC

> You'll need to re-do this step after every BATC update.

1. Create the file `SimConnect.cfg` in the BATC Folder, default install directory is `C:\BeyondATC`:
  ```
  [SimConnect]
  Protocol=IPv4
  Address=LOCAL_IP_ADDRESS_OF_THE_MSFS_PC
  Port=500
  ```
2. A copy of this file also needs to be made at `C:\BeyondATC\BeyondATC_Data\StreamingAssets`

---

# Traffic Models

1. Share the Community folder over the LAN. Windows NFS - google it - out of scope of this tutorial
2. Assume it is at `C:\dir\to\your\Community24` on the MSFS PC, and shared at `\\MSFS_PC\Community24` on the BATC PC. This needs symlinking to the same directory as the MSFS PC. In Command Prompt:
  ```
  mklink /D "C:\dir\to\your\Community24" "\\MSFS_PC\Community24"
  ```
