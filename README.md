# eclambda

eclambda is a set of tools 
Tools for computing Elliptic Curve interval discrete logarithms using Pollard's lambda algorithm, also known as the kangaroo algorithm.

There are two components, a client and a server. Multiple clients can work on the same problem and send their results to the server.

Limitations/things to improve

-Private key interval cannot be spciefied i.e. limited to an interval of [1, 2^n - 1] for an n-bit private key 
-The client stores distinguished points in RAM. They are lost of the program closes before they can be sent to the server.
-There are no options for tuning the server database

# client program

The client program performs the lambda algorithm on the GPU. Periodically it will sends the results to the server.
Before sending results to the server, progress is saved on disk so that no work will be lost in the event of a failure. For machines with multiple GPUs, multiple instances of the client can be run with a different device specified for each instance (see --list-devices and --device options).

The client displays the number of distinguished points (DP), total points (TP) and calculation speed (points per second). Note that DP and TP are
not updated until the client contacts the server. The client will contact the server every 3 minutes to check the job status and get the latest
count for DP and TP. DPs's are uploaded to the server every 10 minutes by default.


`--name NAME`
Job name

`--gpu-mem-usage N`
Specify percent of GPU memory to use for points e.g. 0.8 for 80%.

`--list-devices`
List available GPUs then exit

`--device N`
Specify the device to use (see --list-devices)

`--submit-interval N`
Submit points to the server every N minutes. The default is 10 minutes.

`--host HOST`
Specify the server name to connect to. Default is 127.0.0.1

`--port PORT`
Specify the port to connect on. Default is 5311.



# server program

The server accepts results from the client and stores them in a database. Job progress is written to a text file in the working directory. When
the solution is found it is also written to a file.

--working-dir DIR
Specify the working directory of the server. Default is the current directory.

--port PORT
Listen for connections on port PORT


The jobsubmit utility submits work to the server.

# jobsubmit program

`--host HOST`
Specify the server to connect to. Default is 127.0.0.1

`--port PORT`
Specify the port to connect on. Default is 5311.

`--name NAME`
Specify the name of the job. Required.

`--keylen LEN`
Length of the target private key in bits. Required.

`--pubkey PUBKEY`
The public key encoded in hexadecimal, either compressed or uncompressed. If no public key is given, it is automatically
generated.

`--dbits BITS`
The number of distinguished bits. The default is 18. 


# Example

Solving a 65-bit problem

Start the server

```
eclambda-server.exe
     ______ ______   __     ___     __  ___ ____   ____   ___
    / ____// ____/  / /    /   |   /  |/  // __ ) / __ \ /   |
   / __/  / /      / /    / /| |  / /|_/ // __  |/ / / // /| |
  / /___ / /___   / /___ / ___ | / /  / // /_/ // /_/ // ___ |
 /_____ / \____/ /_____//_/  |_|/_/  /_//_____//_____//_/  |_|
EC LAMBDA SERVER
VERSION 1.0 ALPHA
[2020-06-12.16:11:23] [Info] Database thread started
[2020-06-12.16:11:23] [Info] Server started
[2020-06-12.16:11:23] [Info] Waiting for connection...
```



Submitting a job to the server
```
jobsubmit.exe --name job65 --pubkey 028F75A32E657F80503BC904215711D9717AEA7376AF8B70A0A130FF56F370F7CA --keylen 65 --dbits 20 --host 192.168.0.123

EC LAMBDA SERVER JOB SUBMITTER
VERSION 1.0 ALPHA
Job name: testjob65
Public key: 028F75A32E657F80503BC904215711D9717AEA7376AF8B70A0A130FF56F370F7CA
Key length: 65 bits
Distinguished bits: 20
```


Running the client
```
eclambda.exe --name testjob65 --gpu-mem-usage 0.9 --device 1 --host 192.168.0.123
     ______ ______   __     ___     __  ___ ____   ____   ___
    / ____// ____/  / /    /   |   /  |/  // __ ) / __ \ /   |
   / __/  / /      / /    / /| |  / /|_/ // __  |/ / / // /| |
  / /___ / /___   / /___ / ___ | / /  / // /_/ // /_/ // ___ |
 /_____ / \____/ /_____//_/  |_|/_/  /_//_____//_____//_/  |_|
EC LAMBDA CLIENT
VERSION 1.0 ALPHA
[2020-06-12.16:15:01] [Info] Connecting to 192.168.0.123
[2020-06-12.16:15:02] [Info] Target public key:
[2020-06-12.16:15:02] [Info] X:8F75A32E657F80503BC904215711D9717AEA7376AF8B70A0A130FF56F370F7CA
[2020-06-12.16:15:02] [Info] Y:A4B836212E42F63C8CE2B899E46F44FA7792A730AF836520245A387C8AF93254
[2020-06-12.16:15:02] [Info] Distinguisher: 20 bits
[2020-06-12.16:15:02] [Info] Sending results to server every 10 minutes
[2020-06-12.16:15:02] [Info] Initializing gfx900
[2020-06-12.16:15:02] [Info] Compiling OpenCL kernels...
[2020-06-12.16:15:06] [Info] Initializing...
```




