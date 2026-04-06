---
theme: default
title: 用 Python 打造高互動性的 Discord BOT
info: |
  ## 關於這份簡報
  嗨！我是上竣~
  我目前在一間 AI 新創公司從全端開發的工作，負責將公司所開發的 AI 技術包裝成各個產品。
  目前只有大約 4 年的工作經驗，較沒有開發大型系統的經驗，還請各位多多指教

  本次分享的是「內行人才知道的系統設計面試指南」第 12 章：設計聊天系統。

  簡報原始檔都放在[Github](https://github.com/CK642509/slidev_system_design_12)上，歡迎查看
class: text-center
drawings:
  persist: false
transition: slide-left
mdc: true
---

## 內行人才知道的系統設計面試指南

<br>

# 第 12 章：設計聊天系統

<br>

### 上竣
2026.04.06


---

# 定義問題

- 1 對 1、群組
- APP + Web
- 5000萬 DAU
- 群組聊天人數上限 100 人
- 文字訊息、顯示連線狀態
- 訊息 < 100000 個字元
- 暫不考慮端對端加密
- 聊天紀錄永久保存

---

# 聊天系統架構

核心功能

<br>

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-2.jpg)

- 可接收來自客戶端的訊息
- 針對每則訊息，都找出對應的接收者，並轉發給他
- 如果接收者不在線，則伺服器要先保留訊息，直到接收者上線

---
layout: two-cols
---

# 聊天系統架構

<br>

1. 無狀態服務
2. 有狀態服務
3. 第三方整合

<br>

<v-click>

- 通訊協定
- 資料儲存系統

</v-click>

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-7.jpg)


<!-- 
1. 無狀態服務 -> 常見功能，e.g. 登入、註冊；服務探索
2. 有狀態服務 -> 聊天系統
3. 第三方整合 -> 第 10 章：設計通知系統
-->

---

# 通訊協定

<br>

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-2.jpg)

<v-click>

- HTTP
  - 輪詢 (Polling)
  - 長輪詢 (Long Polling)
- Websocket

</v-click>

<v-click>

- SSE (Server-Sent Events)

</v-click>

---
layout: two-cols
---

# 通訊協定 - 輪詢

Polling

- 單向
- 較無效率


::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-3.jpg)

---
layout: two-cols
---

# 通訊協定 - 長輪詢

Long Polling

- 單向
- 依然效率不高

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-4.jpg)

---
layout: two-cols
---

# 通訊協定 - WebSocket

- 雙向
- 效率較高

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-5.jpg)

---

# 資料儲存系統

資料類型

<br>

<v-switch>
<template #1>

兩種資料：
- 通用型資料

- 聊天歷史資料

</template>
<template #2>

兩種資料：
- 通用型資料 --> RDBMS

- 聊天歷史資料 --> 鍵值儲存系統

<br>
<br>
<br>
<br>

第 6 章有討論怎麼設計 KV Store
</template>
</v-switch>





---
layout: two-cols
---

# 資料儲存系統

資料模型

<div class="class: flex justify-center items-center">
  <img
    class="w-95"
    src="https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-9.jpg"
    alt=""
  />
</div>

::right::

<br>
<br>
<br>

<div class="class: flex justify-center items-center">
  <img
    class="w-95"
    src="https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-10.jpg"
    alt=""
  />
</div>

---
layout: TwoCols75
---

# 資料儲存系統

訊息 ID

- `message_id` 必須是 unique
- 理想上是依照時間排序

<br>

<v-click>

- auto_increment 通常可以，但 NoSQL 通常沒有
- 參考第 7 章：設計可用於分散式系統的唯一 ID 生成器
  - snowflake 
  - UUID v7
  - Redis Incr

</v-click>

::right::

<div class="class: flex justify-center items-center">
  <img
    class="w-60"
    src="https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-9.jpg"
    alt=""
  />
</div>

<br>

<div class="class: flex justify-center items-center">
  <img
    class="w-60"
    src="https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-10.jpg"
    alt=""
  />
</div>

---
layout: two-cols
---

# 架構圖

1. 無狀態服務
2. 有狀態服務
3. 第三方整合
4. 通訊協定
5. 鍵值儲存系統

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-8.jpg)

---

# 細節討論

- 服務探索
- 訊息傳遞流程
  - 一對一
  - 多裝置同步
  - 群組
- 連線狀態指示

---
layout: two-cols
---

# 服務探索

1. 使用者 A 嘗試登入到 APP
2. 負載平衡器把登入請求發送到 API 伺服器
3. 後端對使用者進行身分驗證後，服務探索就會幫使用者 A 找出最佳的聊天伺服器
4. 使用者 A 透過 WebSocket 連接到聊天伺服器

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-11.jpg)

---
layout: two-cols
---

# 訊息傳遞流程 - 一對一

1. 使用者 A 向聊天伺服器 #1 發送聊天訊息
2. 聊天伺服器 #1 從 ID 生成器取得 `message_id`
3. 聊天伺服器 #1 把訊息發送給訊息同步佇列 (message sync queue)
4. 這個訊息會儲存到鍵值儲存系統中
5. 使用者 B
   - 正在連線：訊息轉發到使用者 B 所連接的聊天伺服器 #2
   - 已離線：從推送通知伺服器發送出一個推送通知
6. 聊天伺服器 #2 會把訊息轉發給使用者 B

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-12.jpg)

---
layout: two-cols
---

# 訊息傳遞流程 - 多裝置同步

- 每個裝置都有一個 `cur_max_message_id`
- 檢查鍵值儲存系統是否存在：
  - 訊息接受者 ID = 登入使用者 ID
  - `message_id` > `cur_max_message_id`

::right::

<br>
<br>

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-13.jpg)

---
layout: two-cols
---

# 訊息傳遞流程 - 群組

小群組

<div class="class: flex justify-center items-center">
  <img
    class="w-95"
    src="https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-14.jpg"
    alt=""
  />
</div>

::right::

<br>
<br>
<br>

<div class="class: flex justify-center items-center">
  <img
    class="w-95"
    src="https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-15.jpg"
    alt=""
  />
</div>

---
layout: two-cols
---

# 連線狀態指示

1. 登入
   
2. 登出

3. 斷線

::right::

心跳 (heartbeat) 機制

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-18.jpg)

---
layout: two-cols
---

# 連線狀態指示

小群組

4. 扇出 (fanout)

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-19.jpg)


---

# 其他

1.  錯誤處理
    1.  聊天伺服器錯誤
    2.  訊息重發
2.  支援圖片、影片
3.  端對端加密
4.  客戶端快取

---

<br><br><br><br>
<br><br><br><br>

<div class="flex justify-center">

# Thank you

</div>