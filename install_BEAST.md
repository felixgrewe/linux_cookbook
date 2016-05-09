# How to install BEAST on our linux server #
Version 0.1 (not tested - use at your own risk)

You need to install a copy of BEAST into your local folder to run it on the server:
~~~
cd ~
mkdir -p /programs
cd programs
wget https://github.com/beast-dev/beast-mcmc/releases/download/v1.8.3/BEASTv1.8.3.tgz
tar zxvf BEASTv1.8.3.tgz
~~~

To start BEAST:
~~~
java -jar ~/programs/BEASTv1.8.3/lib/beast.jar yourinputfile.xml
~~~
or 
~~~
java -jar ~/programs/BEASTv1.8.3/lib/beast.jar -help
~~~
for more information.
