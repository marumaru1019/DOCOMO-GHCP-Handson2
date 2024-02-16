---
title: 4. Github Copilot を活用したコード作成 & テストケースの作成
categories: [ソフトウエアエンジニア向け]
tags: [Github Copilot, Demo]
weight: 4
---

## Github Copilot について
Github Copilotは、OpenAIとGitHubが共同開発した、画期的なAIコーディングパートナーです。このツールは、開発者がコーディングしている最中にリアルタイムでコードの提案を行い、生産性を劇的に向上させることを目的としています。
Github Copilotは、OpenAI Codexという強力なAIモデルを核としています。このモデルは、一般に公開されている大量のソースコードと自然言語テキストを元に事前に学習されており、コーディングに特化しています。
Github Copilot を活用することで以下のようなことが可能です。
- コメントからコードを生成
    - コメントからコードを生成する能力を持ち、開発者が書いたコメントを基に関連するコードを提案します。
- 繰り返しコードの補完
    - 繰り返しコードの補完機能により、一貫性のあるコーディングを支援し、タイピングの負担を軽減します。
- 複数の提案オプション
    - 複数の提案オプションを提供し、開発者が適切なコードを選択できるようにします。

## Github Copilot の使い方
### 準備するもの
- Visual Studio Code
- Github アカウント
- Copilot ライセンス

### Github Copilot 拡張機能のインストール
Visual Studio Code の拡張機能から Github Copilot をインストールします。
![](../images/copilot-extensions.png)

### GIthub Copilot の有効化
Github Copilot
![](../images/copilot-place.png)

## Github Copilot でできること
Github Copilot を用いて、単純にコードを生成するだけでなく、以下のようなことが可能です。
- コードの可読性の向上
- 型の追加
- バグフィックス

### コードの可読性の向上
もともと、以下のようなコードがあるとします。
`get_speeds_and_times` 関数は、速度センサーのデータから車両の平均速度と、与えられた時間間隔内での最大加速度を計算します。
```python
def get_speeds_and_times(speeds, times):
    avg_speed, max_acceleration = (sum(speeds) / len(speeds), max([(s2 - s1) / (t2 - t1) for s1, s2, t1, t2 in zip(speeds, speeds[1:], times, times[1:])])) if len(speeds) > 1 else (speeds[0], 0)
    return avg_speed, max_acceleration
```

#### :memo: プロンプト
Github Copilot に対して以下のようなプロンプトを与えることで、可読性が高いコードに変換することができます。

```text
このコードをより理解しやすい形にリファクタリングしてください。具体的には、長い式や複雑な条件を複数の変数に分割し、各変数に明確な名前を付けてください。
```

#### :robot: 出力例
一行で長い式や複雑な条件が含まれていたコードが、以下のようにリファクタリングされます。
```python
def get_speeds_and_times(speeds, times):
    if len(speeds) == 1:
        avg_speed = speeds[0]
        max_acceleration = 0
    else:
        avg_speed = sum(speeds) / len(speeds)
        acceleration = [(s2 - s1) / (t2 - t1) for s1, s2, t1, t2 in zip(speeds, speeds[1:], times, times[1:])]
        max_acceleration = max(acceleration)
    return avg_speed, max_acceleration
```
### 型の追加
もともと、以下のようなコードがあるとします。
`estimate_fuel_consumption` 関数は、走行距離と平均燃料消費率を元に、推定燃料消費量を計算します。

#### :memo: プロンプト
Github Copilot に対して以下のようなプロンプトを与えることで、型の追加を行うことができます。

```text
この関数の引数と戻り値に適切な型アノテーションを追加してください。
```

#### :robot: 出力例
引数と戻り値に適切な型アノテーションが追加されたコードが、以下のように出力されます。
```python
def estimate_fuel_consumption(distance: float, avg_fuel_rate: float) -> float:
    estimated_fuel: float = (distance / 100) * avg_fuel_rate
    return estimated_fuel
```

### バグフィックス
もともと、以下のようなコードがあるとします。
`monitor_battery_status` 関数は、バッテリーの電圧、容量、電力消費、走行距離を元に、バッテリーの充電レベル、残り走行距離、充電警告を計算します。
この関数には、以下のようなバグが含まれています。
- `battery_voltge` という変数名のタイポ (本来は `battery_voltage`)
- avg_power_per_km の計算式において、走行距離が0の場合にゼロ除算が発生する可能性がある
- estimated_range の計算式において、走行距離が0の場合にゼロ除算が発生する可能性がある
```python
def monitor_battery_status(battery_voltage, battery_capacity, current_draw, total_distance_covered):
    # 電力消費を計算 (typo: correct variable name is 'battery_capacity')
    power_used = battery_voltge * current_draw  # typo: 'battery_voltage'
    
    # 現在のバッテリー充電レベルを計算
    current_charge_level = (battery_capacity - power_used) / battery_capacity * 100
    
    # 1km走行あたりの平均消費電力を計算
    avg_power_per_km = power_used / total_distance_covered  # potential bug: division by zero
    
    # 推定残り走行距離を計算
    estimated_range = (battery_capacity - power_used) / avg_power_per_km # potential bug: division by zero
    
    # 充電警告をチェック
    charge_warning = current_charge_level < 20  # battery needs charging if less than 20%
    
    return current_charge_level, estimated_range, charge_warning
```

