## 現場Rails Chapter07 「機能を追加してみよう」

---

<BR><BR>

### Chapter07-1 「確認画面を挟む」  

---

登録や編集の際に、確認画面を挟みたい。  
具体的には、以下のアクション間に、確認を行うconfirm_newアクションとconfirm_editアクションを挟みたい。  
（練習も兼ねて、現場railsでconfirm_newアクションだけのところ、今回はconfirm_editを追加したい）  

1. newアクションからcreateアクションの間（新規作成画面から登録処理まで）
2. editアクションからupdateアクションの間（編集画面から更新処理まで）

<br>

#### ルーティングについて検討する
---

該当箇所のルーティングを確認する。  

```
           Prefix Verb   URI Pattern                          Controller#Action
             root GET    /                                    tasks#index
            tasks GET    /tasks(.:format)                     tasks#index
                  POST   /tasks(.:format)                     tasks#create
         new_task GET    /tasks/new(.:format)                 tasks#new
        edit_task GET    /tasks/:id/edit(.:format)            tasks#edit
             task GET    /tasks/:id(.:format)                 tasks#show
                  PATCH  /tasks/:id(.:format)                 tasks#update
                  PUT    /tasks/:id(.:format)                 tasks#update
                  DELETE /tasks/:id(.:format)                 tasks#destroy
```

ルーティングについて、どこにconfirm_newとconfirm_editを所属させるか検討する。  
現場railsに従って、tasks/new/confirmにtasks#confirm_newアクションを作成したい。  
また、tasks/:id/confirmにtasks#confirm_editアクションを作成したい。  

<br>

#### 必要な作業を洗い出しする
---

では、confirm_newアクション関係の作業として、何を行う必要があるのか整理する。  
1. newアクションにて新規画面を表示
2. createアクションではなく、confirm_newアクションに飛ばすように変更（new画面のリンクを修正）
3. confirm_newの画面を表示（confirm_newの画面を作成、パラメータ格納についてコントローラに記入）
4. confirm_newにてエラー検証を行う（コントローラに記入）
5. エラーがある場合、newアクションに飛ばす。new画面にてエラーを表示（new画面にてエラー表示させるよう修正）
6. エラーがない場合、new画面で入力されたパラメータをcreateアクションに飛ばす（confirm_new画面のリンクを修正）

では、confirm_editアクションで関係の作業として、何を行う必要があるのか整理する。  
1. editアクションにて編集画面を表示
2. updateアクションではなく、confirm_editアクションに飛ばすように変更（edit画面のリンクを修正）
3. confirm_editの画面を表示（confirm_editの画面を作成、パラメータ格納についてコントローラに記入）
4. confirm_editにてエラー検証を行う（コントローラに記入）
5. エラーがある場合、editアクションに飛ばす。edit画面にてエラーを表示（edit画面にてエラー表示させるよう修正）
6. エラーがない場合、edit画面で入力されたパラメータをupdateアクションに飛ばす（confirm_edit画面のリンクを修正）

その他、DRY（Don't Repeat Yourself）に出来るところは、積極的に挑戦する。（ただし、無理はしない）  

<br>

#### エラーにハマって学んだこと
---

上記にて、ステップを細かく記載したが、当初は作業の洗い出しに漏れや誤りが発生した。  

そのこと自体は問題ではないが、そこから安易にコードの修正や情報検索ばかりに走ってしまい、  
結局、行き当たりばったりのトライアンドエラーの繰り返しになってしまった。

プログラムは、どこか数学や実験に似ている。  
少なくとも慣れるまでは、手順を踏んでコツコツとやるのが重要だ。  

自分の脳味噌には限界がある。
このことをよく肝に命じて、何をやっているかきちんと記録していくこと。  
（どうせ大概の場合、またエラーになるのだから）  

躍起になってガチャガチャやり過ぎて、自分を失ってしまう前に冷静になること。  
時間がかかりそうでも、分析して、全体の見取り図を作っていくこと。  

<br>

#### 参考にしていく・振り返るべき記事
----

チェリー本の著者である伊藤さんのQiita記事を発見したが、  
今後エラーに遭遇して、解決の見込みが立たなければ、即この記事を眺めること。  

