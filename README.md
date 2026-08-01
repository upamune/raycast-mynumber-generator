# Mynumber Generator

チェックデジットが正しいダミーのマイナンバー（個人番号）を生成する Raycast 拡張です。テストデータ作成用途を想定しています。

> [!WARNING]
> 生成される番号はランダムなダミーです。チェックデジットの形式検証は通りますが、実在の個人番号とは一切関係ありません。実在の番号と偶然一致する可能性は理論上ありますが、意図的なものではありません。

## 機能

- `Generate Mynumbers` コマンドで、指定した個数のダミーのマイナンバーを一括生成
- 生成した番号をクリップボードにコピー、またはアクティブなウィンドウにペースト（Preferences で切替）
- `1234-5678-9012` のようなハイフン区切りフォーマットにも対応（Preferences で切替）

## 使い方

1. Raycast で `Generate Mynumbers` を実行
2. 引数に生成したい個数を入力（省略時は Preferences の `Default Number of Mynumbers` を使用）
3. 生成された番号がコピー（またはペースト）される

## チェックデジットの算出

行政手続における特定の個人を識別するための番号の利用等に関する法律の施行に伴う総務省令（平成26年総務省令第85号）に定められた算式に従っています。

```
検査用数字 = 11 − (Σ(n=1..11) Pn × Qn を 11 で除した余り)
※ 余りが 1 以下の場合は 0

Pn: 検査用数字を除いた11桁のうち最下位から数えて n 番目の数字
Qn: n が 1〜6 のとき n + 1、7〜11 のとき n − 5
```

## 開発

ツールチェインは [mise](https://mise.jdx.dev/) で管理しています（bun / node）。

```sh
mise install       # bun / node をインストール
bun install
bun run dev        # Raycast で開発モード起動
bun test src       # チェックデジットロジックのテスト
bun run lint       # oxlint
bun run fmt        # oxfmt（fmt:check でチェックのみ）
bun run knip       # 未使用の依存・エクスポート検出
bun run build      # ray build
```

CI（GitHub Actions）では fmt:check / lint / knip / test / build を実行しています。actions のバージョンは [pinact](https://github.com/suzuki-shunsuke/pinact) でコミットSHAに固定しています。

## リリース

[tagpr](https://github.com/Songmu/tagpr) でリリースを自動化しています。

1. main への push をトリガーに tagpr がリリース PR を作成・更新する（`package.json` の version を bump）
2. リリース PR をマージすると `vX.Y.Z` タグが打たれる
3. タグ push をトリガーに Release workflow が `ray build` でビルドし、成果物の zip を添付した GitHub Release を作成する

tagpr が push したタグで workflow を起動するため、GitHub App のトークンを使用しています（リポジトリの variable `TAGPR_APP_ID` と secret `TAGPR_PRIVATE_KEY` が必要）。

## License

MIT
