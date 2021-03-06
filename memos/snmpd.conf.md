# net-snmp で独自で追加した監視項目を入れる方法

* zabbixとかで監視できない場合において、snmpで独自で指定した内部の情報を取りたい。

* 拡張可能MIBというものがあり、 ```shell``` とかであれば、普通に ```exec``` 等で可能。

```
.1 -- index
    以下にリストされた「ディレクティブ」に対するテーブルのインデックス番号。 
.2 -- name
    指定されたテーブルエントリの名前。 これは他と重ならない方が良いが、重なっても構わない。 
.100 -- errorFlag
    このテーブルエントリにエラーが見つかった場合に、 整数 1 または 0 を返すフラグ。 
.101 -- errorMsg
    上記の errorFlag を引き起こすエラーを説明する「表示文字列」。 
.102 -- errorFix
    このエントリが SNMPset から整数 1 で、 かつ上で定義されている errorFlag が 1 の場合、 上記のテーブルエントリ名を引き数として プログラムやスクリプトが実行される。 実行されるプログラムはコンパイル時に config.h ファイルで設定される。 
```

* 例

```
root@webtest1:~ # tail -1 "/usr/local/share/snmp/snmpd.conf"
exec test /root/test.sh
exec httpd /root/test2.sh
```

* 結果

```
root@webtest1:~ # snmpwalk -On -v 2c -c sakura-open 127.0.0.1 1.3.6.1.4.1.2021.8.1
.1.3.6.1.4.1.2021.8.1.1.1 = INTEGER: 1
.1.3.6.1.4.1.2021.8.1.1.2 = INTEGER: 2
.1.3.6.1.4.1.2021.8.1.2.1 = STRING: test
.1.3.6.1.4.1.2021.8.1.2.2 = STRING: httpd
.1.3.6.1.4.1.2021.8.1.3.1 = STRING: /root/test.sh
.1.3.6.1.4.1.2021.8.1.3.2 = STRING: /root/test2.sh
.1.3.6.1.4.1.2021.8.1.100.1 = INTEGER: 0
.1.3.6.1.4.1.2021.8.1.100.2 = INTEGER: 0
.1.3.6.1.4.1.2021.8.1.101.1 = STRING: This is test
.1.3.6.1.4.1.2021.8.1.101.2 = STRING: 869
.1.3.6.1.4.1.2021.8.1.102.1 = INTEGER: noError(0)
.1.3.6.1.4.1.2021.8.1.102.2 = INTEGER: noError(0)
.1.3.6.1.4.1.2021.8.1.103.1 = STRING:
.1.3.6.1.4.1.2021.8.1.103.2 = STRING:
```

