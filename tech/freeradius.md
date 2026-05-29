---
layout: page
title: FreeRADIUS 3 によるRADIUSサーバ構築
---

# 改訂履歴

2015.6.29 初出  
2015.7.1 更新  
2015.7.31 更新  
2017.2.7 更新  
2020.7.22 更新  
2022.8.22 更新  
2023.3.8 更新  
2025.2.21 更新  
2026.5.26 更新 (3.2.8対応)  

[FreeRADIUS](https://www.freeradius.org/) 3.2 を用いて eduroam 対応の RADIUS サーバ (proxy および IdP 機能) を構築する方法について説明します。

* * *

想定
--

* 本ドキュメントは FreeRADIUSの **3.2.8** を対象に記述。
    > [!WARNING]  
    > FreeRADIUS 3.2.7 以前にはTLSの処理が不安定になる不具合があるため、必ず 3.2.8 以降を使う必要があります。
* 機関ドメイン example.ac.jp のトップレベルのサーバ(RADIUS proxy)の設定を想定。小規模なIdPとしても利用可能とする。
* 認証方式は EAP-TTLS / Microsoft PEAP両用とする。
* FreeRADIUSのファイル群は `/usr/local/freeradius/3.2.8/` 以下にインストールする。設定ファイルは `/usr/local/freeradius/3.2.8/etc/raddb/` に入る。
* サーバ証明書は FreeRADIUS が bootstrap で自動的に作成するが、 設定後に正規のものに入れ替える (IdPのみ)。

# FreeRADIUSの導入

1. FreeRADIUSのビルドにopenssl, tallocなどのライブラリが必要なので、OSにあらかじめ開発用のパッケージを導入しておきます。
2. FreeRADIUS のソースパッケージを以下のサイトからダウンロードします。  
   [https://www.freeradius.org/](https://www.freeradius.org/)
3. ダウンロードしたファイルを展開し、ビルド後、インストールします。
   ```
   $ tar zxf freeradius-server-3.2.8.tar.gz
   $ cd freeradius-server-3.2.8
   $ ./configure --prefix=/usr/local/freeradius/3.2.8
   $ make
   # make install
   ```
   `make` までは一般ユーザで構いませんが、`make install` は root 権限で行ってください。

# FreeRADIUSの設定

1.  設定ファイルのテンプレート[raddb-3.2.8-eduroamJP.tgz](raddb-3.2.8-eduroamJP.tgz)をダウンロードします。
2.  どこか適当なディレクトリで、テンプレートを展開します。このテンプレートを参考に、raddb ディレクトリにあるファイルを書き換えます。
    ```
    # cd /usr/local/freeradius/3.2.8/etc
    # tar zxpf raddb-3.2.8-eduroamJP.tgz
    ```
    最低限、変更が必要なファイルは、以下のとおりです。設定の注意点も示します。
    * **radiusd.conf**  
    認証ログを残すために `auth = yes` とする必要があります。
    * **proxy.conf**  
    元のファイルをテンプレートのファイルで上書きしてください。  
    ファイル中の `<JP proxyX addr>` と `<JP proxyX secret key>` に、eduroam 接続時にeduroam JP事務局から通知されるIPアドレスと共通鍵をそれぞれ設定してください。  
    realmの example を自機関名に書き換えてください (2個所 + nonexistent仮想サーバのメッセージ部分)。eduroam JPのサーバとの間でループが生じないように、機関レルム(例: example.ac.jp) に末尾がマッチするレルムはすべて、機関側のRADIUS proxyで終端する必要があります。  
    機関内の別のRADIUS IdPに認証要求を転送する場合は、IdP1/IdP2の設定例を参考にしてください。  
    オプション `status_check = status-server` は、相手サーバの死活監視を効率よく行うためのものです。FreeRADIUSを含む多くのRADIUSサーバがこの機能に対応していますが、もし機関内の認証サーバ(RADIUS IdP)との接続がうまくいかない場合は、このオプションを外してみてください。
    * **clients.conf**  
    元のファイルをテンプレートのファイルで上書きしてください。  
    ファイル中の `<JP proxyX addr>` と `<JP proxyX secret key>` に、eduroam JP事務局から通知されるIPアドレスと共通鍵をそれぞれ設定してください。  
    機関内の無線LANコントローラや、他のRADIUS proxyも、このファイルに記述することでアクセス許可されます。
    * **mods-available/eap**  
    [IdPのみ] FreeRADIUSが自動的に作成したサーバ証明書で動作確認の後、正規のサーバ証明書を用意して、ファイル中の `private_key_password`, `private_key_file`, `certificate_file` を適宜書き換えて下さい (proxyの場合は書き換え不要)。  
        * `private_key_password`: サーバ証明書の秘密鍵にパスワードが付いている場合はそれを指定する。パスワード無しの場合は空文字。
        * `private_key_file`: 秘密鍵のファイルを指定。
        * `certificate_file`: **サーバ証明書と、Root CA証明書に至るまでのすべての中間証明書**を連結したもの (=フルチェイン) を指定する。Root CA証明書は含めないこと。
        * `ca_file`: EAP-TLS認証を使わない場合は変更不要。EAP-TLSを使う場合は、**クライアント認証に使うプライベートCAのRoot CA証明書を指定**する。  
        **注意: クライアント認証にパブリックCAを使うべきではありません (eapファイルにコメントあり)。  
        `ca_file`には、パブリックCAのRoot CA証明書を絶対に含めないようにしてください。もしRoot CA証明書が含まれると、同じパブリックCAから発行された他人の証明書で、EAP-TLSの認証が成功して不正利用の恐れがあります。**
        * `ca_path`: `ca_file`と同様だが、使わないなら削除を推奨。
    * **mods-config/files/authorize**  
    基本的には空にします。テストアカウントや少人数のアカウントは、このファイルに記述できます。``users``としてシンボリックリンクがあります。  
    * **policy.d/filter**  
    FreeRADIUS 3.x系の一部に、`reject mixed case` という(不正な)ルールが有効になっているものがあります。この処理がコメントアウト（無効化）されていることを確認してください。FreeRADIUS 3.2.8では最初から無効になっているはずです。(eduroamのアカウントは、ユーザ名が case sensitive (大文字小文字を区別する)、レルム名が case insensitive (大小区別しない)です。)


# サーバ認証に関する注意事項

* **「サーバ認証」は、ID・パスワードを偽の基地局から保護するために必要なものです。端末によってはサーバ認証を無効にできることがありますが、これは危険なので、必ずサーバ認証を有効させるように利用者に指示してください (推奨は、geteduroamなどで端末の設定を自動化することです)。**  
* サーバ認証において、パブリックCAから発行された証明書を用いた場合、プライベートのCA証明書を端末に導入する煩雑さを回避できます。UPKI電子証明書発行サービスなどのサーバ証明書が利用できます。  
* パブリックCAを利用する場合、サーバ証明書のドメイン名を確実に確認できるように、端末設定する必要があります (geteduroamのような自動設定を推奨)。


# 動作確認

1. テスト用のアカウントを登録します (mods-config/files/authorizeに書き込む)。
2. debugモードでradiusサーバを起動します。
   ```
   # /usr/local/freeradius/3.2.8/sbin/radiusd -fxxl stdout
   ```
    * rootで実行してください。
    * オプション `-fxxl stdout` を付けているので、デーモンではなく通常のプロセスとして動作します。
3. パスを通した後、テスト用コマンドを実行します。
   ```
   # export PATH=$PATH:/usr/local/freeradius/3.2.8/bin
   # radtest ユーザ名@example.ac.jp パスワード localhost 1 testing123
   # radtest -t mschap ユーザ名@example.ac.jp パスワード localhost 1 testing123
   # radtest -t mschap ユーザ名@example.ac.jp パスワード localhost:18120 1 testing123
   ```
    * radiusサーバの起動に失敗した場合には設定を見直してください。
    * テスト用コマンドで認証が成功すると"Access-Accept"と表示されます。
    * 最後のradtestはinner-tunnelの確認のためのもので、もしこれが認証失敗する場合は、実際の端末からの認証が失敗することになります。
    * 正常動作が確認できたら、オプション `-fxxl stdout` を付けないで `radiusd` を起動します。システム起動時に `radiusd` が立ち上がるように、OSのスタートアップファイルに追記します (使用しているOSやディストリビューションによって設定方法が異なります)。
4. radtestコマンドでは、サーバ認証やEAPの試験ができません。eapol_testコマンドを使った動作確認も行ってください。eapol_test コマンドは、wpa_supplicant の一部として提供されていることが多いでしょう。
    * eap-test.confのような名前でテキストファイルを作成します。OSにバンドルされている証明書ストアの位置は、ディストリビューションによって調整してください。ドメイン名はサーバ証明書のCN/SANに合わせてください。
   ```
   # EAP-TTLS (MSCHAPv2)の場合
   network={
       ssid="eduroam"
       key_mgmt=WPA-EAP
       eap=TTLS
       anonymous_identity="anonymous@example.ac.jp"
       identity="ユーザ名@example.ac.jp"
       password="パスワード"
       ca_cert="/etc/ssl/certs/ca-bundle.crt"
       domain_suffix_match="rad1.example.ac.jp"
       phase2="autheap=MSCHAPV2"
   }
   ```
    * 次のようにコマンドを実行します。SUCCESSになれば認証成功です。  
    もし結果が FAILURE になって、`domain_suffix_match=` を外すと SUCCESS になるような場合は、サーバ認証の設定に間違いがあります。
   ```
   # eapol_test -c eap-test.conf -s testing123
   ```

5. 動作確認後は、必ずテスト用アカウントを削除して、`radiusd` を再起動してください。
    
# eduroam JPプロキシ接続時の注意事項

eduroam JPのトップレベルRADIUS proxyに接続する際に、機関側の設定不足によって認証連携がうまく動作しない例が散見されます。事務局の負担軽減のため、問い合わせの前に以下の点を確認、修正するようにお願いします。
* **RADIUSサーバ上のFirewallでRADIUSの通信がブロックされていないか?**  
  OSのFirewallの設定で、RADIUSプロトコルが使用するポート(1812/udp)を開けてください。
* **機関のFirewallでRADIUSの通信がブロックされていないか?**  
  機関のFirewallの設定で、RADIUSプロトコルが使用するポート(1812/udp)を開けてください。
* **正しい共通鍵を使用しているか?**  
  RADIUSのサーバソフトウェアまたはアプライアンスによって、共通鍵の最大長や、利用できる字種に違いがあります。ご利用の環境に合った鍵を申請書に記入してください。
* **レルムの転送処理が正しいか?**  
  自機関のレルムの付いた認証要求をeduroam JPプロキシに転送すると、動作不安定の原因になります。特に正規表現の利用には十分に注意し、このような不正な転送が行われないようにしてください。  
  負荷軽減のため、レルム無しの認証要求をeduroam JPプロキシに転送しないでください。
