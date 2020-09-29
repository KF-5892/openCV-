# Opencvで輪郭抽出

参考にしたページ

https://github.com/kujirahand/book-mlearn-gyomu

使用した画像

https://unsplash.com/photos/gDPaDDy6_WE

## 概要

りんごが写っている写真の輪郭を抽出し、線で描画する。

#### 環境

Googlecolaboratory

### 手順

1. opencvで画像読み込み＋リサイズ
2. 画像をグレイスケール化
3. 画像をぼかす
4. 画像を２値化
5. 輪郭を抽出＋書き出し

### 必要なライブラリ

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
```

### 画像読み込み・リサイズ

任意の画像を作業ディレクトリにアップロード。ここでは上記のページからダウンロードした画像を「apple_sample.jpg」と名前をつけている。

元の画像のサイズが大きいため、cv2.resizeで、画像を200×133pxにリサイズしている

```python
img = cv2.imread("apple_sample.jpg")
img = cv2.resize(img, (200, 133))
```

#### グレイスケール化・ぼかし・二値化

opencvで輪郭は、画像の明暗の差で判断している。そのため、白黒以外の色をなくす処理を行う。また、ぼかして二値化することで、はっきりと判別できるように処理する。

```python
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY) # グレイスケール化
gray1 = cv2.GaussianBlur(gray, (7, 7), 0) # ぼかし処理
im2 = cv2.threshold(gray1, 140, 240, cv2.THRESH_BINARY)[1] # 二値化
```

#### 輪郭を抽出

findcontrours関数を使用して輪郭を抽出。変数cntsに代入する。

##### 第２引数は輪郭検出モード。

**RETR_LIST**：全ての輪郭を抽出

**RETR_EXTERNAL**：最も外側の輪郭のみ検出

**RETR_CCOMP**：相対的階層（親子）を持って輪郭を抽出

**RETR_TREE**：絶対的階層を持って輪郭を抽出　など



##### 第３引数は輪郭の近似の方法を指定。

**CHAIN_APPROX_NONE**：輪郭上の全ての点を検出

**CHAIN_APPROX_SIMPLE**：4点のみで検出



なお、戻り値として輪郭の情報と階層情報の２つが返される。今回は輪郭の情報のみ使用。

```python
cnts = cv2.findContours(im2, 
        cv2.RETR_LIST,
        cv2.CHAIN_APPROX_SIMPLE)[0]
```

抽出した枠を描画

```python
for pt in cnts:
    x, y, w, h = cv2.boundingRect(pt)
    # 大きすぎたり小さすぎたり領域を除去
    if w < 30 or w > 150: continue
    cv2.rectangle(img, (x, y), (x+w, y+h), (0, 255, 0), 2)
```

matplotlib を使用して結果を表示する

```python
plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
```

#### 結果



<img width="650" alt="スクリーンショット 2020-09-29 13 09 54" src="https://user-images.githubusercontent.com/68985919/94511789-56670580-0255-11eb-9f14-45be62d455e6.png">
