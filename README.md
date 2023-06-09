# つぶやきビックデータ
twitterのトレンドワードを集計するサービス

# 機能
- 形態素解析でTwitterの呟きからキーワードを抜き出してグラフ化(慣用句は辞書ファイルで指定し無視)
  - リアルタイムビュー(定点。リロードしない限り更新されない)
  - 本日累計ビュー
  - 日別累計ビュー
- キーワードをが含まれる呟き一覧を表示、またはtwitter公式にリンクする機能
  - NHKだったらNHKで検索した結果のハイライトを表示する(NHKでドラクエが人気だと分かる)
- キーワードの元記事を抽出する機能
- 過去のトレンドを閲覧できる仕組み(シームレスな時系列指定、日別指定など、多様な指定方法を用意)
- 特定の話題に絞り込む機能(コミケの話題とか)
- 興味の無い分母を取り除く機能(3代目とか)
- ストリームビューを付ける(フィルター可能)
- 英語と日本語に対応
- とあるキーワードのコンテキスト内でどんな言葉が話題になっているのか検索する機能(例: 「クリスマス」というコンテキストの中でやり取りされる情報の傾向を調べる)

# アーキテクチャ
- `stream api` → `d3JS用JSON`の生成をリアルタイムで行いたい
- twitter4Jからtwitterstream取得 → kuromojiでワードを形態素解析 → ワードと規模の関係をフォーマット&ソート → D3jsonに書き出し

# feature
- ローディング不要でリアルタイムにグラフが表示されるモードを実装したい(既存の読み込み時のグラフを固定表示するモードはstaticモードとして別に残す)

# 研究したい内容
- 例えば同人とか、そのカテゴリ|クラスタ|コンテキストの中での勢力構成はどうなっているのか？
- ニュースサイト、webサービスの勢力図(シェアされた元サイトの勢力構成)
- 主に世間の情報の流れと、特定領域にフォーカスした際の情報の流れをビジュアライズ
- 僕も@roprossさんや@fukayatsuさんや@yositosiさんや@yusukebeみたいになりたい
- お金の香りがする http://oras-pokemon.com/?page_id=5332
- 情報ボリューム全体の中でのマッピング位置を確認したい

# SQL
- 特定のranking_idのキーワードとカウントを取得する
```
select r.id, k.keyword, ri.count from (rankings r join ranking_items ri on r.id = ri.ranking_id) join keywords k on ri.keyword_id = k.id where r.id = 4239 order by ri.count desc;
```

- 全ranking_idのキーワードとカウントを集計する
```
select k.keyword, sum(ri.count) from (rankings r join ranking_items ri on r.id = ri.ranking_id) join keywords k on ri.keyword_id = k.id group by k.id,ri.count order by sum(ri.count) desc;
```

- 直近30件のanking_idのキーワードとカウントを集計する
```
select k.keyword, sum(ri.count) from (rankings r join ranking_items ri on r.id = ri.ranking_id) join keywords k on ri.keyword_id = k.id where r.id in(select id from rankings order by id desc limit 30) group by k.id,ri.count order by sum(ri.count) desc;
```
