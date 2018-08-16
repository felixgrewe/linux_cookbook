# How to install QIIME on our linux server #
Version 0.1 (not tested - use at your own risk)

If not already done, you need to install Anaconda:
~~~
cd ~
wget https://repo.anaconda.com/archive/Anaconda2-5.2.0-Linux-x86_64.sh
bash Anaconda2-5.2.0-Linux-x86_64.sh
source ~/.bashrc
~~~

Install QIIME with Anaconda:
~~~
conda create -n qiime1 python=2.7 qiime matplotlib=1.4.3 mock nose -c bioconda
~~~

Start QIIME by activating the QIIME environment:
~~~
source activate qiime1
~~~

Finish your work in QIIME by deactivating the QIIME environment:
~~~
source deactivate
~~~
