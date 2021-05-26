# How to install BEAST2 on our linux server #
Version 0.2

You need to install a copy of BEAST (here version 2.6.4) into your local folder to run it on the server:
The location for finding the latest version is at https://github.com/CompEvol/beast2/releases
~~~
cd ~
mkdir -p /programs
cd programs
wget https://github.com/CompEvol/beast2/releases/download/v2.6.4/BEAST.v2.6.4.Linux.tgz
tar zxvf BEAST.v2.6.4.Linux.tgz
~~~

To start BEAST:
~~~
~/programs/beast/bin/beast yourinputfile.xml
~~~
or 
~~~
java -jar ~/programs/beast/lib/launcher.jar input.xml -help
~~~
for more information.

To start BEAST with multiple cores (here use 4) type:
~~~
~/programs/beast/bin/beast -threads 4 yourinputfile.xml
~~~
