# 訂餐系統設計與實現
## 11224227郭秉恆、11224211李崇勛

# 1.
## 一、系統設計目標與核心理念

本訂餐系統以 高可擴充性、可維護性、分層解耦、真實商業情境 為設計核心，模擬實際外送平台或餐廳訂餐系統的後端架構。系統採用 分層架構（Layered Architecture） 並結合 DDD（Domain-Driven Design） 與 事件導向（Event-driven） 的思想，確保系統能應付高併發、功能演進與商業規則複雜化。

## 二、整體系統分層架構

<img width="1200" height="900" alt="order_system_architecture (1)" src="https://github.com/user-attachments/assets/59c2bbd9-52fe-438d-a21c-326ebd1137d6" />
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

# 2.
## 一、需求說明書（Software Requirements Specification, SRS）

1. 系統目的
本訂餐系統旨在模擬真實商業環境下的線上訂餐平台後端系統，支援使用者下單、付款、訂單管理，並能在高併發與需求變動下保持高可維護性與可擴充性。

2. 使用者角色定義
角色	說明
一般使用者	瀏覽菜單、下訂單、付款、查詢訂單
餐廳	接收訂單、更新訂單狀態
系統管理者	系統監控、異常處理

4. 功能性需求（Functional Requirements）
3.1 使用者訂餐功能

使用者可建立訂單
使用者可新增 / 移除訂單項目
系統自動計算訂單金額（含稅、服務費）
訂單狀態管理（Created / Paid / Cancelled / Completed）

3.2 付款功能
<img width="1500" height="950" alt="payment_p2p" src="https://github.com/user-attachments/assets/175bf656-cebd-4749-8c89-a4f50c26604b" />

系統可串接第三方金流服務
支援付款成功 / 失敗回傳
付款成功後更新訂單狀態

3.3 訂單查詢

使用者可查詢訂單歷史
管理者可查詢全系統訂單

4. 非功能性需求（Non-functional Requirements）
4.1 效能需求

支援高併發下單（每秒 1,000 筆以上）
訂單建立平均回應時間 < 300ms
4.2 可用性與可靠性
系統可用性 99.9%
金流失敗不影響訂單資料一致性

4.3 可維護性

商業邏輯集中於 Domain Layer
可獨立替換資料庫與外部服務

5. 介面需求

提供 RESTful API
JSON 作為資料交換格式
API 與內部 Domain 完全解耦

<img width="1500" height="950" alt="order_domain_p2p" src="https://github.com/user-attachments/assets/6f990fbd-86f7-4b14-aa3a-0ae0358b97bc" />
<img width="1500" height="950" alt="payment_p2p" src="https://github.com/user-attachments/assets/0c4a5ed3-e911-4b78-a50b-5b64b966c169" />

<img width="1500" height="950" alt="place_order_p2p" src="https://github.com/user-attachments/assets/971ca3bc-0a10-4d06-8df7-8c7d249bb873" />

## 二、概要設計說明書（High-Level Design, HLD）
1. 系統架構總覽

系統採用 分層式架構：
Client
 ↓
Presentation Layer
 ↓
Application Layer
 ↓
Domain Layer
 ↓
Infrastructure Layer
 ↓
Database / External Services

2. 模組劃分

2.1 Presentation Layer
OrderController
PaymentController
Request / Response DTO

2.2 Application Layer

OrderApplicationService
PaymentApplicationService
Command / Query
Domain Event Publisher

2.3 Domain Layer

Aggregate Root：Order
Entity：OrderItem、User
Value Object：Money
Domain Event：OrderCreated、OrderPaid

2.4 Infrastructure Layer

OrderRepositoryImpl
PaymentGatewayAdapter
Message Queue Adapter

3. 介面設計原則

上層永遠不依賴下層實作
Domain 僅依賴介面
使用 Adapter Pattern 隔離第三方服務

## 三、詳細設計說明書（Low-Level Design, LLD）
1. Order Aggregate 設計
1.1 Order（Aggregate Root）
職責:
維護訂單狀態
管理 OrderItem
計算訂單總金額
核心方法
createOrder()
addItem()
removeItem()
pay()
cancel()

2. 訂單建立流程（邏輯）

Controller 接收請求
Application Service 開啟交易
建立 Order Aggregate
呼叫 Order.createOrder()
儲存 Order
發佈 OrderCreated Event

3. 付款流程（Event-driven）

Application Service 呼叫 Payment Adapter
收到成功回應
Order.pay()
發佈 OrderPaid Event
觸發後續流程（通知、配送）

4. Repository 設計

Domain 定義 Repository Interface
Infrastructure 實作資料庫存取
支援 ORM / SQL 切換

## 四、測試計畫（Test Plan）

1. 測試範圍
測試類型	內容
單元測試	Domain 邏輯（Order、Money）
整合測試	Application + Repository
API 測試	Controller
壓力測試	高併發下單
回歸測試	商業規則變更

3. 測試策略

Domain Layer：不依賴資料庫
使用 Mock Repository
Event 驗證狀態轉換

3. 測試資源

測試資料庫
模擬金流服務
CI/CD 自動化測試

## 六、可延伸進階主題

CQRS + Event Sourcing
微服務拆分（Order / Payment / Delivery）
Saga Pattern（跨服務交易）
高併發下的訂單鎖與一致性
