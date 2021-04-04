# Atom3 冷卻風扇重新設計之旅

Atom3 從 2019年拿到至今，邁入第三年。期間大大小小的問題，最棘手的莫過於冷卻風扇吹的方向是不對的，這個是 Atom3 的散熱風扇外罩，雖然很堅固，完整度很高，但是其實風量集中的區域並不是噴頭與出料口的地方，常常會有下列的問題

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9261.jpg)

- 大於60度的小圓弧夾角就會有來不及冷卻導致外殼會翹起來的問題
- 快殼的翹起容易導致噴頭撞到而毀掉印件
- 翹起的外殼在每一層的黏合出現問題
- 散熱太慢，導致熱堆積（雖然這個可以用一些小技巧改善，例如增加列印距離）

> 恐怖的回抽地獄小豬就是小夾角的圓弧尖端，印的非常差。

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9252.jpg)

總之印的不好會讓人覺得厭世，所以最後不得不自救改變這個可憐的散熱系統...

## 72 小時馬拉松設計

![](/assets/3dprinter/design/cooling-fan-upgrade/fanv0-fanv9.gif)

> 一旦開始，就無法停止的男人

總共設計到第 9 個版本才調整到出風口覺得滿意

### 第一版

左右兩邊風扇，原本想用原本的螺絲孔位，但是花了四小時設計完才發現，用原本的螺絲孔位會因為出風口太接近噴頭，導致溫度過高而融化，而且設計的角度也不夠大，所以根本吹不到噴頭以下

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-v1.jpg)

### 第二版

用的是新孔位，也就是先把印件鎖在滑台上，再把風扇固定在印件上，可行。但是印件與滑臺的角度互相抵消還不夠，所以整個是畸形的外翻，而且發現沒有挖空後方會卡到風扇，變成轉不動...

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-v2.jpg)

### 第三版

一樣用的是新孔位，但是這一版就把後面挖空了，不然風扇會卡住，雖然這一版的角度是正確的，但是因為角度太立了，導致平台自動校正的時候會頂到熱床，變成校正失敗，連桿都撞掉了。

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-v3.jpg)

### 第四版

感覺這版很完美，風扇的角度是對的，結構也很堅固，但是沒想到會上方厚度太厚卡到柱子...

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-v4.jpg)

其實設計到這一版之後還是失敗，實在是有點想放棄，覺得怎麼那麼難。但是我就是不服輸的男人，不相信自己做不到

### 第五版

不服輸的男人捲起秀子，整個打掉重新設計，用新孔位、後方挖空、重新測量角度，連滑台上的對應點都設計上了，經過四個小時，再度列印挑戰，這版非常棒，孔位風向都有對到，但是我覺得可以再把出風口往前延伸，應該散熱會更棒

*這一版還很風騷的加上 Logo*

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-v5.jpg)

### 第六版

接近完整版了，延伸的出風口也吹到噴頭了，但是我發現風會往上飄，所以追求完美的我再次調整設計圖，讓出風口的上緣突出一點

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-v6.jpg)

### 第七版

- 不會卡到風扇
- 不會卡到柱子
- 不會卡到熱床
- 自動校正會卡到，但是不影響校正
- 出風口對準噴頭，而且風向是往下
- 外型也好看，完整度很高 (自己講)
- 改善風向，讓風往下跑

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-v7.jpg)

### 第八版

細修之後，這次就是心目中完美的一版了

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-back-v10.jpg)

裝到後吹的散熱風扇...不可能一切那麼順利的，多列印了一個之後，發現後方的風扇位置根本跟前面兩個完全不一樣...所以必須要重新設計...

### 第九版，後吹散熱風扇

用新孔位，因為後吹散熱風扇在自動校正的時候剩下的空間非常小，所以直接把後面的部分刪除了，但是在拆支撐的時候因為結構很脆弱，所以還拆壞一個。裝上後跑自動矯正發現後吹的風扇得用原本的螺絲孔位，因為散熱塊是往前的，把風扇往下拉一點也可以節省跟柱子之間的距離。

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-back-v9.jpg)

### 第十版，後吹散熱風扇

孔位準到不行，一整個設計度爆表

![](/assets/3dprinter/design/cooling-fan-upgrade/fan-back-v10.jpg)

## 側吹外殼與熱風扇合體

![](/assets/3dprinter/design/cooling-fan-upgrade/collage-3.jpg)

## 後吹外殼與熱風扇合體

![](/assets/3dprinter/design/cooling-fan-upgrade/collage-4.jpg)

## 裝上滑台

前方兩側風扇

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9262.jpg)

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9263.jpg)

完美的出風角度 (自己講)

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9264.jpg)

後方的風扇

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9266.jpg)

完美的出風角度 (自己講)

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9267.jpg)

## 測試成品

45 ~ 60 度完全沒有問題

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9268.jpg)

印一個比較複雜的測試圖形，升級風扇前後並沒有影響到原本的能力

![](/assets/3dprinter/design/cooling-fan-upgrade/collage-1.jpg)

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9278.jpg)

但是這次的升級就是要解決下圖這個例子，這是夾角 80 度的小尖端，改善得非常的明顯。左邊在最末端已經變形到無法自拔了，右邊依然保持著小夾角的尖端，至少要後處理都還有機會。

![](/assets/3dprinter/design/cooling-fan-upgrade/collage-2.jpg)

俯視圖更明顯

![](/assets/3dprinter/design/cooling-fan-upgrade/batch_IMG_9282.jpg)

經過這次 72 小時的馬拉松設計經驗後，我想~ 我應該會越來越自己設計，哈...