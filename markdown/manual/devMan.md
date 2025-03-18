# 環境構築手順書

# 1 本書について

本書では、建物振動シミュレーションシステム（以下「本システム」という。）の利用環境構築手順について記載しています。本システムの構成や仕様の詳細については以下も参考にしてください。

[技術検証レポート](https://www.mlit.go.jp/plateau/file/libraries/doc/plateau_tech_doc_0030_ver01.pdf)

# 2 動作環境

本システムの動作環境は以下のとおりです。




| 項目 | 最小動作環境 | 推奨動作環境 | 
| - | - | - | 
| WEBブラウザ | 特に縛りは無い | Google Chrome | 
| CPU | Intel Core i3以上 | Intel Core i5以上 | 
| メモリ | 4GB以上 | 8GB以上 | 
| ディスプレイ解像度 | 1024×768以上 |  同左  | 
| ネットワーク       | 光回線|  同左                            | 

# 3 ビルド手順

### ビルドの準備
自身でソースファイルをダウンロードしビルドを行うことで、システムを動作させることができます。\
ソースファイルは
[こちら](https://github.com/Project-PLATEAU/Earthquake-simulation-tools/)
からダウンロード可能です。

1. **環境の確認**
   - Node.js v16.x以上がインストールされていることを確認
   - pnpmがインストールされていることを確認（なければ`npm install -g pnpm`でインストール）

2. **依存関係のインストール**
   ```bash
   pnpm install
   ```

3. **環境変数の設定**
   - 本番環境用の環境変数が`.env`または`.env.production`ファイルに設定されていることを確認
   - AWS認証情報やAPIエンドポイントなど、必要な設定が完了していることを確認

4. **アダプターのインストール**
   - デプロイ先の環境に応じて適切なアダプターをインストール
   ```bash
   # AWS Lambda + API Gateway用
   pnpm add -D @sveltejs/adapter-node

   # 静的サイトホスティング用
   pnpm add -D @sveltejs/adapter-static

   # Vercel用
   pnpm add -D @sveltejs/adapter-vercel
   ```
5. **svelte.config.js の確認**
   - 使用するアダプターが正しく設定されていることを確認

### ビルド実行

本番用ビルドを実行します：

```bash
pnpm run build
```

このコマンドは以下の処理を行います：
- TypeScriptのコンパイル
- SvelteコンポーネントのSSRおよびクライアントコードのビルド
- CSSの最適化
- アセットの最適化と圧縮
- 選択したアダプターによる出力ディレクトリの構成

### ビルド後の確認

ビルドされたアプリケーションをローカルでプレビューするには：

```bash
pnpm run preview
```

### デプロイ

デプロイ先環境に応じた手順：

1. **AWS Lambda + API Gateway**
   - `build`ディレクトリを Lambda 関数としてデプロイ
   - API Gateway との連携設定

2. **静的ホスティング (S3, Netlify, GitHub Pages など)**
   - `build/client`ディレクトリの内容をホスティングサービスにアップロード

3. **Dockerコンテナ**
   ```bash
   # Dockerfileがプロジェクトルートにあることを確認
   docker build -t mosiri-app .
   docker run -p 3000:3000 mosiri-app
   ```

### よくある問題と解決策

- **ビルドエラー**: 依存関係のバージョン不一致の場合は `pnpm install` を再実行
- **環境変数エラー**: `.env.production` ファイルを確認
- **アダプタエラー**: 正しいアダプタがインストールされ、設定されていることを確認
- **APIアクセスエラー**: CORS設定とAPIエンドポイントの構成を確認

> 注意: デプロイ先の環境に合わせて適切な[adapter](https://kit.svelte.dev/docs/adapters)を必ずインストールしてください。デフォルトでは開発環境向けのアダプタのみが設定されています。