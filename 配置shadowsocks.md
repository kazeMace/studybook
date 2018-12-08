## Install
Debian / Ubuntu:
```bash
apt-get install python-pip
pip install git+https://github.com/shadowsocks/shadowsocks.git@master
```
CentOS:
```bash
yum install python-setuptools && easy_install pip
pip install git+https://github.com/shadowsocks/shadowsocks.git@master
```
For CentOS 7, if you need AEAD ciphers, you need install libsodium
```bash
dnf install libsodium python34-pip
pip3 install  git+https://github.com/shadowsocks/shadowsocks.git@master
```
Linux distributions with snap:
```bash
snap install shadowsocks
```
Windows:

See [Install Shadowsocks Server on Windows](https://github.com/shadowsocks/shadowsocks/wiki/Install-Shadowsocks-Server-on-Windows).

## Usage
```bash
ssserver -p 443 -k password -m aes-256-cfb
```
To run in the background:
```bash
sudo ssserver -p 443 -k password -m aes-256-cfb --user nobody -d start
```
To stop:
```
sudo ssserver -d stop
```
To check the log:
```
sudo less /var/log/shadowsocks.log
```
Check all the options via -h. You can also use a [Configuration] file instead.

If you installed the snap package, you have to prefix the commands with shadowsocks., like this:
```
shadowsocks.ssserver -p 443 -k password -m aes-256-cfb
```
## Usage with Config File
[Create configuration file and run](https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File)

To start:
```
ssserver -c /etc/shadowsocks.json
```
## Config
Create a config file /etc/shadowsocks.json. Example:
```json
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```json
{
    "server":"my_server_ip(内网ip)",
    "local_address": "127.0.0.1",
    "local_port":1080,
    "port_password":{
	"8388":"password",
	"8389":"password",
	"8390":"password",
	"8391":"password",
	"8392":"password",
	"8393":"password",
	"8394":"password",
	"8395":"password"
	},
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}

```

| Name   |      Explanation      |
|----------|:-------------:|
| col 1 is |  left-aligned |
| col 2 is |    centered   |
| col 3 is | right-aligned |
|server|	the address your server listens|
|server_port|	server port|
|local_address|	the address your local listens|
|local_port|	local port|
|password|	password used for encryption|
|timeout|	in seconds|
|method|	default: "aes-256-cfb", see Encryption|
|fast_open|	use TCP_FASTOPEN, true / false|
|workers|	number of workers, available on Unix/Linux|

To run in the foreground:
```bash
ssserver -c /etc/shadowsocks.json
```
To run in the background:
```bash
ssserver -c /etc/shadowsocks.json -d start
ssserver -c /etc/shadowsocks.json -d stop
```