# Groundpolis の Misskey との相違点

## 独自機能
- Groundpolis Flavored MFM (GPFM)
  - `<xspin>X軸回転</xspin>`
  - `<yspin>Y軸回転</yspin>`
  - `<vflip>縦反転</vflip>`
  - `****より大きい文字****`
  - `<blink>点滅</blink>`
  - `<marquee>マーキー</marquee>`
  - `<right>右寄せ</right>`
  - `<sup>上付き</sup>`
  - `<sub>下付き</sub>`
- プレビュー機能
- アカウントの誕生日が今日のとき「本日誕生日です！」などと表示される
- プロフィールに性別を設定できる
- サーバーにログインしているユーザーのみ閲覧できる、投稿範囲「ログインユーザー」
- ~~リノートを解除できる~~
  - 本家で実装
- アイコンの種別を4種類から選べる
- 絵文字単体のノートをスタンプとして表示するよう設定できる
- 通知の設定を追加
  - ブラウザーから通知を発行するかどうかの設定
  - トースト通知(画面内に飛び出る通知表示)をするかどうかの設定
- お知らせがタイムライン上に固定表示される
- ~~通知音が鳴る~~
  - →本家で実装
- 顔文字ガチャができる
	- カスタマイズもできる
- ノートやチャットの表示を小さい端末に最適化して表示するコンパクトモード
- お絵かき機能
- サイドバーをたためるボタン
- タイムライン上での動画再生
- 実験的機能
  - タイムラインカラムを非表示にする機能
		- ウィジェットによる擬似Deckの実現などに
- リモートフォロワーとローカル限定フラグ
- ~~公開範囲ピッカーにローカル限定フラグのトグルボタンを統合~~
  - →本家 12.39.0 でマージ
- タイムラインを追加
	- キャットタイムライン - CAT に設定しているユーザーのノートが流れる
	- リモートフォロータイムライン - フォローしているリモートユーザーのノートが流れる
	- フォロワータイムライン - 自分をフォローしているユーザーのノートが流れる
- リモートのノートであることを明示するアイコンを表示
- ノートのユーザー名表示をカスタマイズできる
- フォロー・フォロワーを隠す機能
- 他ユーザーのノートをパクるボタン
- RSS ウィジェットに共有ボタンを追加
- カスタム絵文字リクエスト機能
- 投稿フォームウィジェット
- ハイライトをサーバー側で無効化できる

## 改善
- ミュート/ブロックリストの項目をプロフィールへのリンクに
- CW 入力欄でキーボードショートカットで投稿できる
- トークを ⌘Enterでも送信できる
- isVerified を復活
- ブロックされている、している場合表示する
- ブロック、被ブロックユーザーのページにフォローボタンを表示しない
- 確認ダイアログを表示するか否か選べる
- カスタム絵文字を大きく表示しないよう設定できる
- 設定画面を本家と大きく変更
- 自分のユーザーページからプロフィールを設定できる
- サイドバーのユーザー名がユーザーページへのリンクになる
- サイドバーのユーザー名を表示名に変更
- サイドバーのユーザー名の隣に…ボタンを用意
  - アカウント切り替えができる
- ユーザーページのアイコンをクリックした場合アイコンが最大表示される
- プロフィール編集欄、検索ダイアログなど本家より多くの箇所で絵文字などの補完がされる
- デスクトップモードでサイドバーから[検索]を削除
- お知らせ と アンテナ のアイコンを変更
- ユーザーページのフォローリストにフォローされているかどうかを表示
- カスタム絵文字一覧の手動更新機能（絵文字を編集するたびにページネーションが戻ってしまう問題の暫定的な対策）
- ローカルユーザーのホストを省略しないオプション
- 「あなた宛て」「ダイレクト投稿」をタイムラインページに移動
- モバイルを除くタイムライン切り替えのUIを改良
- 付箋ウィジェットを複数置いた場合、内容が独立するよう改善

## その他
- ブランド名の変更 (Misskey -> Groundpolis)
- 用語をいくつか変更
  - ソーシャル → ホーム+ローカル
  - (公開範囲の)ホーム→未収載

## v3 で未実装(v2まであった機能）
- リモートユーザーのメニューに、管理者およびモデレーター限定で[リモート情報を更新]ボタン追加
- 本日が誕生日であるアカウントのページに花火が打ち上がる
- タイムラインの切り替えに応じて公開範囲を切り替える機能「ビジビリティスイッチ」を追加
- 管理者の追加／削除ができる
- ユーザーページにルームへの移動ボタンを追加
- ~~ルームページにホームへ戻るボタンを追加~~
  - 不要
- 「:」だけを入力しても絵文字サジェストが出ない
