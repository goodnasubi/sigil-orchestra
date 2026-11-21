# 魔法陣ダッシュボードの設計

## 位置づけ

sigil-orchestra の統合管理ダッシュボードの設計である。画面のモチーフとして魔法陣を用い、Sigil バックエンド一式の全コンポーネントを一つの陣の上に配置して、状態の把握と制御を一画面で行う。

役割分担は次のとおりとする。

- 監視データ（ログ・メトリクス）の収集と供給は sigil-argus-observe が担う。
- sigil-orchestra は、その表示と、デプロイ等の制御を担う。

総合監視ポータルを別に新設しないことは、2026-11-22 に typesafe-ai の正式判定で決定済みである（ポータルの役割は本ダッシュボードが担い、監視データは sigil-argus-observe が供給する）。

## 原型

原型の実物は、本リポジトリ内の `docs/prototype/magic-circle-dashboard.html` である（自己完結した HTML で、ブラウザで開けばそのまま動く）。2026-11-22 に完成し、所有者が承認した。データはすべてダミーで、配置と操作の設計を固めるための原型という位置づけである。本書は、その原型で固めた設計を記録する。

原型のプレビュー（GitHub Pages）: https://goodnasubi.github.io/sigil-orchestra/prototype/magic-circle-dashboard.html

なお、クマ（Muse）の作業用スペースにも同じ原型があるが、スペースは他のエージェントから見られない。他のエージェントは、リポジトリ内の HTML を見ること。

## 配置（魔法陣の構造）

全11コンポーネントを、同心円の魔法陣として配置する。

- 中心: sigix
- 第一の円: sigil-rune-auth／sigil-stash-kv／sigil-summon-functions
- 第二の円（結界）: sigil-atlas-dns／sigil-shield-tls／sigil-aegis-waf
- 下部の鎖: sigil-herald-queue → sigil-raven-mail
- 最外周: sigil-argus-observe の「目」のリング（全コンポーネントを見渡す監視の象徴）
- 土台: sigil-orchestra の基盤バー（陣全体を支える統合管理そのもの）

装飾と動き:

- 背景は真っ黒ではなく深い紺色とする。
- ルーン文字のリングがゆっくり回転する。
- 接続線の上を、リクエストの流れを表す光の粒が流れる。流れの向きは実際のリクエスト経路に合わせる。
  - sigil-atlas-dns → sigil-shield-tls → sigil-aegis-waf → sigix（入り口の経路）
  - sigix ↔ sigil-rune-auth（認証・トークン検証）
  - sigix → sigil-stash-kv／sigil-summon-functions
  - sigil-summon-functions → sigil-herald-queue → sigil-raven-mail（イベント駆動の鎖）

## 状態表現

各コンポーネントの状態を色で表す。

- 正常: ミント色
- 警告: 琥珀色
- 異常: 赤く脈動する

全コンポーネントが正常なとき、魔法陣が完成して明るく光る。警告・異常がある場合は陣全体の色味も連動して変わり、一目で異常の有無が分かるようにする。

## 操作

- ノードのクリック: そのコンポーネントの詳細パネル（役割・メトリクス・ログ）を開く。パネルから状態の切り替えも行える（原型ではダミー）。
- ノードのドラッグ: 円上の配置を並べ替える。ドラッグ中は対象ノードが浮き上がり、近くのノードと位置を入れ替えられる。
- 「発動」ボタン: デプロイの起動シーケンスを演出する。sigil-atlas-dns → sigil-shield-tls → sigil-aegis-waf → sigix の順に点灯し、全て正常になれば魔法陣が完成する。

## 実データ接続の将来計画

原型のダミーデータを、実データへ順に差し替える。

- メトリクスとログは sigil-argus-observe から受け取る。データ形式の約束は、sigil-argus-observe 側の ADR（ログ／メトリクスの形式の約束）と揃える。
- デプロイ等の制御操作は、sigil-orchestra の実行機能と接続する。「発動」の演出は、実際の起動シーケンスの進捗表示へ置き換える。
- 実装の順序は、部品側の実装が揃うのに合わせて進める（収集・表示の本実装は、各コンポーネントが出すログ／メトリクスが実在するようになってから）。
