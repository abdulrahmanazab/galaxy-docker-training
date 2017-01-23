# Install Galaxy on CentOS 6.6

The default python version for CentOS 6.x is 2.6.x and Galaxy require Python 2.7 to start. Here is how to install Python 2.7 on CentOS 6.6.

In order to compile Python you must first install the development tools:

```bash
yum groupinstall "Development tools"
```
You also need a few extra libs installed before compiling Python or else you will run into problems later when trying to install various packages:
```bash
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel
```
Download, compile and install Python

The --no-check-certificate is optional
```bash
cd /usr/src
wget --no-check-certificate https://www.python.org/ftp/python/2.7.6/Python-2.7.6.tar.xz
tar xf Python-2.7.6.tar.xz
cd Python-2.7.6
./configure --prefix=/usr/local
make && make altinstall
```
