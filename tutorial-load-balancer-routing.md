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
  20. **Available subnets**では少なくとも２つのsubnetを選択し、**Actions**列でアイコンを選び。**Selectd subnets**で
