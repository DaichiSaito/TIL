# Chapter 6 - Webセキュリティと認証

### ○ Webセキュリティの３要素

1. 機密性(Confidentiality)
    - 許可していない第三者からのアクセスを遮断できているか
2. 完全性(Integrity)
    - 情報の改竄・消去などから守ることができているか
3. 可用性(Availability)
    - 必要なユーザーが情報にきちんとアクセスできるか

なお、情報セキュリティの保護にあたっては、
    - リスクを鑑みて、
    _ どのような脅威が想定され、
    - 標的とされるシステム上の脆弱性がないか、
具体的に考える必要性がある。

リスクが低いのであれば（例：アクセスも限られており、流出するような大した情報もない場合など）、
多少の脆弱性については目を瞑ってもよいかもしれない。

逆に、通常であれば堅牢とされるようなシステムであっても、情報流出の際のリスクが大きく、
様々な悪意のある第三者からの攻撃が予想されるのであれば、より一層の対策が必要になるかもしれない。

### ○ Webシステムへの様々な攻撃
#### ●　パスワードクラッキング

悪意の第三者がパスワードを取得し、代わってログインする行為を指す。
手法として、「辞書攻撃」や「ブルートフォース」などがある。

辞書攻撃は、辞書に登録してあるようなパスワードに使われている言葉を片っ端から
パスワードでないか確認していく方法である。

ブルートフォースは、Brute の文字がついているように「野蛮」な形で、
とにかく虱潰しに、全ての組み合わせのパスワードを片っ端から試していく方法である。

#### ●　Dos攻撃

Denial of Systemの略称。
直訳すると、システムの否定。Webサーバーなどが機能しないよう攻撃することを指す。

SYN Floodや、F5攻撃などがある。

TCP通信においては、まず通信が確立しているか確認するため、互いにSYNとACKパケットを事前に送り合う。
SYN Floodはその性質を利用し、SYNパケットを大量に送信することでサーバーを機能不全にし、
他のユーザーからのアクセスができないような状態へと陥れる。

F５攻撃は、多くのユーザーがF5の更新ボタンを押すことで、サーバーへのアクセスを集中させ、
同様にサーバーを機能不全にさせることを指す。

#### ●　セッションハイジャック

##### セッションIDとは（おさらい）

TCP通信はステートレスなやり取りである。
サーバーはどのようなユーザーからのアクセスがあったか記憶していない。

しかし、これではECサイトで買い物をするような場合、買い物カゴにあった情報などを継続して
保管することができないので不都合が生じてしまう。

そのため、cookieを使い、セッションが終わるまで、ユーザー情報を保存することがある。
その際に、サーバーはセッションIDを発行し、どのような人が買い物をしたか記憶する。

##### セッションハイジャック

セッションIDを何らかの方法で盗み取り、代わって乗っ取ってしまうこと。
セッションIDを盗み取れば、ユーザーIDやパスワードが無くとも、悪さを働くことができる。

#### ●　ディレクトリトラバーサル

 Directory Traversal。ディレクトリを越えた横断を意味する。
 ディレクトリを越えて、Webシステムの管理者が意図していない領域にまでアクセスし、
 重要な情報を入手・改竄などをする行為を指す。

#### ●　クロスサイトスクリプティング(XSS)

Cross Site Scriptingの略称。
CSSとせずXSSとしているのは、あのCSSと混同しないようにするため。
脆弱性のあるサイトを利用して、クライエントエンドのスクリプトを送信し、悪さを働くこと。

例：偽のサイトにアクセスさせ、そこでユーザーに勘違いさせて、ID・パスワードを入力させる。
　　その際に、スクリプトを利用して、ID・パスワードを秘密裏に送信させる。

#### ●　クロスサイトリクエストフォージェリ(CSRF)

Cross Site Request Forgeriesの略称。
Forgeryは偽造を意味する。
サイトを介して、サーバーに偽のリクエストを送信し、ログインしてしまう行為。

脆弱性のあるサイトを利用して、クライエントエンドのスクリプトを送信し、
そのユーザーを乗っ取るような形でアカウントがあるサイトにアクセスし、
ユーザの意思に反する形で情報発信や商品の購入などを行うことを指す。

例：ツイッターで意図していない犯罪予告メッセージが勝手に投稿されてしまう。

> 3分でわかるXSSとCSRFの違い
https://qiita.com/wanko5296/items/142b5b82485b0196a2da
Qiita記事 @@wanko5296

#### ●　SQL インジェクション

脆弱性を利用して、SQLのスクリプトを実行し、
管理者側が意図していない形でDBから機密情報を取得すること。

#### ●　ゼロデイアタック

Webシステムには、Webサーバー・APサーバー・DBサーバー・ファイヤウォール等のいずれかに、
何らかの脆弱性が存在してしまうことが多い。

権限がない第三者が情報にアクセス・操作できてしまう不具合のことをセキュリティホールという。

どのような脆弱性があるかについては、脆弱性データーベースにて情報公開されている。
システム管理者は、そのデータベースを確認することで利用システムのセキュティホールを知ることができる。

