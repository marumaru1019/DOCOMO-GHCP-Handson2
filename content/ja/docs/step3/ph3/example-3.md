---
title: 3. 数値流体解析での活用
categories: [ソフトウエアエンジニア向け]
tags: [CFD, Python, NumPy, Matplotlib]
weight: 3
---

自動車の設計ではシミュレーションを行うことがあります。そこで簡単な数値流体解析でのプロンプトの例を紹介します。

### :memo: 数値計算のプロンプト
```text
2次元キャビティ流れをPythonで解くためのプログラムを作成してください。結果の可視化もお願いします。
```

### :robot: 出力例
2次元キャビティ流れをPythonで解くプログラムを作成し、結果を可視化するためには、NumPyやMatplotlibなどのライブラリが必要です。以下に、Pythonを使用して2次元キャビティ流れを解くための基本的なステップとサンプルコードを示します。

まず、必要なライブラリをインポートします。

```python
import numpy as np
import matplotlib.pyplot as plt
```

次に、計算領域の設定と初期条件を定義します。

```python
# 計算領域の設定
nx = 41  # x方向の格子点数
ny = 41  # y方向の格子点数
Lx = 1.0  # x方向の領域の長さ
Ly = 1.0  # y方向の領域の長さ
dx = Lx / (nx - 1)
dy = Ly / (ny - 1)

# 時間ステップとステップ数の設定
nt = 100  # タイムステップ数
dt = 0.001  # 時間ステップ幅

# 初期条件の設定
u = np.zeros((ny, nx) )  # x方向速度成分
v = np.zeros((ny, nx) )  # y方向速度成分
p = np.zeros((ny, nx) )  # 圧力
```

次に、流体の物性値や境界条件を設定します。ここでは、簡単のために無粘性流体としています。

```python
rho = 1.0  # 密度
nu = 0.1  # 動粘性係数

# 境界条件の設定
u[0, :] = 1  # 上壁面のx方向速度
u[-1, :] = 1  # 下壁面のx方向速度
v[:, 0] = 0  # 左壁面のy方向速度
v[:, -1] = 0  # 右壁面のy方向速度
```

次に、2次元キャビティ流れのメインループを実装します。このループでは、時間ステップごとに速度と圧力を更新します。

```python
for n in range(nt):
    un = u.copy()
    vn = v.copy()

    # x方向速度成分の更新
    for i in range(1, ny - 1):
        for j in range(1, nx - 1):
            u[i, j] = (un[i, j] -
                       un[i, j] * dt / dx * (un[i, j] - un[i, j - 1]) -
                       vn[i, j] * dt / dy * (un[i, j] - un[i - 1, j]) -
                       dt / (2 * rho) * (p[i, j + 1] - p[i, j - 1]) +
                       nu * (dt / dx**2 * (un[i, j + 1] - 2 * un[i, j] + un[i, j - 1]) +
                             dt / dy**2 * (un[i + 1, j] - 2 * un[i, j] + un[i - 1, j])))

    # y方向速度成分の更新
    for i in range(1, ny - 1):
        for j in range(1, nx - 1):
            v[i, j] = (vn[i, j] -
                       un[i, j] * dt / dx * (vn[i, j] - vn[i, j - 1]) -
                       vn[i, j] * dt / dy * (vn[i, j] - vn[i - 1, j]) -
                       dt / (2 * rho) * (p[i + 1, j] - p[i - 1, j]) +
                       nu * (dt / dx**2 * (vn[i, j + 1] - 2 * vn[i, j] + vn[i, j - 1]) +
                             dt / dy**2 * (vn[i + 1, j] - 2 * vn[i, j] + vn[i - 1, j])))

    # 圧力の更新
    for i in range(1, ny - 1):
        for j in range(1, nx - 1):
            p[i, j] = (p[i, j] -
                       rho * (dx * dy) / (2 * dt) * ((u[i, j + 1] - u[i, j - 1]) / (2 * dx) +
                                                      (v[i + 1, j] - v[i - 1, j]) / (2 * dy)))
```

最後に、結果の可視化を行います。

```python
# 結果の可視化
plt.figure(figsize=(10, 5))
plt.contourf(u, cmap='viridis')
plt.colorbar()
plt.title('x方向速度成分')
plt.show()

plt.figure(figsize=(10, 5))
plt.contourf(v, cmap='viridis')
plt.colorbar()
plt.title('y方向速度成分')
plt.show()

plt.figure(figsize=(10, 5))
plt.contourf(p, cmap='viridis')
plt.colorbar()
plt.title('圧力')
plt.show()
```

これで、2次元キャビティ流れの基本的な数値シミュレーションと可視化ができます。必要に応じてパラメータや境界条件を調整して、特定の問題に適した結果を得ることができます。

---
なお、Visual Studio Codeを使うとJupyterNotebookでPythonコードが実行できます。

![](../images/cavity_flow_sample1.png)

![](../images/cavity_flow_sample2.png)

