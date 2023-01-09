SQLiteRet
=========
**Recover deleted data from SQLite databases**

  SQLiteRet is a Python3 script which goes through a SQLite database file and dumps all intact deleted rows it finds. Optionally, it attempts retrieval of partial (corrupted) rows and exports to file.

Usage:
---------
`sqliteret.py file [--corrupted] [--nostrict] [--output outputFile] [--tab | --raw] [--verbose] [--help]`

* --corrupted, -c 

  **Corrupted rows**: selecting this option, the program will try to partially retrieve rows which contain corrupted data; the option is most useful to read through deleted string (corrupted rows make up to 80% of deleted rows), but please bear in mind that the reliability of these results is limited as these row can never be recovered with accuracy. Since primary keys are not available, they're outputted as 0.
  Due to corruption, each partial row might be read in different ways and a group of rows is outputted istead of a single row. 
  In output, each group is separated by an additional new-line.
  

* --nostrict, -ns

  **Strict and non strict mode**: normally, the program runs in strict mode, i.e. it assumes a common use of table types, which means *int* type tables actually store integers, *char* type tables actually store strings and so forth. However, SQLite allows for more flexible table types, e.g. an integer can be stored in a *char* type table. If you suspect a non regular use of table types, select the --nostrict option. 
Note: running this option raises the odds of false hits.


* --output outputFile, -o outputFile

  **Output files**: select an output file. Recommended types are .tsv and .txt.
If not specified, results will be printed to sdout.
Note: use this option instead of output redirection, since the program may require user interaction.


* --tab, -t | --raw,-r

  **Output modes**: tab mode outputs each row as a tab-separated list of values. It is recommended for .tsv files.
raw mode outputs each row as a Python tuple. It is recommended for stdout and .txt files.
Defaults to raw.


* --verbose, -v

  **Verbose mode**: prints additional information during execution.

User interaction:
-----------------
User interaction might be required if a database page is found to belong to more than one possible table. 
The program will ask to choose between the possible schemas presenting an example of a row decoded to each schema.


Usage example:
-------------
`sqliteret.py cookies.sqlite -c -o results.tsv -t -v`

  Sample output:
![alt sample output](http://s3.postimg.org/3s8leoflv/two.png "Sample output")

`sqliteret.py cookies.sqlite -c -o results.txt`

  Sample output:
![alt sample output](http://s8.postimg.org/pocz34c4l/one.png "Sample output")


Note:
----- 
SQLiteRet relies on the principle that deleted data can be found in the file's free (unallocated) space. Therefore, the chance of recovering data from databases which have been fully vacuumed and defragmented is minimal.


不root获取db文件
--------------
https://github.com/greycodee/wechat-backup  
https://www.pansafe.com/a/news/2016/0909/138.html  
https://github.com/nelenkov/android-backup-extractor/releases  

* 获取db文件,adb version>=1.0.31
```
adb version

adb devices
adb shell pm uninstall –k com.tencent.mm
adb install -r -d weixin5.apk
adb backup –f ./wx.ab com.tencent.mm
```
* 解包
```
java -jar abe.jar unpack wx.ab wx.tar.gz
tar -zxvf wx.tar.gz
```
* 获取DB解密密钥
 ```
 Android数据库密码一般是手机IMEI+微信UIN 两部分md5后取前7位
 
IMEI： 
    拨号盘输入 *#06# 可以获取（双卡手机可能有3个， 可以挨个尝试下，另外有人说IMEI改成了，1234567890ABCDEF，经测试我这里不是）
UIN:
    搜索auth_info_key_prefs.xml文件，取_auth_uin的值，符号也要
    
 ```
 
* 解密EnMicroMsg.db，WxFileIndex.db
```
docker run --rm -v $(pwd):/wcdb  greycodee/wcdb-sqlcipher -f DB名字 -k 解密密钥
```
* 从索引库恢复被删除的消息(FTS5IndexMicroMsg.db）  
 https://www.bilibili.com/read/cv15864412  
 ```
 微信版本<7
 python3 sqliteret.py FTS5IndexMicroMsg.db
 ```

  
