---
title: 事前準備
categories: [事前準備]
weight: 1
---


本ハンズオンでは、VS Code と GitHub Copilot の拡張機能が使用できる必要があります。
基本的に説明は VS Code をベースに行いますが、JetBrains IDE などでも基本的に同じ操作が可能です(一部 VS Code 二しか存在しない機能もあります)。

## 1. VS Code を使用する場合
空のディレクトリを作成し、VS Code でそのディレクトリを開いてください。
ハンズオンの流れとして、サンプルのコードをベースに GitHub Copilot の使い方を説明しますので、このプロジェクトに任意のファイルを作成してコードを書いていくことになります。
![Image](https://github.com/user-attachments/assets/952fa1de-34c5-4421-b6c4-baae7ad398d0)


### GitHub Copilot が有効になっているか確認
VS Code 右下にある「GitHub Copilot」アイコンをクリックして、拡張機能が有効になっていることを確認してください。
![Image](https://github.com/user-attachments/assets/3f06052b-28e3-41d6-8aef-946922e769c7)

また、動作確認として、プロジェクト内に Sample.java などのファイルを作成し、下記のようなコメントを入力して改行してみてください。
```java
// Hello World! と出力する

```

すると、コメントの下に「Hello World! と出力する」というコードが自動補完されるはずです。
![Image](https://github.com/user-attachments/assets/d825612a-3714-4947-9b35-386a14956f8d)

### Copilot Chat が有効になっているか確認
VS Code の検索窓の右隣にある「Copilot Chat」アイコンをクリックして、チャットが起動することを確認してください。
入力欄に「Hello World と出力するプログラムを作成してください」と入力して送信し、コードが生成されることを確認してください。

![Image](https://github.com/user-attachments/assets/c2e67af5-b1f0-4015-bc26-01d3484c0338)

## 2. IntelliJ IDEA を使用する場合
新規のプロジェクトを作成し、開いてください。
ハンズオンの流れとして、サンプルのコードをベースに GitHub Copilot の使い方を説明しますので、このプロジェクトに任意のファイルを作成してコードを書いていくことになります。
![Image](https://github.com/user-attachments/assets/4c512a0c-a2be-4489-8ce2-a6a814d266db)

### GitHub Copilot が有効になっているか確認
IntelliJ IDEA の右下にある「GitHub Copilot」アイコンをクリックして、拡張機能が有効になっていることを確認してください。
![Image](https://github.com/user-attachments/assets/f66237c1-7a96-4ad0-9a3f-960bd48bbcf3)

また、動作確認として、プロジェクト内に Sample.java などのファイルを作成し、下記のようなコメントを入力して改行してみてください。
```java
// Hello World! と出力する

```

すると、コメントの下に「Hello World! と出力する」というコードが自動補完されるはずです。
![Image](https://github.com/user-attachments/assets/465e9c9e-0167-41d2-9144-fcbed96ac817)

### Copilot Chat が有効になっているか確認
IntelliJ IDEA の右側のサイドバーにある「GitHub Copilot Chat」アイコン(Copilot のアイコンの右下にチャットの吹き出しがあるアイコンの方です)をクリックして、チャットが起動することを確認してください。
入力欄に「Hello World と出力するプログラムを作成してください」と入力して送信し、コードが生成されることを確認してください。
![Image](https://github.com/user-attachments/assets/23a3a36f-119c-439a-bcda-b345d109e92a)