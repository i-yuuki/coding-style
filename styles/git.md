# いまでぃの Git Style

gitうまくなりたい

## ブランチ

### 適当な運用

主に自分だけのリポジトリ

- **メイン**
  - 適当にコミットしていく
  - 何コミットもかかりそうな作業は機能ブランチを作る
  - `main`か`master`
- **機能**
  - 記述的でわかりやすい (descriptive) 名前
  - `enemy-movement`とか`user-content-cache-key`とか

### 本気の運用

**🧡 採用**: [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)

チーム制作とか

- **メイン**
  - バージョンアップとか開発の大きな区切りに`develop`からマージしてくる
  - 直接コミットは避ける
  - `main`か`master`
- **開発**
  - コミットしていく
  - 何コミットもかかりそうな作業は機能ブランチを作る
  - 基本`develop`
- **機能**
  - 記述的でわかりやすい (descriptive) 名前
  - 機能追加は名前を`feature/`で始める
  - バグ修正は名前を`bugfix/`で始める
  - `feature/item-details-dialog`とか
  - 基本的に使い捨て ― 作るものが完成したら`develop`にマージしてブランチ消す
