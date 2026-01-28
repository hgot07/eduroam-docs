---
layout: page
title: ""
<!-- permalink: /tech/:basename -->
---

RADIUSクライアントからの認証をテストする方法を簡単にまとめています。  
以下 "TEST@EXAMPLE.AC.JP","PASSWORD","SECRET" はそれぞれユーザ名, パスワード, RADIUS 共有鍵で置き換えてください。

1. radtest
----------

# radtest [T](mailto:test@t.eduroam.jp)[EST@EXAMPLE.AC.JP](mailto:EST@EXAMPLE.AC.JP) PASSWORD 127.0.0.1 0 SECRET

2\. eapol\_test (wpa\_supplicant)
---------------------------------

\# eapol\_test -a 127.0.0.1 -s SECRET -c CONFIG\_FILE

CONFIG\_FILEの中身の例(EAP-MSCHAPv2)

| network={  <br>ssid="eduroam"  <br>eapol\_flags=255  <br>key\_mgmt=WPA-EAP  <br>eap=PEAP  <br>identity="TEST@EXAMPLE.AC.JP"  <br>\# anonymous\_identity="anonymous@EXAMPLE.AC.JP"  <br>password="PASSWORD"  <br>phase2="autheap=GTC"  <br>} |
| --- |
| network={  <br>ssid="eduroam"  <br>eapol\_flags=255  <br>key\_mgmt=WPA-EAP  <br>eap=PEAP  <br>identity="TEST@EXAMPLE.AC.JP"  <br>\# anonymous\_identity="anonymous@EXAMPLE.AC.JP"  <br>password="PASSWORD"  <br>phase2="autheap=GTC"  <br>} |
| --- |

CONFIG\_FILEの中身の例(EAP-TLS)

| network={  <br>ssid="eduroam"  <br>eapol\_flags=255  <br>key\_mgmt=WPA-EAP  <br>eap=TLS  <br>identity="TEST@EXAMPLE.AC.JP"  <br>\# anonymous\_identity="anonymous@EXAMPLE.AC.JP"  <br>private\_key="証明書ファイル名.p12"  <br>private\_key\_passwd="証明書パスワード"  <br>phase2="autheap=GTC"  <br>} |
| --- |
| network={  <br>ssid="eduroam"  <br>eapol\_flags=255  <br>key\_mgmt=WPA-EAP  <br>eap=TLS  <br>identity="TEST@EXAMPLE.AC.JP"  <br>\# anonymous\_identity="anonymous@EXAMPLE.AC.JP"  <br>private\_key="証明書ファイル名.p12"  <br>private\_key\_passwd="証明書パスワード"  <br>phase2="autheap=GTC"  <br>} |
| --- |

EAPでは、トンネルの外でやりとりされるユーザIDを匿名にすることができますが、匿名を指定してテストしたい場合はanonymous\_identityの定義を有効にして利用します。

3\. radtest (Windows)
---------------------

[http://www.freeradius.org/](http://www.freeradius.org/) のツールをダウンロードして利用。

インストール先のフォルダの中の bin にある radtest.txt と radtest.bat を必要に応じて編集。

radtest.txtの中身の例

| User-Name = "TEST@EXAMPLE.AC.JP"  <br>User-Password = "PASSWORD" |
| --- |
| User-Name = "TEST@EXAMPLE.AC.JP"  <br>User-Password = "PASSWORD" |
| --- |

  

radtest.batの中身の例

| radclient.exe -d ..\\etc\\raddb -f radtest.txt -x -s 127.0.0.1 auth SECRET |
| --- |
| radclient.exe -d ..\\etc\\raddb -f radtest.txt -x -s 127.0.0.1 auth SECRET |
| --- |