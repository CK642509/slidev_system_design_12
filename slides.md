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

1. RDBMS
2. KV Store --> 第 6 章有討論怎麼設計 KV Store

---

# 資料儲存系統

1. 資料模型
2. Message ID

---
layout: two-cols
---

# 架構圖

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

1. OOO

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-11.jpg)

---
layout: two-cols
---

# 訊息傳遞流程 - 一對一

1. OOO

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-12.jpg)

---
layout: two-cols
---

# 訊息傳遞流程 - 多裝置同步

1. OOO

::right::

<br>
<br>

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-13.jpg)

---
layout: two-cols
---

# 訊息傳遞流程 - 群組

<br>

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-14.jpg)

::right::

<br>
<br>
<br>

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-15.jpg)

---
layout: two-cols
---

# 連線狀態指示

1. 登入
   
2. 登出

3. 斷線

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-18.jpg)

---
layout: two-cols
---

# 連線狀態指示

4. 扇出 (fanout)

::right::

![](https://raw.githubusercontent.com/Admol/SystemDesign/main/images/chapter12/figure12-19.jpg)