# Daily Animation Specimens — 日替わりアニメーション標本箱

毎晩0時、AI (Claude) がその日のテーマを考え、CSSアニメーションを1つ生成して勝手に増え続けるギャラリーサイトです。

**🔗 サイト: https://saku0875.github.io/animation-gallery/**

<img width="1400" height="699" alt="image" src="https://github.com/user-attachments/assets/8d55bcac-0bb2-4da5-9b1f-0d5b33e8c8c8" />


## 仕組み

```
[n8n (VPS上で毎晩0時に起動)]
    ├─ ① 日付・季節を判定、今日のモードを決める
    ├─ ② Claude API (Haiku): 今日のテーマを考える
    ├─ ③ Claude API (Sonnet): テーマから作品を生成する
    └─ ④ このリポジトリの data/animations.json に1件追記してコミット
             ▼
[GitHub Pages] コミットを検知して自動反映
             ▼
[ギャラリーサイト] JSONを読んで描画するだけの静的サイト
```

- **テーマもAIが考える**: 25%の確率で「季節モード」になり、季節の風物詩がお題になります。残りは完全ランダム
- **DBなし**: 保存先は `data/animations.json` の1ファイルのみ。git履歴がそのままアーカイブになります
- **デプロイ作業なし**: n8nのコミットをトリガーにGitHub Pagesが自動更新

## サイトの機能

- **Today**: 最新の作品を大きく表示(自動再生)
- **アーカイブ**: 過去作は止め絵で一覧表示。ホバー/タップで再生(パフォーマンス対策)
- **テーマフィルタ**: サイドバーからテーマ別に絞り込み
- **コード表示**: 各カードの `</>` ボタンで、その作品のCSS/HTMLをその場で閲覧可能
- **アクセシビリティ**: 「視差効果を減らす」設定の環境では自動再生を抑制し、ユーザー操作時のみ再生 ([#1](https://github.com/saku0875/animation-gallery/issues/1))

## リポジトリ構成

```
animation-gallery/
├── index.html            # ギャラリー本体(1ファイル完結の静的サイト)
└── data/
    └── animations.json   # n8nが毎晩追記する作品データ
```

### データ形式

```json
[
  {
    "date": "2026-07-16",
    "theme": "波",
    "title": "波のゆらぎ",
    "keyframes_css": "@keyframes wave { ... } .demo-stage { ... }",
    "demo_html": "<div></div>..."
  }
]
```

配列の先頭が最新。各作品のCSSは `.demo-stage` 配下にスコープされており、表示時に作品ごとの固有ID配下へ書き換えて注入することでセレクタの衝突を防いでいます。

## 技術スタック

| 領域 | 使用技術 |
|---|---|
| 自動化 | n8n (Xserver VPS + Docker でセルフホスト) |
| AI | Claude API (テーマ考案: Haiku / 作品生成: Sonnet) |
| データ保存 | GitHubリポジトリ内のJSON |
| ホスティング | GitHub Pages |
| フロント | HTML/CSS/JavaScript (フレームワークなし・1ファイル) |

## 解説記事

作り方の詳細はQiitaに書いています。

- [n8nをXserver VPSにセルフホストする(既存nginxと同居させる編)]([https://qiita.com/tsugumic5/](https://qiita.com/tsugumic5/items/271e88e203ca2291e86c)) 
- [n8n + Claude APIで毎晩CSSアニメーションが増えていく標本箱サイトを作った]([https://qiita.com/tsugumic5/](https://qiita.com/saku0875/items/6a362e82bf2ce9ef0a08)) 