セキュティホールが発見された場合、速やかに修正プログラム（セキュティパッチ）が開発されるが、
こうしたプログラム発見前に、その脆弱性をついて攻撃することをゼロデイアタックという。

### ○ Webシステムを守るための技術
#### ●　パケットフィルターファイヤーウォール

Webにおいては、多くの第三者からのアクセスから攻撃が行われる。
これを防ぐため、クライアントからのアクセスを制限するファイアーウォールというものがある。

ファイヤーウォールにも様々な種類があるが、代表的なものとしてパケットフィルター型がある。
パケットフィルター型では、IPアドレスとポート番号にて、可能なアクセス・処理を制限してしまう。

#### ●　IDSとIPS

Intrusion Detection System の略称。
Intrusionとは侵略を意味し、Detectionとは検知を意味する。

IDSにより、不正なアクセスがあった際には管理者に通知が送られるため、
アクセスを遮断するなどの対応を取ることができる。

Intrusion  Prevention System の略称。
Preventionとは防止を意味する。

IPSにより、不正なアクセスがあった際には、自動でそのアクセスを遮断することができる。

IPSの方がより強固な防御ではあるが、誤検知してしまった場合、
本来アクセスできる人がアクセスできない状態となってしまい、利便性が低下してしまう。

IDSとIPSには、シグネチャー型とアノマリー型が存在する。

シグネチャー型は、登録型を意味する。
SYN Floodなどの攻撃があった場合、その特徴的な通信アクセスが登録データベースと一致していることが
検知されるため、アクセスを遮断するなどの対応を取ることができる。

アノマリー型は、異常検知型を意味する。
F5攻撃などがあった場合、異常なアクセス数を検知し、そのアクセスを遮断するなどの対応を取ることができる。

#### ●　WAF

Web Application Firewall の略称。

XSSやCSRFなどは、IDSやIPSなどで防ぐことができない。
そのため、想定しうる攻撃パターンに対応したファイヤーウォールを設定することでセキュリティを高める。

こうした攻撃パターンの想定及び対策を講じるのは大変であるため、この対策には費用がかかることが多い。

WAFには、ブラックリスト型とホワイトリスト型がある。
ホワイトリスト型は、許可していない通信以外は拒否するため、より堅牢なシステムとなる。
ただ、無用にアクセスを拒否しては利便性が失われるため、採用には高度な技術が求められる。

#### ●　暗号化

データが不正に入手されたとしても、そのデータが悪用されないよう暗号化することが重要である。
 - HTTPSでは通信が暗号化されており、暗号を解く鍵がないと情報の中身がわからないようになっている。
 - 通信だけでなく、サーバー上の重要データも暗号化することがリスク回避につながる。

また、パスワードのように一致の確認さえできればよいものであれば、
ハッシュ関数を用いて、ハッシュ化という処理を行うこともある。

ハッシュ化された情報は元に戻すことは困難であるので、不正に入手されても悪用されにくい。

#### ●　公開鍵証明書

アクセス先が真正なサイトであることを証明するため、公開鍵証明書が発行される。
公開鍵証明書は、認証局という第三者機関から発行される。

公開鍵証明書を発行することにより、銀行などのサイトでは、偽サイトで振り込みを行おうとした人が
パスワード等を漏洩してしまうリスクを減らそうと努力している。

#### ●　CAPTCHA

Completely Automated Public Turing test to tell Computers from Humans Apart の略称。
チューリングテストという、機械と人間を見分けるためのチューリングさんが考えたテストがある。
そのテストを完全にオートマチックに作成するプログラムのことを指す。

「（グネグネと波打った文字が出てきて）これは何と書いてあるでしょう」と答えさせるテストや、
ジグソーパズルのようなことをさせるテストなどがある。

### ○ APIを活用したログインや権限付与
#### ●　認証
ツイッターアカウントなどを利用して、ログインする仕組みを「認証」という。

API認証を行うことで、ユーザーはわざわざそのサイトでアカウント情報を登録する必要がなくなる。
ただし、開発者側はAPI認証に対して、それぞれ対応しなければならない。

こうした問題を解決できるのが、OpenIDというAPI認証のプロトコルである。
OpenIDは、複数のサービスによって利用されているものである。

ユーザーは、そのOpenIDを利用しているサービスのいずれかにアカウントを持っていれば、
アカウントを作成することなく、ログインできるようになる。

#### ●　認可
ログインだけでなく、権限付与についてもサービス間をまたいで連動させてしまうことを「認可」という。

例えば、認証だけの仕組みしか整っていない場合、ツイッターアカウントさえ持っていれば、
フェイスブックアカウントなくしてログインすることはできる。
しかし、「ログイン」という手順は踏まなければならない。

認可されていれば、ツイッターアカウントにログインした状態で、フェイスブックアカウントにも同時に投稿できる。

OAuthという認可だけのプロトコルや、認証と認可の仕組みをまとめた OpenID Connect がある。