[プログラミング初心者歓迎！「エラーが出ました。どうすればいいですか？」から卒業するための基本と極意（解説動画付き） \- Qiita](https://qiita.com/jnchito/items/056325421b7e36f02335)  

あと、改めてこちらの記事の偉大さを実感。  
具体的に書いてある。  

[RUNTEQの講師をやってみてわかった初学者にありがちなパターン20選（前編） \- Qiita](https://qiita.com/DaichiSaito/items/52448ebfcb0db768dcf3)  

<br>


#### ルーティングを設定する
---

まず、目標を確認。  

URL        tasks/new/confirm  
アクション   tasks#confirm_new  
パス名      confirm_new_task

URL        tasks/:id/confirm  
アクション   tasks#confirm_edit  
パス名      confirm_edit_task

なお、routes.rbには以下のとおり記載。  

```rb
# routes.rb
# 該当部分のみ記載

  resources :tasks do 
    post :confirm, action: :confirm_new, on: :new
    patch :confirm, action: :confirm_edit, on: :member, as: 'confirm_edit'
  end
```

なお、コードの記載にあたっては、以下を参照した。  

[Rails のルーティング \- Railsガイド](https://railsguides.jp/routing.html)  

[railsのroutes\.rbのmemberとcollectionの違いをわかりやすく解説してみた。〜rails初心者から中級者へ〜 \- Qiita](https://qiita.com/hirokihello/items/fa82863ab10a3052d2ff#comments)  

以下のとおり、ルーティング設定が完了した。  
この練習を通して、かなりルーティングについて分かってきた気がする！  

```
confirm_new_task   POST   /tasks/new/confirm(.:format)    tasks#confirm_new
confirm_edit_task  PATCH  /tasks/:id/confirm(.:format)    tasks#confirm_edit
```

<br>

#### 作業1・2・3・4
---

先ほど書いた作業リストの内、１〜３を行う。

confirm_new  
1. newアクションにて新規画面を表示
2. createアクションではなく、confirm_newアクションに飛ばすように変更（new画面のリンクを修正）
3. confirm_newの画面を表示（confirm_newの画面を作成、パラメータ格納についてコントローラに記入）
4. confirm_newにてエラー検証を行う（コントローラに記入）

confirm_edit  
1. editアクションにて編集画面を表示
2. updateアクションではなく、confirm_editアクションに飛ばすように変更（edit画面のリンクを修正）
3. confirm_editの画面を表示（confirm_editの画面を作成、パラメータ格納についてコントローラに記入）
4. confirm_editにてエラー検証を行う（コントローラに記入）

なお、confirm_newもconfirm_editも画面に違いがほぼないので、パーシャルを活用する。  

<br>

#### createではなくconfirm_new、updateではなくconfirm_editに飛ばす  
---

```rb
# new.html.slim
# 該当部分のみ記載
# formでのsubmit後のアクセス先について、以下のとおりconfirm_newアクションを指定

= form_with model: @task, local: true, url: {controller: 'tasks', action: 'confirm_new' } do |f|
```

<br>

```rb
# edit.html.slim
# 該当部分のみ記載
# formでのsubmit後のアクセス先について、以下のとおりconfirm_editアクションを指定

= form_with model: @task, local: true, url: {controller: 'tasks', action: 'confirm_edit' } do |f|
```

#### confirm_newとconfirm_editの画面を表示  
---

newもeditも、h1の内容を除いて違いはない。

```rb
# confirm_new.html.slim

h1 登録内容の確認

= render partial: 'confirm', locals: { task: @task }
```

```rb
# confirm_edit.html.slim

h1 修正内容の確認

= render partial: 'confirm', locals: { task: @task }
```

```rb
# _confirm_html.slim

= form_with model: task, local: true do |f|
  table.table.table-hover
    tbody  
      tr
        th= Task.human_attribute_name(:name)
        td= task.name
        # ユーザーから見えない形で決まった値を送るためにhidden_fieldを使う
        = f.hidden_field :name
      tr
        th= Task.human_attribute_name(:description)
        td= simple_format(task.description)
        # ユーザーから見えない形で決まった値を送るためにhidden_fieldを使う
        = f.hidden_field :description
  = f.submit '戻る', name: 'back', class: 'btn btn-secondary mr-3'
  = f.submit '登録', class: 'btn btn-primary'
```

<br>

#### パラメータ格納についてコントローラに記入（ついでにエラーの時の処理も追記）
---

```rb
# tasks_controller.rb

class TasksController < ApplicationController
  # :confirm_editを追加
  before_action :set_task, only: [:show, :edit, :update, :destroy, :confirm_edit]

  ~ 省略 ~

  def confirm_new
    # current_userのtasksで新しいオブジェクトを作成
    @task = current_user.tasks.new(task_params)
    # errorになればnewアクションに戻し、エラーを表示させる
    # errorについては後ほどコードを書く
    render :new unless @task.valid?
  end
  
  def confirm_edit
    # errorになればnewアクションに戻し、エラーを表示させる
    # errorについては後ほどコードを書く
    
    # @taskにTaskモデルから該当のidのものを格納しているので、paramsで書き換える
    # assign_attributesは、DBを更新せずに@taskを更新するメソッド
    @task.assign_attributes(task_params)
    render :new unless @task.valid?
  end

  private

  def set_task
    @task = current_user.tasks.find(params[:id])
  end
```

assign_attributesメソッドについては下記のとおり。  
[ActiveRecord の attribute 更新方法まとめ \- Qiita](https://qiita.com/tyamagu2/items/8abd93bb7ab0424cf084)  

<br>

#### 作業5・6
---

confirm_new
5. エラーがある場合、newアクションに飛ばす。new画面にてエラーを表示（new画面にてエラー表示させるよう修正）
6. エラーがない場合、new画面で入力されたパラメータをcreateアクションに飛ばす（confirm_new画面のリンクを修正）

confirm_edit
5. エラーがある場合、editアクションに飛ばす。edit画面にてエラーを表示（edit画面にてエラー表示させるよう修正）
6. エラーがない場合、edit画面で入力されたパラメータをupdateアクションに飛ばす（confirm_edit画面のリンクを修正）

<br>

#### new画面+edit画面にてエラーを表示させる
---

既にコントローラにて、new及びeditアクションに飛ばすコードは書いた。  
続いて、new画面及びedit画面にてエラーを表示する。  

```rb
# new.html.slim
# 該当部分のみ

h1 タスクの新規登録

# 以前は render 'form'として、formに関する部分をパーシャル化した
# form部分のパーシャル化が難しそうだったので、新たにerror等に関する部分をパーシャル化した
= render partial: 'error', locals: { task: @task }
```

```rb
# edit.html.slim
# 該当部分のみ

h1 修正内容の確認

# 以前は render 'form'として、formに関する部分をパーシャル化した
# form部分のパーシャル化が難しそうだったので、新たにerror等に関する部分をパーシャル化した
= render partial: 'confirm', locals: { task: @task }
```

```rb
# _error_html.slim

.nav.justify-content-end
  = link_to '一覧', tasks_path, class: 'nav-link'

# errorがあれば表示する
- if task.errors.present?
  ul#error_explanation
    - task.errors.full_messages.each do |message|
      li = message
```

<br>

#### エラーがなければ、confirm_newからcreate、confirm_editからupdateへ飛ばす
---

エラーがない場合、createアクションもしくはupdateアクションに飛ばす必要がある。  
（オブジェクトが空である場合はcreateアクション、空でない場合はupdateアクション）  

既にコードを記載のとおり、form_withの部分が以下のとおりとなっており、@taskの値を持ってどちらの  
アクションに飛ばすか自動で振り分けてくれるので、コードの追記・修正は不要である。  

```rb
# _confirm_html.slim
# form_withに関する部分のみ記載

= form_with model: task, local: true do |f|
```

以下の記事を参考にした。  

[【Rails】form\_with/form\_forについて【入門】 \- Qiita](https://qiita.com/snskOgata/items/44d32a06045e6a52d11c#22-form_with-model-model)  

<br>

#### 登録アクションで戻るボタンからの遷移に対応
---

戻るボタンを押した際の実装も追加する。  

このままだと登録ボタンも戻るボタンも区別されていないので、戻るボタンを押しても登録されてしまう。  
そこで、戻るボタンを押した場合の処理もコントローラに記載する。  

戻るボタンを押した場合、@taskにパラメータを格納して、newアクションをrenderする。  

```rb
# tasks_controller.rb
# 該当部分のみ記載

class TasksController < ApplicationController
  before_action :set_task, only: [:show, :edit, :update, :destroy, :confirm_edit]

  〜 省略 〜

  def create
    @task = Task.new(task_params.merge(user_id: current_user.id))

    # 以下のif部分を追加
    if params[:back].present?
      render :new
      # saveされては困るのでreturnを記載
      return
    end

    if @task.save
      logger.debug "task: #{@task.attributes.inspect}"
      redirect_to @task, notice: "タスク「#{@task.name}」を登録しました。"
    else
      render :new
    end

  end

  def update
    # 以下のif部分を追加
    if params[:back].present?
      render :edit
      return
    end

    @task.update!(task_params)
    redirect_to tasks_url, notice: "タスク「#{@task.name}」を更新しました。"
  end
```

<br>

#### 実装後の画面
---

新規タスク作成フローだけでなく、既存タスク修正フローにおいて、確認画面を挟むことができた。  
以下がその確認画面のスクリーンショットである。

なお、戻るボタンも実装され、以下のとおりとなった。
1. 「洗濯をすり」→「洗濯をする」として確認画面に遷移
2. 確認画面で戻るボタンを押す
3. 修正しようとした内容が引き継がれるので「洗濯をする」の内容が残っている

[![Image from Gyazo](https://i.gyazo.com/5453ee22f1ec47578f1446283291450a.png)](https://gyazo.com/5453ee22f1ec47578f1446283291450a)  

<BR><BR>

### Chapter07-2 「一覧画面に検索機能を追加する（Ransack）」  

---

Ransackというgemをインストールするため、Gemfileに書き加え、bundleを実行する。
実行した後、ransackメソッドが追加されるので、活用して検索機能を実装する。  

<br>

#### 名称による検索
---

手順についてまず確認する。  
1. search_formを作成し、検索データを飛ばせるようにする。
2. 検索データは、ransackメソッドがあるアクションに飛ぶ？  
（もしくは、index.html.slimでsearch_formを作ると、indexアクションに飛ぶようになっている？）  
3. 該当アクションにてパラメータを格納する。
4. そのパラメータがindex.html.slimで表示される。

謎が多いが、とりあえずはスルーすることとする。  

<br>

#### 検索フォームを作成する
---

Ransackの場合、search_form_forを使う。  
name_contとすると、nameに検索文字列を含むものを検索する。  

```
# index.html.slim
# 該当箇所のみ記述

= search_form_for @q, class: 'mb-5' do |f|
  .form-group.row
    = f.label :name_cont, '名称', class: 'col-sm2 col-form-label'
    .col-sm-10
      = f.search_field :name_cont, class: 'form-control'
  .fom-group
    = f.submit class: 'btn btn-outline-primary'
```

<br>

#### コントローラに追記する
---

ransackメソッドを使う。  

```rb
# tasks_controller.rb
# indexアクションのみ記載

  def index
    # ransackメソッドを使い、インスタンス変数qに検索値paramsが格納される
    @q = current_user.tasks.ransack(params[:q])
    # resultメソッドにより、検索結果が表示される
    # 検索していない時は、全てのデータが表示されるのは、<% %>で検索された結果が表示されているから？
    @tasks = @q.result(distinct: true).recent
  end

```

<br>

#### 実装結果
---

すごい！

<a href="https://gyazo.com/ff795ce9f9672cfeab31a361976cf494"><img src="https://i.gyazo.com/ff795ce9f9672cfeab31a361976cf494.gif" alt="Image from Gyazo" width="600" border=1/></a>  

<br>

#### 登録日時による検索
---

登録日時以降のタスクのみを表示する機能を実装する。  
gteqマッチャーを利用する。  

```
# index.html.slim
# 該当箇所のみ記述

= search_form_for @q, class: 'mb-5' do |f|

  ~ 名称での検索フォーム ~

  .form-group.row
    = f.label :created_at_gteq, '登録日時', class: 'col-sm2 col-form-label'
    .col-sm-10
     = f.search_field :created_at_gteq, class: 'form-control'
```

<a href="https://gyazo.com/04efe00dbaab44fe94ad6da4f3d954e6"><img src="https://i.gyazo.com/04efe00dbaab44fe94ad6da4f3d954e6.gif" alt="Image from Gyazo" width="600" border=1/></a>

<br>

#### 検索条件を絞る
---

現状のままだと、パラメータが加工されると、他のカラムを使った検索もできてしまう。  
そこで以下のような実装をモデルに追加するとよい。  

Strong Parametersを実装するのと同じ効果があるとのことだが、  
Strong Parametersで仕様変更に対応していくのは労力が大きいとのこと。  

```rb
# models/task.rb

  def self.ransackable_attributes(auth_object = nil)
    %w[name created_at]
  end

  def self.ransackable_associations(auth_object = nil)
    []
  end
```

<BR><BR>

### Chapter07-3 「一覧画面にソート機能を追加する」  

---

Ransackのヘルパーメソッドであるsort_lilnkヘルパーを使う。  
なお、ヘルパーが機能するように、登録日時順にタスクを並べるコントローラ上の設定を解除する。  
（具体的には、recentメソッドの削除）

```rb
# tasks_controller.rb
# indexアクションのみ記載

  def index
    @q = current_user.tasks.ransack(params[:q])
    # 最後の.recentを削除
    @tasks = @q.result(distinct: true)
  end

```
<br>

```
# index.html.slim
# 該当箇所であるth部分のみ記述

.mb-3
table.table.table-hover
  thead.thead-default
    tr
      th= Task.human_attribute_name(:name)
      
      # 元々は「th= Task.human_attribute_name(:created_at)」だった
      th= sort_link(@q, :created_at, default_order: :desc)
```

なお、名称と登録日時の両方についてソートできるような機能の実装も簡単にできるのかと思いきや、  
手順が複雑そうだったので、自作アプリ等にチャレンジする時の課題としたい。  

以下に、詳細な機能について記載があった。  

[Ransackで簡単に検索フォームを作る73のレシピ \- 猫Rails](http://nekorails.hatenablog.com/entry/2017/05/31/173925#%E3%82%BD%E3%83%BC%E3%83%88%E3%81%AE%E3%82%BB%E3%83%AC%E3%82%AF%E3%83%88%E3%83%9C%E3%83%83%E3%82%AF%E3%82%B9%E3%82%922%E3%81%A4%E7%94%A8%E6%84%8F%E3%81%99%E3%82%8B)  

<BR><BR>

### Chapter07-4 「メールを送る」  

---

<br>

#### メイラーのgenerate
---

Railsには、Action Mailer という仕組みがある。  
メールはコントローラに似ており、コントローラがテンプレートを通じて画面を出力するように、  
メイラーはテンプレートを通じてメールを作成・送信する。  

以下のコマンドで簡単にメイラーが作れる。

```
bin/rails g mailer TaskMailer
```

<br>

#### 実行手順
---

タスクを新規で作成した場合、メールが自動で送られるようにする。  
createアクションにて新規タスクはDBに保存されるが、その際に保存される  
インスタンス変数taskを持ってきて、メールのテンプレートに流し込む。  

これから行う作業は下記のとおりである。  

1. mailers/task_mailer.rbにコードを追加し、メイラーを実装する。
2. メイラーが起動した際に読み込むテンプレートを実装する。
3. メール送信処理をコントローラに実装する。


<br>

#### メイラーの実装
---

メイラーの実装は下記のとおりとなる。  

```rb
# mailers/task_mailer.rb

class TaskMailer < ApplicationMailer
  # アプリから複数種類のメールを送信する場合、どのメールのfromも以下のアドレスに固定する
  default from: 'taskleaf@example.com'

  # createアクションから、インスタンス変数である @task を持ってくる
  def creation_email(task)
    # メールのテンプレートで使うため、@taskに改めて格納する
    @task = task
    mail(
      subject: 'タスク作成完了メール',
      to: 'user@example.com',
    )
  end
end
```

<br>

#### テンプレートの実装
---

テンプレートのパスは、特に指定しない場合、メイラーのクラス名とメソッド名から推測される。  
今回は、cration_email.拡張子となる。  

受信者の環境に配慮して、HTML形式及びテキスト形式のメール、両方を用意する。  

```
# creation_email.html.slim
# HTML形式のメールテンプレート

| 以下のタスクを作成しました

ul
  li
    | 名称：
    = @task.name
  li
    | 詳しい説明：
    =simple_format(@task.description)

```

```
# creation_email.text.slim
# TEXT形式のメールテンプレート

| 以下のタスクを作成しました
= "`\n"
| 名称：
= @task.name
= "`\n"
| 詳しい説明：
= "`\n"
= @task.description

```

<br>

#### メール送信処理
---

メール送信処理を追加する。
コード１行を追加するのみである。  

```rb
# tasks_controller.rb
# creationアクションのみ記載

 def create
    @task = Task.new(task_params.merge(user_id: current_user.id))

    if params[:back].present?
      render :new
      return
    end

    if @task.save
      # 以下のコードを追加し、deliver.nowメソッドで即時送信するよう設定（deliver_laterというメソッドもある）
      TaskMailer.creation_email(@task).deliver_now
      redirect_to @task, notice: "タスク「#{@task.name}」を登録しました。"
    else
      render :new
    end

  end
```

<br>

#### 動作確認
---

mailcatcherというgemを使い、動作確認を行う。
mailmcatcherを使うと、シンプルなsmtpサーバーを立てて、  
送信されたメールをブラウザで確認できるようになる。  

```
# bundle からのインストールは正常に作動しないことがある
gem install mailcatcher
```

次に、config/environments/development.rbにてSMTPサーバー利用の設定を行う。  

```rb
# config/environments/development.rb
# 該当部分のみ記載

# Don't care if the mailer can't send.
config.action_mailer.raise_delivery_errors = false
# メーラー設定のため、以下２行を追記
config.action_mailer.delivery_method = :smtp
config.action_mailer.smtp_settings = { address: '127.0.0.1', port: 1025 }
```

そして、コマンドでmailcatcherを起動させ、Taskleafアプリを起動させる。  
新規タスクを登録すると、以下のとおりmailcatcherで送信メールを確認できる。  

<a href="https://gyazo.com/ab4e7598530b3afe94f1b42c8a2ce521"><img src="https://i.gyazo.com/ab4e7598530b3afe94f1b42c8a2ce521.png" alt="Image from Gyazo" width="600" border=1/></a>  
<a href="https://gyazo.com/b7f6dfd38317fbdebad3742051dc3ba1"><img src="https://i.gyazo.com/b7f6dfd38317fbdebad3742051dc3ba1.png" alt="Image from Gyazo" width="600" border=1/></a>  

<br>

#### メイラーのテスト
---

RSpecを使い、メイラーが想定どおり動いているか確認できるようにする。  

まず、ディレクトリを作成する。

```
mkdir spec/mailers
```

そして、RSpecの中身を記述する。  

```rb
# spec/mailers/task_mailer_spec.rb

RSpec.describe TaskMailer, type: :mailer do

  let(:task){ FactoryBot.create(:task, name: 'メイラーSpecを書く', description: '送信したメールの内容を確認します')}

  let(:text_body) do
    part = mail.body.parts.detect { |part| part.content_type == 'text/plain; charset=UTF-8' }
    part.body.raw_source
  end
  
  let(:html_body) do
    part = mail.body.parts.detect { |part| part.content_type == 'text/html; charset=UTF-8'}
    part.body.raw_source
  end

  describe '#creation_email' do
    let(:mail){ TaskMailer.creation_email(task) }

    it '想定どおりのメールが生成されている' do
      # ヘッダ
      expect(mail.subject).to eq('タスク作成完了メール')
      expect(mail.to).to eq(['user@example.com'])
      expect(mail.from).to eq(['taskleaf@example.com']) 

      # text形式の本文
      expect(text_body).to match('以下のタスクを作成しました')
      expect(text_body).to match('メイラーSpecを書く')
      expect(text_body).to match('送信したメールの内容を確認します') 

      # html形式の本文
      expect(html_body).to match('以下のタスクを作成しました')
      expect(html_body).to match('メイラーSpecを書く')
      expect(html_body).to match('送信したメールの内容を確認します')    
    end
  end
end
```

結果は下記のとおりとなった。

```
$ bundle exec rspec spec/mailers/task_mailer_spec.rb

Finished in 0.55547 s

econds (files took 3.2 seconds to load)
1 example, 0 failures
```

<BR><BR>

### Chapter07-5 「ファイルをアップロードしてモデルに添付する」  

---

タスクに画像ファイルを添付する。  
Rails5.2からActiveStorageが同梱され、クラウドストレージサービスへファイルをアップロードし、  
データベース上でActiveRecordモデルに紐づけることが簡単にできるようになった。 

Active Storageを使うためには、以下を実行し、マイグレーションファイルを作成する。  

```
bin/rails active_storage:install
```

すると、マイグレーションファイルには、以下の２つのテーブルを作成する内容が記述されている。  
- ActiveStorage::Blob
- ActiveStorage::Attachment

Blobは、画像データに対応するモデルであり、ファイル名・コンテンツタイプ、サイズなどを管理する。    
Attachmentは、ActiveBlobをアプリ内の他のモデルと関連付けするための中間テーブルを管理する。  

今回の場合、AttachmentはTaskモデルとBlobの間を取り持つことになる。  

マイグレーションファイルを実行し、DBに反映させる。  

```
$ bin/rails db:migrate
```

また、ファイル自体の管理の設定についてだが、以下のとおりとなっている。  
- config/environments/development.rb にて「Rails.application.config.active_storage.service」に  
ファイルを管理する場所の名前を与える。development環境において、デフォルトだとlocalが保存場所となっている。
- localがどこかという定義については、config/storage.ymlファイルにて記述されている。   

<br>

#### タスクモデルに画像を添付できるようにする
---

手順について確認する。  

1. Taskモデルに紐付けを行う。
2. new.html.slimにて画像アップロード用のフィールドを追記する。
3. edit.html.slimにて画像アップロード用のフィールドを追記する。
4. confirm_new.html.slimにて画像が表示されるようにする。
5. confirm_edit.html.slimにて画像が表示されるようにする。
6. show.html.slimにて画像が表示されるようにする。

<br>

#### Taskモデルに紐付けを行う
---

これだけらしい。  

```rb
# models/task.rb

  has_one_attached :image

```

なお、複数のイメージを紐付けたい場合、has_many_attachedとするらしい。  
[【Rails 5\.2】 Active Storageの使い方 \- Qiita](https://qiita.com/hmmrjn/items/7cc5e5348755c517458a)  

<br>

#### newとeditのhtml.slimにて画像アップロード用のフィールドを追記する  
---

いずれのコードにも以下を追加する。
new.html.slimの例を記す。

```rb
# new.html.slim

h1 タスクの新規登録

= render partial: 'error', locals: { task: @task }

= form_with model: @task, local: true, url: {controller: 'tasks', action: 'confirm_new' } do |f|
  .form-group
    = f.label :name
    = f.text_field :name, class: 'form-control', id: 'task_name'
  .form-group
    = f.label :description
    = f.text_area :description, rows: 5, class: 'form-control', id: 'task_description'
    # 以下の２行を追加
    = f.label :image
    = f.file_field :image, class: 'form-control'
  = f.submit '確認', class: 'btn btn-primary'

```

edit.html.slimについても、同一のコードを追加すること。
なお、tasks_controller.rb に strong params に :image を追加すること。


<br>

#### confirm_new, confirm_edit, showのhtml.slim画面にて、画像が表示されるようにする  
---

どちらもパーシャルである_confirm.html.slimをrenderしてきている。  
よって、_confirm.html.slimファイルを修正する。  

```
# _confirm.html.slim
# 該当部分のみ記載

tr
  th= Task.human_attribute_name(:image)
  td= image_tag @task.image if @task.image.attached?
```

また、show.html.slimについても同様の対応を行う。  

<br>

#### 実装後の画面
---

以下のとおり、show.html.slimを開くと表示される。  
また、確認画面においても表示されるようになった。  

<a href="https://gyazo.com/99d00ff3535be68c566f3242dc267e2d"><img src="https://i.gyazo.com/99d00ff3535be68c566f3242dc267e2d.png" alt="Image from Gyazo" width="600" border=1/></a>  

<BR><BR>

### Chapter07-6 「CSV形式のファイルのインポート／エクスポート」  

---

シンプルなCSV出力機能とCSV入力機能を実装する。  
実装には、Rubyのcsvライブラリを使用する。  

config/application.rbに以下のコードを加える。

```rb
require 'csv'
```
<br>

#### タスクをCSV出力する
---

models/task.rb ファイルに csv_attribute というクラスメソッドを追加する。  
また、generate_csv というクラスメソッドを追加する。  

これにより、モデルからcsvを出力できる。

```rb
# models/task.rb

# CSVデータにどの属性をどの順番で出力するかを定義
def self.csv_attributes
  ["name", "description", "created_at", "updated_at"]
end

def self.generate_csv
  # CSVデータの文字列を生成し、csv_attributesクラスメソッドの戻り値を出力する
  CSV.generate(headers: true) do |csv|
    # CSVの１行目としてヘッダを出力する。
    # csv_attributesクラスの属性値である"name"や"description"などを出力。
    csv << csv_attributes
    all.each do |task|
      csv << csv_attributes.map{|attr| task.send(attr)}
    end
  end
end
```

次に、generate_csvクラスメソッドを呼び戻すコントローラの実装を行う。  
tasks_controller.rbに以下のコードを加える。  

ここでは、CSV形式でファイルを実際に出力するコードを記載する。  

```rb
# tasks_controller.rb
# indexアクションのみ抜粋

def index
    @q = current_user.tasks.ransack(params[:q])
    @tasks = @q.result(distinct: true)

    # respond_to を使うと、求めている形式にてファイルを出力することができる。  
    # 今回はcsv形式だが、json形式などで返すこともできる。  
    respond_to do |format|
      format.html
      format.csv {send_data @tasks.generate_csv, filename: "tasks-#{Time.zone.now.strftime('%Y%m%d%S')}.csv"}
    end
  end
end
```

最後にcsvファイルをダウンロードできるボタンを実装する。  

```
# index.html.slim
# 末尾にこのコードを追加する

= link_to 'エクスポート', tasks_path(format: :csv), class: 'btn btn-primary mb-3'
```

<br>

#### 実装後
---

無事、以下のようなcsvファイルが出力できるようになった。  

<a href="https://gyazo.com/ea854176cf32d6de6b72848c2ea7794d"><img src="https://i.gyazo.com/ea854176cf32d6de6b72848c2ea7794d.png" alt="Image from Gyazo" width="600" border=1/></a>

<br>

#### タスクをCSV形式でインポートする
---

多少流れが複雑になるため、メモする。  
これを５番から１番という順番で行っていく。  
（１番→５番と考える方がイメージしやすいが、実装するとなると５番から始める方がやりやすいため）

1. index.html.slimにインポートボタンを実装
2. インポートボタンを押すと、importアクションに飛ぶ
3. importアクションに飛ぶよう、ルーティング設定を行う
4. importアクションを実装。Taskモデルのimportメソッドを使って、csv形式のタスク群をDBに登録する。  
5. Taskのimportメソッドを実装する。

<br>

#### Taskのimportメソッドを実装する
---

インポートメソッドを実装する。
CSVファイルをTaskクラスのattributesに合わせて取り込み、DBに保存する。  

```rb
# models/task.rb
# importメソッドのみ記載

  # csvをインポートするメソッド
  def self.import(file)
    # CSV ファイルを１行ずつ読み込む（headers: trueにより、１行目をヘッダとして無視する）
    CSV.foreach(file.path, headers: true) do |row|
      # クラスメソッドのためnewのみとなっているが、インスタンスメソッドの場合はTask.newとなる
      task = new
      # sliceというメソッドを使って、指定した安全なキーに対応するデータだけを取り出して入力に用いる
      # *csv_attributesは、csv_attributesメソッドの戻り値の配列内の要素をそれぞれ引数に指定する書き方
      task.attributes = row.to_hash.slice(*csv_attributes)
      task.save!
    end
  end
```

<br>

#### importアクションを実装（コントローラへの追記）
---

続いて、コントローラに追記する。  
Taskモデルのimportアクションを使用して、index.html.slimにリダイレクトするようにする。  

```rb
# tasks_controller.rb
# importアクションのみ記載

  def import
    current_user.tasks.import(params[:file])
    redirect_to tasks_url, notice: "タスクを追加しました"
  end

```
<br>

#### importアクションに飛ぶよう、ルーティング設定を行う
---

importアクションが実行されるよう、ルーティング設定を行う。  
resourcesの処理群の一つに加えたいので、collectionを使う。  

なお、memberは:idがつく場合、collectionは:idがつかない場合。  
[railsのroutes\.rbのmemberとcollectionの違いをわかりやすく解説してみた。〜rails初心者から中級者へ〜 \- Qiita](https://qiita.com/hirokihello/items/fa82863ab10a3052d2ff)  

```rb
# routes.rb
# 該当部分のみ記載

  resources :tasks do 
    post :import, on: :collection
    〜 省略 〜
  end
```

結果、以下のとおりルーティングが設定された。  

```
import_tasks POST   /tasks/import(.:format)   tasks#import
```

<br>

#### 1. index.html.slimにインポートボタンを実装（クリック→importアクションへ）
---

index.html.slimの末尾に以下のコードを追加する。  

```
# index.html.slim

# gemなどを使わ図に画像や写真をアップロードする場合、このmultipart: trueが必要
= form_tag import_tasks_path, multipart: true, class: 'mb-3' do
  = file_field_tag :file
  = submit_tag "インポート", class: 'btn btn-primary'
```

なお、以下のcsvファイルを取り込んでみる。  

```csv
name, description, created_at, updated_at
餃子を作る, 160個作る
餃子を冷凍する, 160個は食べきれないので冷凍する

# name => 餃子を作る, description => 160個作る
# created_atとupdated_at => nilなので取り込まれた日時を登録
```

無事、実装された。  

【インポートボタン】  
<a href="https://gyazo.com/1848baa9ffcb10f4d2d6c14bea53e0f5"><img src="https://i.gyazo.com/1848baa9ffcb10f4d2d6c14bea53e0f5.png" alt="Image from Gyazo" width="600" border=1/></a>  

【インポート後】  
<a href="https://gyazo.com/e508da6924ac9dcd32d5d561abac091e"><img src="https://i.gyazo.com/e508da6924ac9dcd32d5d561abac091e.png" alt="Image from Gyazo" width="600" border=1/></a>  


<BR><BR>

### Chapter07-7 「ページネーション」  

---

ページネーションとは、レコード件数が一定数を超えた場合に複数のページに分割して表示を行うようにすること。  
ページネーションを行うには、よくkaminariというgemが使われる。  

kaminariをGemfileに加えて、bundleする。  

<br>

#### ページ番号に対応する範囲のデータを検索するようにする
---

タスク一覧画面のindexアクションを変更する。  

```rb
# tasks_controller.rb

  def index
    @q = current_user.tasks.ransack(params[:q])
    # .page(params[:page])を追加した
    @tasks = @q.result(distinct: true).page(params[:page])
```

非常にシンプル。  

kaminariは、デフォルトだと1ページあたりにレコード件数を25件表示する設定となっている。
1番目~25番目のタスクを1ページ目とし、25~50番目のタスクを2ページ目・・・としてくれる。  
該当のページを開くと、そのページのparamsが格納され、indexアクションに引き渡してくれる。  

それが.page(params[:page])で実現する。  
すごい。。。  

<br>

#### ビューにページネーションのための情報を表示
---

これだけで良いらしい。  

```
# index.html.slim
# 該当部分のみ表示

.mb-3
  # 現在どのページにいるか表示し、他のページに移動するためのリンクも表示
  = paginate @tasks
  # 全データが何件なのか表示
  = page_entries_info @tasks
```

なお、日本語化する場合、kaminari.ja.ymlファイルを作成・追加する必要がある。  
面倒なので、万葉さんが公開しているファイルをそのまま使うことにする。。。  

<br>

#### デザインの調整
---

paginateヘルパーが利用するパーシャルテンプレートをアプリケーション内に用意し、  
それを自分でカスタマイズすることで、好きなデザインに調整することができる。  

パーシャルテンプレートはkaminariの提供するジェネレータで生成するが、  
フレームワークに合わせたテンプレートが既に用意されているため、該当のテーマを選択するとよい。  

[Github: amatsuda/kaminari\_themes](https://github.com/amatsuda/kaminari_themes)  

Githubで案内のとおり、以下のコマンドを入力してみる。  
全てのテーマを確認することができる。  

```
rails g kaminari:views
```

bootstrap4 を使っているため、bootstrap4 のパーシャルテンプレートを導入する。  

無事、実装が終了。  
恐ろしく簡単だった。  

<a href="https://gyazo.com/dba2b49aa2c7eeaf6cb961c745df3b57"><img src="https://i.gyazo.com/dba2b49aa2c7eeaf6cb961c745df3b57.png" alt="Image from Gyazo" width="600" border=1/></a>  

<br>

#### 表示件数を変更したいとき
---

1. perスコープで指定
2. per_pageを使って、モデルごとのデフォルトの表示件数を指定
3. kaminariの設定ファイルを使って全体的なデフォルト表示件数を指定

１の場合、pageメソッドにチョロっと追加するだけで済む。

```
# 10件ごとの表示に切り替えたい場合、最後に.per(10)とするだけ

@tasks = @q.result(distinct: true).page(params[:page]).per(10)
```

２の場合、モデルで「paginates_per 10」と追記する。  
このやり方は、当該モデル内のメソッド全てに適用される。  

３の場合、コマンドから「g kaminari:config」をして、生成ファイル内の該当箇所を変更するだけ。  

<BR><BR>

### Chapter07-8 「非同期処理や定期実行を行う」  

---

<dl>
  <dt>非同期処理</dt>
  <dd>重い処理を非同期処理として切り出し、アクションでは処理の受付だけを行うことで快適に操作できるようにすること</dd>
  <dt>定期実行</dt>
  <dd>「朝の９時に毎日実行」というように、日時をしてして処理すること</dd>
</dl>

なお、以上を行うために、Active Job というフレームワークを活用する。

<br>

#### 非同期処理ツールの導入
---

Active Jobは、非同期処理を行うツールではなく、共通のインターフェース（I/F）で扱うための仕組み。 
よって、非同期処理を行うため、Sidekiqを使う。（他、ResqueやDelayedJobなどがある）  

Sidekitdeは、キーバリュー型のデータベースRedisを利用する。  
・・・キーバリュー型。  

[NoSQL キーバリュー型のまとめ \- よしたく blog](https://yoshitaku-jp.hatenablog.com/entry/2018/05/18/742/)  

どうやら、SQLで扱うようなRDBSではなく、関係性についてのデータを保存しないらしい。  
キーとバリューだけを扱うらしい。  
近年、サーバーが簡単に分割できることから、注目を集めているらしい。  

ともかく、Redisをbrewコマンドでインストールし、redis-serverを立ち上げる。  

また、gem 'sidekiq' を Gemfileに追加し、bundle exec sidekiq にて実行する。  

連携のため、redisとsidekiqを連携させるため、以下を行う。  

```rb
# config/environments/development.rb

# 以下のコードの下に追加
# config.file_watcher = ActiveSupport::EventedFileUpdateChecker

config.active_job.queue_adapter = :sidekiq
```

<br>

#### ジョブの作成、実行
---

実際にジョブを作成していく。  
ここではあくまで動作確認を行うため、実質的には仕事をしない、簡単なジョブを作成する。  

```
# 以下のコマンドにてひな形を作成
# app/jobs/sample_job.rbが生成される

$ bin/rails g job sample
```

なお、処理が実行されていることを容易に把握するため、performメソッドに以下を記載する。  

```rb
# app/jobs/sample_job.rb

def perform(*args)
  Sidekiq::Logging.logger.info "サンプルジョブを実行しました"
end
```

このジョブを呼び出すため、以下のようにコードを追記する。  

```rb
# tasks_controller.rb
# createアクションの「if @task.save」部分のみ表示する

〜 省略 〜

if @task.save
  TaskMailer.creation_email(@task).deliver_now
  # 以下に１行コードを追加
  # sample_job.rbを非同期にて、後ほど実行する
  # ここでは実行の予約が行われ、処理できる状態になっった時点で実行開始する
  SampleJob.perform_later
  redirect_to @task, notice: "タスク「#{@task.name}」を登録しました。"

〜 省略 〜

```

これで、タスクを保存した際、先ほどsample_job.rbに記載したジョブが実行される。  
・・・全然実行されない。。。  

30−40分、格闘する。  
とりあえず、Sidekiqにはダッシュボードを実装できる機能があり、  
Queueの処理状況がダッシュボードから確認できるようなので、ダッシュボードの実装を始める。  

どうやら、ルーティングに以下を設定し、gem 'sidekiq/web' を導入すればよいことが分かる。  
公式Githubにて確認。  

```rb
# routes.rb に記載

require 'sidekiq/web'
mount Sidekiq::Web => '/sidekiq'
```

これで '/sidekiq' にてダッシュボードにアクセスできるはず。  

<a href="https://gyazo.com/881d6c39c781206048129379a2ff1248"><img src="https://i.gyazo.com/881d6c39c781206048129379a2ff1248.png" alt="Image from Gyazo" width="600" border=1/></a>  

<br>

<a href="https://gyazo.com/f33aac7098c346ab40d407a5a3b29a53"><img src="https://i.gyazo.com/f33aac7098c346ab40d407a5a3b29a53.png" alt="Image from Gyazo" width="600" border=1/></a>

ダッシュボード、アクセス成功！  

・・・失敗してる。  
エラー名が確認できたので、これで検索をかけてみる。  

[Ruby on Rails 5 \- NameError: uninitialized constant Sidekiq::Loggingの解決方法｜teratail](https://teratail.com/questions/235628)  

> こちら、自分も全く同じエラーが出ました。  
> どうやらsidekiqのバージョンが新しすぎるのが原因みたいです。  

> Gemfileにバージョンを指定して  
> gem 'sidekiq', '~> 5.0'  
> bundle install  
> sidekiqを再起動でいけました。  

なるほど！  
即チャレンジ！！  

<a href="https://gyazo.com/09d915f80ccb9b61bef73afece655bb3"><img src="https://i.gyazo.com/09d915f80ccb9b61bef73afece655bb3.png" alt="Image from Gyazo" width="600" border=1/></a>  

成功した！！  手間かけさせやがって。。。  
gem 導入時は、バージョンの互換性を疑うのが大事！  

<br>

#### 実行日時指定
---

なお、ジョブは日時を指定することができる。  
setメソッドを使って、翌日の正午にジョブを実行することができる。  

```rb
SampleJob.set(wait_until: Date.tomorrow.noon).perform_later
```

詳細は、Sidekiqのwikiに記載されているらしい。  

<br>

#### Active Job って何に使うの？
---

そもそも論が現場railsに記載されていないため、調べた。  

Railsガイドには、以下のとおり解説されていた。  
[1 はじめに](https://railsguides.jp/active_job_basics.html#%E3%81%AF%E3%81%98%E3%82%81%E3%81%AB)  

> Active Jobは、ジョブを宣言し、それによってバックエンドでさまざまな方法による  
> キュー操作を実行するためのフレームワークです。ジョブには、定期的なクリーンアップを  
> 始めとして、請求書発行やメール配信など、あらゆる処理がジョブになります。  

たしかに、メールの自動配信は有効な使い道の一つかもしれない。  
しかも、これでWebサイトの動作が遅れても困るので、非同期とすべきよい事例だろう。  

