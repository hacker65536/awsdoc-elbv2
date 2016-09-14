#Tutorial: Use Path-Based Routing with Your Application Load Balancer
URL pathに基いてリクエストの転送ルールが入っているlistenerの作成ができる。(path-based routing) path-based routingを使用して複数のback-endにトラフィックを転送できる。例えば、通常のリクエストを一つのターゲットグループに振り分け、イメージのレンダーリクエストを別のターゲットグループに振り分けができる。

##Before you Begin
* VPCでEC2インスタンスを立ち上げる。health checkのポートとListenerのポートを許可したsecurity groupを割り当てる。より詳細の情報は[Target Security Groups](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-register-targets.html#target-security-groups)
* 登録予定のEC2インスタンスのマイクロサービスがデプロイされているか確認する。