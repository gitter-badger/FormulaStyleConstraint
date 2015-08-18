# FormulaStyleConstraint
[![GitHub license](https://img.shields.io/badge/license-MIT-lightgrey.svg)](https://raw.githubusercontent.com/fhisa/FormulaStyleConstraint/master/LICENSE) [![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)

FormulaStyleConstraint は、Swift による iOSプログラミングにおいて、レイアウト制約を簡潔な数式のように記述するためのフレームワークです。


## コード例

### 基本的な使い方
「ビューAの横幅はビューBの横幅の50%から4.0引いたものと等しい」というレイアウト制約は、FormulaStyleConstraintフレームワークを使って:
```swift
viewA[.Width] == 0.5 * viewB[.Width] - 4.0
```
のように記述できます。これは以下のコードと同じレイアウト制約を生成します:
```swift
NSLayoutConstraint(
    item: viewA, attribute: .Width,
    relatedBy: .Equal,
    toItem: viewB, attribute: .Width,
    multiplier: 0.5, constant: -4.0)
```

### 例)アスペクト比
また「ビューAのサイズの縦横比は 3:4 に等しい」という制約は:
```swift
viewA[.Width] * 3.0 == viewA[.Height] * 4.0
```
のように記述できます。これは以下のコードと同じレイアウト制約を生成します:
```swift
NSLayoutConstraint(
    item: viewA, attribute: .Width,
    relatedBy: .Equal,
    toItem: viewA, attribute: .Height,
    multiplier: 4.0/3.0, constant: 0.0)
```

### 例)優先順位の指定

二項演算子`~`を使ってレイアウト制約の優先順位を指定することができます:
```swift
innerView[.Leading] == outerView[.Leading] + 4.0 ~ 750.0
```
この演算子は、レイアウト制約の優先順位を設定した上でそのレイアウト制約を返します:
```swift
let constraint = NSLayoutConstraint(
    item: innerView, attribute: .Leading,
    relatedBy: .Equal,
    toItem: outerView, attribute: .Leading,
    multiplier: 1.0, constant: 4.0)
constraint.priority = 750.0
// -> constraint
```

[サンプルアプリ](https://github.com/fhisa/FormulaStyleConstraint/blob/master/SampleApp/ViewController.swift)や[テストケース](https://github.com/fhisa/FormulaStyleConstraint/blob/master/FormulaStyleConstraintTests/FormulaStyleConstraintTests.swift)のコードも参考にしてください。

## リファレンスガイド

### 制約項 (ConstraintTerm)

制約項(ConstraintTerm)とは、レイアウト制約の右辺または左辺で、ビューを含む項のことです。
あるビュー`viewA`の横幅`.Width`を表す制約項は:
```swift
viewA[.Width]
```
と記述します。`viewA`のところにはUIViewオブジェクトが、`.Width`のところには`NSLayoutAttribute`型の値が入ります。
制約項は、以下のような構造体として定義されています:
```swift
public struct ConstraintTerm
{
    let view: UIView?
    let attribute: NSLayoutAttribute
    var multiplier: CGFloat = 1.0
    var constant: CGFloat = 0.0
}
```

### 演算子

*以下の表で「制約項」は`ConstraintTerm`型の値、「定数」は`CGFloat`型の値、「レイアウト制約」は`NSLayoutConstraint`オブジェクトを表します。*

| 演算子 | 左辺 | 右辺 | 評価値 | 意味 |
|:-----:|:------|:------|:-------|:--|
| +     | 制約項 | 定数   | 制約項 | 左辺の`constant`に右辺の値を加算 |
| +     | 定数   | 制約項 | 制約項 | 右辺の`constant`に左辺の値を加算 |
| -     | 制約項 | 定数   | 制約項 | 左辺の`constant`から右辺の値を減算 |
| *     | 制約項 | 定数   | 制約項 | 左辺の`multiplier`と`constant`に右辺の値を乗算 |
| *     | 定数   | 制約項 | 制約項 | 右辺の`multiplier`と`constant`に左辺の値を乗算 |
| ==    | 制約項 | 制約項 | レイアウト制約 | 「左辺と右辺の値が等しい」というレイアウト制約を生成 |
| ==    | 制約項 | 定数   | レイアウト制約 | 同上 |
| ==    | 定数   | 制約項 | レイアウト制約 | 同上 |
| <=    | 制約項 | 制約項 | レイアウト制約 | 「左辺の値は右辺の値より小さいか等しい」というレイアウト制約を生成 |
| <=    | 制約項 | 定数   | レイアウト制約 | 同上 |
| <=    | 定数   | 制約項 | レイアウト制約 | 同上 |
| >=    | 制約項 | 制約項 | レイアウト制約 | 「左辺の値は右辺の値より大きいか等しい」というレイアウト制約を生成 |
| >=    | 制約項 | 定数   | レイアウト制約 | 同上 |
| >=    | 定数   | 制約項 | レイアウト制約 | 同上 |
| ~     | レイアウト制約 | 定数(Float型) | レイアウト制約 | レイアウト制約の優先度を変更して、そのレイアウト制約を返す |

## Requirements

- Swift 1.2 (Xcode 6.3 以降)
- iOS 8 以降 / iOS 7 (ソースコードを直接コピー)

## インストール

2種類の方法があります。

### Carthage を使ってインストール (iOS 8 以降)

FormulaStyleConstraint は [Carthage](https://github.com/Carthage/Carthage) を使うと簡単にプロジェクトに追加できます。

- Cartfile に `github "fhisa/FormulaStyleConstraint"` の1行を追加
- `carthage update` を実行
- Carthage/Build の中にできたフレームワークをプロジェクトに追加

### ソースファイルを直接コピー (iOS 7)

- Add this repository as a git submodule:
   ```shell
   $ git submodule add https://github.com/fhisa/FormulaStyleConstraint.git PATH_TO_SUBMODULE
   // or
   $ carthage update --use-submodules
   ```
- Then just add references of FormulaStyleConstraint/*.swift to your Xcode project.

## TODO

- Mac OS X 対応
- [CocoaPods](https://cocoapods.org) 対応

## ライセンス

FormulaStyleConstraint は [MIT license](https://github.com/fhisa/FormulaStyleConstraint/blob/master/LICENSE) の元で配布しています。