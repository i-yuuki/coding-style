# いまでぃの Unity Coding Style

参考にしたスタイル、改変した部分、自作の部分、特記事項まとめ

後半の[Tips](#Unity-開発Tips)もぜひ

## フォーマット

**🧡 採用**: Visual Studio デフォルト C# スタイル

## 命名

**📗 参考**: [.NET Runtime, C# Coding Style](https://github.com/dotnet/runtime/blob/main/docs/coding-guidelines/coding-style.md)

### 原則

- システムハンガリアンは有罪
  - 変数の型が何かとかクラスメンバーかどうかとかを名前に含めるやつ。そんな情報まで入れなくても分かりやすい名前付けられるって。
- 名前だけでどんなものか分かるのがベスト

### ファイル

- そのファイルが持つクラス名そのまま
- 1ファイルにクラスがいっぱいある場合、メインなものの名前 (ファイル分割してもいいかもよ)

```plaintext
PlayerCameraControl.cs
VectorExtensions.cs
```

### 名前空間

- PascalCase
- `<ProjectName>.Hoge.Fuga`
- シーン固有: `<ProjectName>.Scene.<SceneName>`
- これに合わせてフォルダつくってもいいかも (`Scripts/Scene/Hoge/Fuga.cs`)
  ちょっとJavaみたいだね！

### クラス

- PascalCase
- インターフェースは最初にIをつける

```cs
public sealed class PlayerCameraControl {}
```

### 変数

- プロパティ: PascalCase
- publicフィールド: PascalCase
- privateフィールド: camelCase
- メソッド引数: camelCase

```cs
public bool IsValid { get; private set; }
public bool IsValid; // publicフィールドはあんま使わない気がする
private bool isValid;
private void Hoge(bool isValid) {}
```

### 定数

- PascalCase
- enumもこれ

```cs
public const int MaxPlayers = 10;
public enum DayOfWeek
{
    Sunday = 0,
    Monday = 1,
}
```

### 関数 (メソッド)

- PascalCase
- 動詞にする

```cs
public void SayHi(string name)
{
    Debug.Log($"Hi {name}!");
}
```

## エンコーディング

- スクリプトファイルは全部UTF-8 w/ BOM
- ほかはUTF-8とかそのへん。Shift JISは帰れ
- 改行 (LF or CRLF) はどっちかに統一

# Unity 開発Tips

## フォルダを分けよう

シーン固有のスクリプトを分けてみたり

```plaintext
Assets/
└ Scripts/
  ├ Item/
  ├ Scene/ # <------------
  │ ├ Title/
  │ │ └ TitleScreen.cs
  │ └ Game/
  │    └ LevelManager.cs
  └ Utils/
```

スクリプト以外も分けてみたり

```plaintext
Assets/
├ Prefabs/
│ ├ Title/
│ └ Game/
└ Textures/
   └ Scene/ # こうしてもワンチャン
     ├ Title/
     └ Game/
```

## 名前空間を使おう

もう一度…やってみないか…<ruby><rb>あの頃</rb><rt>Java</rt></ruby>みたいに…

```cs
namespace MyProject.Scene.Title{
  public sealed class TitleScreen : MonoBehaviour{
  }
}
```

## ちょっと待って！！そのpublic、本当に必要？

```cs
// before
public TextMeshProUGUI title;

// after
// 「textしか変えなそうな仕様だったからtextしか変えられなくしたよ」的な
[SerializeField] private TextMeshProUGUI title;
public string Title { set => title.text = value; }
```

- 要はチームメイトがどこからでもコンポーネントをいじり放題なのがやだ
- publicにする理由が「Inspectorで見たいから」**だけ**ならとりあえずやめてみる
- コンポーネント全体が必要か考えて (たぶんそうじゃないぞ) プロパティをうまく使おう

## 小さく一時的なメモリ割り当てを避けよう

### Why

本当はこういう割り当ては世代別GCがうまくやってくれるんだけど、[UnityのGCは世代別じゃない](https://docs.unity3d.com/ja/2021.1/Manual/BestPracticeUnderstandingPerformanceInUnity4-1.html)🥺🥺

### ボックス化

```cs
// More memory
label.text = $"HP: {health}";
// Less memory
label.text = $"HP: {health.ToString()}";
```

補完文字列内の値は`object`引数らしくボックス化される。その前に`ToString()`すれば避けられるとかなんとか

### `UnityEngine.Object.name`

```cs
// More memory
if (icon.sprite.name == iconSprite) return;

// Less memory
if (spriteName == iconSprite) return;
spriteName = iconSprite;

icon.sprite = Resources.Load(iconSprite);
```

`UnityEngine.Object.name`は見るだけで割り当てが起きる…まじか
