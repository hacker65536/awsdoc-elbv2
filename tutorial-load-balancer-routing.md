#Tutorial: Use Path-Based Routing with Your Application Load Balancer
URL pathに基いてリクエストの転送ルールが入っているlistenerの作成ができる。(path-based routing) path-based routingを使用して複数のback-endにトラフィックを転送できる。例えば、通常のリクエストを一つのターゲットグループに振り分け、イメージのレンダーリクエストを別のターゲットグループに振り分けができる。

##Before you Begin
* VPCでEC2インスタンスを立ち上げる。health checkのポートとListenerのポートを許可したsecurity groupを割り当てる。より詳細の情報は[Target Security Groups](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-register-targets.html#target-security-groups)
* 登録予定のEC2インスタンスのマイクロサービスがデプロイされているか確認する。

##Create your Load Balancer
**path-baseルーティングのロードバランサーの作成**
1. Webコンソールを開く
2. EC2インスタンスと同じリージョンをナビゲーションバーより選択
3. ナビゲーションパネルの**LOAD BALANCING**の下の**Target Groups**を選択
4. 下記の様にターゲットが最初にセットされるターゲットグループを作成する
  5. **Create target group**を選択
  6. name,プロトコル、ポート、VPCを設定し**Create**を選ぶ
  7. new target groupを選択
  8. **Targets**タブで**Edit**を選ぶ
  9. 一つやそれ以上のinstanceを選択し、**Add to regstered**を選んで、**Save**する<br/><br />
  注意: インスタンスのstatusは登録とhealth checkがパスされるまで*initial*である、そしてターゲットグループがロードバランサーからトラッフィクを受けるまで*unused*になる
10. ふたつ目のターゲットグループを下記のように作成する
  11. **Create target grpu**を選択
  12. name、protocol、ポート、VPCをを指定し、**Create**を選択
  13. **Targets**タブの**Edit**を選択
  14. 一つあるいはそれ以上のinstanceお選び、**Add to registered**を選び、**Save**する
11. ナビゲーションパネルの**LOAD BALANCING**の下の**Load Balancers**を選ぶ
12. **Create Load Balancer**を選ぶ
13. **Select Load balancer type**から**Application Load Balancer**を選ぶ
14. **Continue**を選ぶ
15. **Configure Load Balancer**ページを以下のように設定する
  16. **Name**にload balancerの名前を入力する<br /><br />
ALBの名前はリージョン内ではユニークである必要がある。最大で32文字、アルファベットとハイフンが使用可能。最初と最後にハイフンを使用することは出来ない。
  17. **Scheme** では、Internet-facing ロードバランサーはinternet上のクライアントからのリクエストを振り分ける。internal ロードバランサーはターゲットへの振り分けはプラベートIPを使用する。
  18. **Listeners**では、80番のHTTPをdefaultでは許可する。それ以外のプロトコルとポートに修正することができ、**Add**を選び別のlistenerを追加することが出来る。
  19. **VPC**では、使用したいEC2インスタンスと同じVPCを選択する
  20. **Available subnets**では少なくとも２つのsubnetを選択し、**Actions**列でアイコンを選びと**Selectd subnets**に移動する。Note Avaliability Zone毎に一つのsubentを選ぶ事ができる。もしAZから選んだsubnetがすでにあれば現在選択したsubnetに置き換えられる。
  21. **Next: Configure Security Settings**を選択
11. もし以前にSecure listenerを作成したことがあれば、次のように**Configure Security Settigns**を完成させる
  12. もしAWS Certificate Managerの証明書があれば、**Choose an existign certificate from AWS Certificate Manager(ACM)**を選び、**Certificatenme**から証明書を選ぶ
  13. もしすでにIAMを使用して証明書をアップロードしているのであれば、**Choose an existing certificate from AWS Identity and Access Management (IAM)**から証明書を選ぶ
  14. もしアップロード出来る証明書があれば**Upload a new SSL Certificate to AWS Identity and Access Management(IAM)**を選ぶ。**Certificate name**に証明書の名前を入力、**Private Key**にはPEM-encoded形式のprivate keyファイルの中身をコピーペースト、**Public Key Certificate**ではPem-encoded形式のPublic keyファイルの中身をコピーペースト、**Certificate Chain**にはPem-encoded形式のCertificate chain ファイルの中身をコピーペースト、自己署名証明書を使っていて、ブラウザは暗黙の内に証明書を受け入れる事が重要ではない限り。
  15. **Select Policy**では存在しているsecurity policyを一つ選ぶ
12. **Next: Configure Security Groups**を選ぶ
13. **Configure Security Groups**ページは以下のように設定する
  14. **Create a new security group**を選択
  15. nameとdescriptionを入力するかdefaultを使用する。この新しいsecurity groupは**Configure Load Balancer**ページで選択しているポートのトラフィックを許可するルールが含まれている
  16. **Next: Configure ROuting**を選ぶ
14. **Configure Routing**を以下のように設定する
  15. **Target group**ではすでにあるtarget groupを選ぶ
  16. 作ったターゲットグループから一つ目のターゲットグループを選ぶ
  17. **Next: Register Targets**を選択
15. **Register Targets**ページではターゲットグループに登録したインスタンスが**Registered instances**下に表示される。wizardを完了させるまで登録されたターゲットの変更はできない。**Next: Review**を選択
16. **Review**ページで**Create**を選択
17. ロードバランサーの作成成功が通知されたら**Close**を選択
18. 新規作成されたロードバランサーを選択
19. **Listeners**タブでは矢印アイコンでlistenerのルールを見る。**Add rule**を選択する。ルールは以下のように指定できる
  20. **Target group name**では作った二つ目のターゲットグループを選択
  21. **Path pattern**ではpath-based routingのパターンを指定する(e.g. /img/*)
  22. **Save**を選ぶ