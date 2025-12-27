# 訂餐系統設計與實現
## 11224227郭秉恆、11224211李崇勛

## 一、系統設計目標與核心理念

本訂餐系統以 高可擴充性、可維護性、分層解耦、真實商業情境 為設計核心，模擬實際外送平台或餐廳訂餐系統的後端架構。系統採用 分層架構（Layered Architecture） 並結合 DDD（Domain-Driven Design） 與 事件導向（Event-driven） 的思想，確保系統能應付高併發、功能演進與商業規則複雜化。

## 二、整體系統分層架構

<img width="900" height="600" alt="01_concept_architecture" src="https://github.com/user-attachments/assets/51103654-2507-4ab3-937d-2fa1d01291ca" />


分層總覽

Presentation Layer（表示層 / API層）

Application Layer（應用層）

Domain Layer（領域層 / 商業核心）

Infrastructure Layer（基礎設施層）

External Services（外部系統）

Client
  ↓
API / Controller
  ↓
Application Service
  ↓
Domain Model
  ↓
Repository / Infrastructure
  ↓
Database / External Service

<img width="381" height="679" alt="螢幕擷取畫面 2025-12-28 005556" src="https://github.com/user-attachments/assets/d123ef49-6ea8-4d45-8446-c176591dee1a" />

## 三、各層詳細設計說明

1️⃣ Presentation Layer（表示層）

職責

接收使用者請求（Web / App）

驗證輸入資料（DTO Validation）

將請求轉為 Application Command

回傳 Response DTO

主要元件

Controller

Request DTO

Response DTO

特點:不包含任何商業邏輯，僅負責「資料轉換與流程轉交」。

UML（表示層）

📌 附註：Controller 不知道訂單怎麼計價，只知道「要交給 Application 層處理」。

2️⃣ Application Layer（應用層）

職責

協調系統流程（Use Case）

控制交易（Transaction Boundary）

呼叫 Domain 物件

發佈 Domain Event

主要元件

Application Service

Command / Query

Domain Event Publisher

UML（應用層）

📌 附註：Application Service 是「流程導演」，但不是「演員」。

3️⃣ Domain Layer（領域層，系統核心）

職責

定義商業規則

保證資料與狀態的一致性

封裝複雜行為

核心概念

<img width="368" height="288" alt="螢幕擷取畫面 2025-12-28 005617" src="https://github.com/user-attachments/assets/a8702151-98ca-44bd-b495-1e41aee125d8" />

📌 附註：

Order 是 Aggregate Root

所有 OrderItem 的變動都必須經過 Order

4️⃣ Infrastructure Layer（基礎設施層）

職責

技術細節實現

資料庫存取

外部系統整合

主要元件

Repository Implementation

ORM / SQL

Message Queue Adapter

UML（基礎設施層）

📌 附註：Domain 只依賴 Repository 介面，不依賴資料庫。

5️⃣ 外部系統整合（External Services）

下單流程與金流概念圖:
<img width="143" height="413" alt="螢幕擷取畫面 2025-12-28 005621" src="https://github.com/user-attachments/assets/a17a6a2f-b0c3-4e40-acb3-38a1d2c6f645" />
<img width="189" height="188" alt="螢幕擷取畫面 2025-12-28 005627" src="https://github.com/user-attachments/assets/073d8cb0-d6b8-41f2-bfb6-c67fa44f7077" />

可能整合項目

金流系統（Payment Gateway）

推播系統（Notification）

第三方外送平台

UML（外部整合）

📌 附註：使用 Adapter Pattern，避免第三方污染核心系統。

使用者功能圖:
<img width="900" height="600" alt="02_usecase_diagram" src="https://github.com/user-attachments/assets/3e01fcaf-76ee-4cc1-9586-6e99ebe62527" />

下單流程順序圖:
<img width="900" height="600" alt="03_sequence_diagram" src="https://github.com/user-attachments/assets/eabe3fbf-f702-4adc-ae08-1d62959e0792" />


## 四、完整系統 UML 總覽（整合）
<img width="950" height="420" alt="order_system_architecture" src="https://github.com/user-attachments/assets/df09b8c3-c03a-4004-8bbf-a5ae3f6682ae" />

## 五、設計優點總結

本系統採用分層式架構結合 Domain-Driven Design，
將商業邏輯集中於 Domain Layer，
Application Layer 負責用例協調，
Infrastructure Layer 則封裝技術實作細節，
以提升系統的可維護性與擴充性。

✅ 高內聚、低耦合

✅ 商業邏輯集中於 Domain

✅ 易於測試（Domain 可單元測試）

✅ 可替換資料庫與外部服務

✅ 可演進為微服務架構

## 六、可延伸進階主題

CQRS + Event Sourcing
微服務拆分（Order / Payment / Delivery）
Saga Pattern（跨服務交易）
高併發下的訂單鎖與一致性
