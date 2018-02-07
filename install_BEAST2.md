# How to install BEAST2 on our linux server #
Version 0.1 (not tested - use at your own risk)

You need to install a copy of BEAST (here version 2.4.3) into your local folder to run it on the server:
~~~
cd ~
mkdir -p /programs
cd programs
wget https://github.com/CompEvol/beast2/releases/download/v2.4.3/BEAST.v2.4.3.Linux.tgz
tar zxvf BEAST.v2.4.3.Linux.tgz
~~~

To start BEAST:
~~~
~/programs/BEAST.v2.4.3/bin/beast -beagle_off yourinputfile.xml
~~~
or 
~~~
~/programs/BEAST.v2.4.3/lib/beast -help
~~~
for more information.

To start BEAST with multiple cores (here 4) type:
~~~
~/programs/BEAST.v2.4.3/bin/beast -beagle_off -threads 4 yourinputfile.xml
~~~
