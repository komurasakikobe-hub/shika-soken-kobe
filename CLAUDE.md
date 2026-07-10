# 神戸歯科総研 プロジェクト指示

大阪歯科総研（`~/Desktop/クロード/大阪歯科総研`）の横展開第1号。
**立ち上げ手順・ルールの正本は `~/Desktop/クロード/横展開マニュアル.md`**。
デザイン・記事・スコアリングの共通ルールは大阪版 `CLAUDE.md`／`articles/ARTICLE_MANUAL.md` に準拠する。

## サイト概要
- 対象：神戸市9区（東灘・灘・中央・兵庫・北・長田・須磨・垂水・西）の歯科医院ポータル
- 設定の正本：`site_config.json`（Python側）＋ `assets/site-config.js`（ブラウザ側）。都市固有の値は必ずここに集約
- 記事生成側の設定：`AI評判設計システム/client_config_kobe.json`（daily_post.sh の CLIENT_CONFIG 環境変数で直接指定する。旧コピー差し替え運用は廃止・2026-07-10）
- ドメイン・GA4測定IDは未定（公開前にユーザーが決定）

## 立ち上げ進捗（2026-07-08 開始）
- [x] フォルダ作成・大阪版からスクリプト/アセット複製・git init
- [x] site_config.json / site-config.js を神戸用に作成
- [x] kobe_stations.py（神戸市内主要駅 約115駅・9区カバー）
- [x] clinic_collector.py：区リスト9区・グリッド範囲を神戸市に変更
- [x] geocode_clinics.py：区名フォールバック正規表現を「神戸市…区」に変更
- [x] build_nearest_station.py：kobe_stations を import
- [x] shindan.js：AREA_KEYWORDS を神戸9区に差し替え
- [x] 全ビルドスクリプト・HTMLのブランド置換（大阪→神戸、兵庫県、KOBE DENTAL RESEARCH）
- [x] client_config_kobe.json 作成
- [x] データ収集（clinic_collector.py・1015院→北区北部漏れ補完後1039院）
- [x] 収集後の監査（7点チェック）→ slug生成 → ジオコーディング → 最寄駅計算
- [x] サイト生成（build_clinics → build_features → build_index → build_sitemap）
- [x] index.html の統計数字を実数に差し替え（789院・2026-07-11更新）
- [ ] サムネイル画像（ChatGPT無課金ルートで神戸用に発注）— 未着手（assets/thumbnails/ 空）
- [x] GitHub Privateリポジトリ作成・push（shika-soken-kobe.git）
- [x] Cloudflare Pages接続・ドメイン決定（kobe.shikasoken.com。ただし本番はCloudflare Pages側が大阪の共有リポジトリshika-sokenの`kobe`ブランチを見る構成のため、pushは`origin`（専用repo）と`osaka-shared`（共有repo）の両方に行うこと。横展開マニュアル参照）
- [ ] GA4新プロパティ・Search Console（ユーザー操作）— site-config.jsに測定IDなし、未設定
- [ ] 毎日投稿 launchd（時刻は大阪と30分ずらして 9:30）— reviewrefreshのみ登録済み、dailypostは未登録

## 注意（大阪版で踏んだ地雷 — 横展開マニュアル §3 の要約）
- slug衝突（generate_slug_map.py 必須）／ジオコーディング座標集約（同一座標4件以上を監査）
- 統計数字の不一致（トップページ手打ち数字とDBの実数）
- 計測タグの入れ忘れ（全ページに site-config.js + odr-track.js）
- 医療広告ガイドライン（「唯一」「No.1」等の断定禁止・順位は条件適合度である免責を明記）

## 費用ルール
- データ収集・API課金・ドメイン購入など費用が発生する操作は、金額の見積りを提示して承認を得てから実行
- 反復作業（数百件の抽出）は gpt-4o-mini スクリプトへ、判断・実装は Claude が担当
## 進捗メモ（2026-07-09）
- 収集完了（1,015院収集・掲載見込み768院。公的統計887施設）。公式サイト深掘り実行中
- 残：AI分析backfill→slug→最寄駅→サイト生成→統計実数化（手順は毎時の監視ジョブが自動実行）

## 進捗メモ（2026-07-11）
- Nearby Searchグリッドのlat_max=34.820が北区北部（八多町・淡河町・大沢町など）を構造的にカバーしておらず、岩田歯科医院ほか北区北部の医院が漏れていた。[[feedback_overpass_first_collection]]の方式（Overpass APIで無料に候補抽出→該当分のみGoogle Places照合）で6院を追加（岩田歯科医院・しわ歯科医院・きずな歯科クリニック・ステップガーデン宮地歯科・米田歯科・室井歯科医院）。西宮市・三田市の院6件も同時ヒットしたがエリア外のため自動除外。フルグリッド再収集より大幅に安価（¥80程度）
- 既存データに住所の区名重複表記バグ（例：「神戸市北区北区北区ひよどり台南町…」）が10件見つかり修正。今後の横展開先でも同種の住所整形バグに注意
- 掲載院数：789院（DB総数1,039院）。共有リポジトリ（osaka-shared→kobeブランチ）・専用リポジトリ両方へpush済み、本番反映確認中
