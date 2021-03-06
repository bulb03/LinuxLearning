一開始想說很簡單，但沒想到踩了一堆地雷；最後是參考了這兩篇部落格(最底下)(其實不只這兩篇，但其他篇的在我的虛擬機上做都有問題)

在linux上安裝，絕不是跟windows一樣，一路下一步到底這麼簡單，一定要確認好哪些東西在哪裡，網路上的教學的指令到底是甚麼意思，都要弄清楚

註：此文章給使用python開發，但想要在Linux上開發的人比較適合

linux：cent os 7，minimal os

內建Python 版本：2.7.5，欲安裝3.6.3

註：裏頭的絕對路徑因人而異，請在輸入指令前確認好自己電腦的實際狀況

註：使用open suse的話，就用zypper或yast，而不是yum



以下是我自己的安裝過程：

前置作業
------------------
yum -y update => 更新作業系統
yum -y install make gcc gcc-c++ => 更新gcc，通常這都會需要更新
yum -y install zlib-devel => 這也要安裝，不然make install會失敗
(但是，我自己在別台電腦上試這指令時，有時會出現zlib-devel-1.2.7-17.e17.x86_64: [Errno 5] [Errno 2] No such file or directory)

下載最新版
-------------------
(2017/12/4)的時候，我選擇下載python 3.6.3
指令：**wget https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tar.xz**
   
解壓縮
------------------
注意：此檔案的副檔名為xz，因此，指令應該是：**tar -xJf Python-3.6.3.tar.xz**
   
   解壓縮完成後會出現目錄：Python-3.6.3
   
移至Python-3.6.3
--------------------
指令：**cd Python-3.6.3**

開始編譯
--------------------
注意：這裡就是linux和windows不同的地方(哪裡不同我就不多說了，自行google鳥哥)，指令：**./configure**

安裝
--------------------
指令：**make -> 按下enter-> 完成後 -> 按下enter -> make install**

到這裡為止，如果途中沒有發生什麼問題，python3.6.3應該安裝完成了，可以打python3來檢查，但你也會發現，我打python所出來的python版本依舊是2.7.5，
那要怎麼做才能讓python執行的是python2.7.5呢?

先整理一下到目前為止的狀態：centos中應該有python2.7(在/usr/bin/Python2.7) 和 python3.6.3(在/usr/local/bin/python3)，但同時也會發現，有東西是寫：

lrwxrwxrwx.  1 root root         9 Dec  4 10:28 python2 -> python2.7

-rwxr-xr-x.  1 root root      7136 Aug  4 08:40 python2.7

lrwxrwxrwx.  1 root root         7 Dec  4 10:28 python -> python2

這些又是甚麼?這些東西叫做連結，有點像windows的捷徑，所以當我們下"python"時，會連結到python2，python2再連到python2.7這檔案，所以，要怎麼讓下python
時執行的是python3.6.3呢?

複製並建立新連結
-------------------
   我們想要的，是打python時執行python3.6.3，但是，我們依舊得要保留舊的python，因為yum是依靠python2.7.5來運作；
   
   因此，我們先將/usr/bin/python用一個新的連結：python.bak取代，因為接下來要以/usr/bin/python重新建立連結(要先刪掉原本的/usr/bin/python，才能在創立新連結時再次使用/usr/bin/python)
   
   指令：**mv /usr/bin/python /usr/bin/python.bak**
   
   如此一來，就變python.bak指向python2了
   
   接下來，我們就要重新創造/usr/bin/python來指向python3了：
   指令：**ln -s /usr/local/bin/python3.6  /usr/bin/python**
   
   (-s 代表建立一個symbolic連結，差異請自行google鳥哥，我不想誤人子弟XD)
   
   到這裡為止，就算大功告成了，可以打python看看，看出來的python版本是不是3.6.3
   
   筆記：symbolic link和hard link最大的差異就在於即使hard link連結的檔案被刪掉，hard link本身也依舊有東西(就像是複製檔案到hard link裡一樣)
   symbolic link則不一樣，連結的檔案被刪掉後，它就沒有用了，從格式也可以發現：hard link其實是個檔案，symbolic link則是連結

修改yum
--------------------
   如上面提到，yum是依靠python2.7.5來運行，那他運行的依據就是在/usr/bin/裡的yum檔的第一行：#!/usr/bin/python
   我們只要把該行改成 #!/usr/bin/python2.7就可以了
   指令：vim yum (vim的使用也請自行google，這裡就不多提，如果出現錯誤，通常只要在root模式或是sudo vim yum即可，但後者需注意登入的帳號是否有系統管理員之權限)
   
   到這裡應該就沒有多大問題了，那為何我要寫這篇呢，因為我就是觀念不熟，所以常少做好幾步，我希望這篇可以幫助其他剛踏入Linux的人
   
參考網址
-------------
https://dotblogs.com.tw/shaynling/2017/10/12/170139

https://www.tecmint.com/install-python-in-linux/