#### :memo: プロンプト
Github Copilot に対して以下のようなプロンプトを与えることで、バグフィックスを行うことができます。

```text
この関数のバグを修正してください。
```

#### :robot: 出力例
タイポやゼロ除算のバグが修正されたコードが、以下のように出力されます。
```python
def monitor_battery_status(battery_voltage, battery_capacity, current_draw, total_distance_covered):
    # 電力消費を計算 (typo: correct variable name is 'battery_capacity')
    power_used = battery_voltage * current_draw  # typo: 'battery_voltage'
    
    # 現在のバッテリー充電レベルを計算
    current_charge_level = (battery_capacity - power_used) / battery_capacity * 100
    
    # 1km走行あたりの平均消費電力を計算
    avg_power_per_km = power_used / total_distance_covered if total_distance_covered > 0 else 0  # potential bug: division by zero
    
    # 推定残り走行距離を計算
    estimated_range = (battery_capacity - power_used) / avg_power_per_km if avg_power_per_km > 0 else 0
    
    # 充電警告をチェック
    charge_warning = current_charge_level < 20  # battery needs charging if less than 20%
    
    return current_charge_level, estimated_range, charge_warning
```

## Github Copilot におけるプロンプトの重要性
Github Copilotの効果を最大限に引き出すためには、プロンプトエンジニアリングが鍵となります。適切なプロンプトを設計することで、コードの出力精度が大幅に向上します。
具体的には以下のようなことをするとよいとされています。
- 指示を明確化する
- 参考を与える
- 大きなタスクを小さなタスクに分割する

### 指示を明確化する
プロンプトを与える際には、具体的な指示を明確にすることが重要です。

#### Before
以下のプロンプトでは、「車両の速度データを処理して。」という非常に一般的な指示が与えられています。この指示だけでは、Copilotは具体的なアクションを推測する必要があり、結果として多岐にわたる提案がなされる可能性があります。

```text
車両の速度データを処理して。
```

#### After
以下のプロンプトでは、プログラミング言語、関数の目的、引数の形式、関数名といった具体的な指示が与えられています。このような指示を与えることで、Copilotはより適切なコード提案を行うことができます。
```text
Pythonで、車両の速度データリスト（km/h）を引数として受け取り、平均速度を計算して返す関数を作成してください。関数名はcalculate_average_speedとしてください。
```

### 参考を与える
Copilotにコーディングの文脈や具体例を提供することで、より適切なコード提案を促します。

#### Before
以下のプロンプトでは、燃料消費率の計算を行うコードを作成するように指示されています。しかし、具体的な例がありません。

```text
燃料消費率の計算を行うコードを作成して。
```

#### After
以下のプロンプトでは、具体的な利用シナリオが与えられています。このような指示を与えることで、Copilotはより適切なコード提案を行うことができます。

```text
Pythonで、車両の走行距離（km）と消費した燃料の量（L）を引数として受け取り、100kmあたりの燃料消費量（L/100km）を計算して返す関数を作成してください。関数名はcalculate_fuel_efficiencyとしてください。例えば、500kmを50Lで走った場合、10L/100kmを返します。
```

### 大きなタスクを小さなタスクに分割する
大きなタスクを小さなタスクに分割することで、Copilotによるコード提案の精度を向上させることができます。

#### Before
以下のプロンプトでは、非常に広範囲で抽象的な要求「自動車の故障診断システムを開発して。」が示されています。この一般的な指示では、Copilotが提供する解決策は幅広く、特定の要件を満たさない可能性があります。

```text
自動車の故障診断システムを開発して。
```

#### After
以下のプロンプトでは、タスクが具体的な2つのステップに分割されています。このような指示を与えることで、Copilotはより適切なコード提案を行うことができます。

```text
ステップ1:
「Pythonで、エンジン温度センサーのデータリストを引数として受け取り、平均温度を計算する関数を作成してください。関数名はcalculate_average_engine_temperatureとしてください。」

ステップ2:
「ステップ1で作成した関数を使用して、エンジン温度が一定の閾値（例えば90°C）を超えた場合に警告メッセージを表示するコードを作成してください。」
```

## Copilot Chat について
Copilot Chatは、GitHub Copilotの機能拡張として想定される機能です。GitHub Copilotは、コードを書く際のAI支援ツールとして開発者に提案を行いますが、Copilot Chatはこの概念をさらに進め、より対話的な形式での支援を提供することを目的としています。この機能を通じて、開発者は自然言語での質問や指示を行うことで、特定のコーディングタスクや問題解決に関するAIからの直接的な支援を受けることができます。
- チャットインターフェイスを通じて、コーディングに関する質問に対する回答を受け取る
    - チャットインターフェイスを通じて、コーディングに関する質問をCopilotに投げかけることができます。Copilotは、質問に応じて、コードの提案や説明、単体テストや修正プログラムなどを返します。
- コードの提案や説明を受け取る
    - コードの提案や説明は、エディターで開いているコードまたはエディターで強調表示したコードスニペットに基づいて生成されます。Copilotは、コードの機能や目的を自然言語で説明したり、コードの改善や最適化のための提案を行ったりします。
- 単体テストや修正プログラムを生成する
    - 単体テストや修正プログラムは、エディターで開いているコードまたはエディターで強調表示したコードスニペットに基づいて生成されます。Copilotは、コードの品質や安全性を向上させるために、単体テストのケースやコード内のバグに対する修正プログラムを提供します。


