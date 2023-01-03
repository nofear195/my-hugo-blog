---
title: "Design Patterns in TypeScript"
date: 2022-12-31
lastmod: 2023-01-04
draft: false
authors: ["nofear195"]
description: ""

categories: ["learning-note"]
series: [""]

lightgallery: true

---

For more practice code, please click the github repository --> https://github.com/nofear195/ts-design-pattern :smile: :smile: :smile:
<!--more-->

## Reference

1. [7天學會設計模式：設計模式也可以這樣學](https://www.drmaster.com.tw/Bookinfo.asp?BookID=MP22251)
    - 書中程式碼範例撰寫語言為 JAVA
    - 本篇文章參照書中程式碼，並依照練習需求改寫為用 TypeScript 撰寫
2. [Jest + TypeScript：建置測試環境](https://titangene.github.io/article/jest-typescript.html)

## Factory

- Practice code in TypeScrpt : [Factory pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/factory)

### Simple Factory

- 概念:建立一個 class(factory)，可隨著不同的輸入，取得相同抽象的類別實體
- 方法:
    1. 建立一個共同行為的抽象 (interface or class)
    2. 實作或繼承所需的類別
    3. 建立一個類別(factory) ，其中可藉由不同的輸入，返回特定實作抽象的類別實體
- 簡單工廠,又稱靜態工廠
- 優點:封裝類別實體的建立過程
- 缺點:違反 OCP 開放/封閉原則，加入新的類別時，會改動 factory 的內容

### Factory

- 概念: 抽象化 factory 的概念，只定義 factory 產出的方法，具體實作由子類別各自實現
- 方法：
    1. 建立一個共同行為的抽象 interface or class
    2. 實作或繼承所需的類別
    3. 建立ㄧ個抽象(factory)，定義類別實體的方法
    4. 實作所需類別的 factory
    5. 實體的建立，只能由該 factory 類別產生

### Abstract Factory

- 概念: 將產生實體所需要的相關功能(屬性,方法) 再各自進行抽象，形成一系列的 factory
- 方法：
    1. 建立額外功能的 factory
    2. 建立基礎實體類別的 factory, 並在其中實體化額外功能的 factory
    3. 實體的建立，一樣只能由該基礎 factory 類別產生

## Singleton

- Practice code in TypeScrpt : [Singleton pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/factory)
- 概念: 為確保此類別只會產出一個物件，在建立該類別時，便實體化該物件，且需提供唯一存取的方法
- 方法:
    1. 建立 private 的屬性，並將實體化的物件 assign 給該屬性
    2. 建立 private 的 constructer，確保該物件不會再被實體化
    3. 建立 public static 方法，取得唯一的類別實體
- 若建立該物件實體需要花費龐大資源，則實體可以延後至該類別第一次被呼叫時進行實體化

## Strategy

- Practice code in TypeScrpt : [Strategy pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/strategy)
- 概念: 將各種不同的策略(演算法)，包裝成類別，以便在需要時可快速切換不同的策略
- 方法:
    1. 建立呼叫策略方法的介面
    2. 實作不同的策略類別
    3. 在需要使用多個策略的類別中，加入呼叫策略的方法
    4. 切換不同策略時，直接帶入所需策略的實體，即可使用該策略
- 與工廠模式的差異
    factory pattern : 專注於如何去建立物件，而非使用
    strategy pattern : 專注於依照當前所需，由外部帶入不同的策略做使用

## Decorator

- Practice code in TypeScrpt : [Decorator pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/decorator)
- 概念: 像俄羅斯娃娃般，如同 "同心圓" 般一層覆蓋一層，由小至大，在原有的功能上再包裹一層作用(功能)
- 方法:
    1. 建立一致功能的抽象方法
    2. 依照需求在不同的實作階層上，實作該共同功能
    3. 使用該類別的實體時，可動態依照實作類別，動態附加功能於實體上

## Observer

- Practice code in TypeScrpt : [Observer pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/observer)
- 概念: 在觀察者(observer) 介面中，提供方法供被觀察者(subject)一次呼叫多個實作其介面的類別，用於處理物件之間一對多的連動關係
- 方法:
    1. 建立 obserser 介面，提供被呼叫 or 觸發時對應的方法
    2. 依照不同的需求，實作被觸發時，對應的做法
    3. 在一個被觀察的物件 (subject) 中, 建立一個可以觸發 observer 的方法，達到一對多的連動與互動的效果

## Command

- Practice code in TypeScrpt : [Command pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/command)
- 概念: 將 command(request) 封裝成不同的物件形式,用於分源分流處理不同的 command(request)
- 方法:
    1. 建立 receiver 介面方法，並依照需做分別去實作
    2. 建立 command 介面，實作可接收 receviver 實體，與呼叫該 receiver 方法的類別
    3. 建立 invoker 類別，呼叫不同的 command
- 特色:
    1. 建立一個中介的角色 invoker, 用來轉接所有的 command 給對應的 receiver
    2. invoker 可將不同的 command 進行處理的優先順序排列
    3. invoker 可自行控管不同 receiver 可接收的 coomand 數量, 也可取消未處理的 command
    4. invoker 可將不同的 command 進行處理的優先順序排列

## Adapter

- Practice code in TypeScrpt : [Adapter pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/adapter)
- 概念: 一個轉接器的功能，讓原本無法作用功能的實體，載入另一個可執行功能的實體，使原本的實體可藉由載入的實體，去完成原本無法作用的功能
- 方法:
    1. 在 adapter 初始化中，放入之後需要轉接功能的實體
    2. 定義 function 函式, 用來呼叫需要轉接實體的功能

## Facade

- Practice code in TypeScrpt : [Facade pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/facade)
- 概念: 使用一個類別，用來封裝直接與各個子系統的功能，並可以製作一連串子系統之間的互動的功能
- 方法:
    1. 在 facade 類別的初始化中，放入所有會互相作用的類別實體
    2. 定義 function 函式, 用來定義內部類別實體的相互作用方式
- 特色:
    1. 封裝一個大系統中，各個子系統間的互動方式，外部行為者只需要跟 facade 的介面進行互動即可
    2. 如同遙控器一樣，整合各式的工具，供使用者可以方便去使用
    3. web service 中的 MVC 架構即是採用此 pattern 的做法
    4. 為最常見且被應用的 pattern 之一

## Template

- Practice code in TypeScrpt : [Template pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/template)
- 概念: 定義一套方法或流程(演算法)的結構，但實作的細節則由實作的子類別去實現，並藉由 hook 去切換不同的實作方法
- 方法:
    1. 定義一個實體作為 template 的 input, 供 template 類別改變其內容
    2. 定義 template 介面方法，表達演算法的結構
    3. 實作介面演算法結構的 template 類別實體
    4. 在 template 實體中，加入 hook，用來判定如何切換不同的 template 實體
    5. 定義 function 函式，用來將 input 套用在當下的 template，並返回與 input 相同的類別，供給下一次套用 template 使用

## Composite

- Practice code in TypeScrpt : [Composite pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/composite)
- 概念: 用於處理樹狀結構的資料，即當資料有階層式的關係，而每個階層都可抽象出共同行為時
- 方法:
    1. 定義一個抽象,搜集階層中 node 共同擁有的行為或方法，讓所有實體的 node 都可以去繼承並實作
    2. 在 node 介面上加入 add, remove 等新增或刪除的方式，用於改變當前樹狀結構的方式
    3. 分別以各個階層所需要的實作方式進行實作，eg: root, child, leaf 等

## State

- Practice code in TypeScrpt : [State pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/state)
- 概念: 讓物件的不同狀態封裝成各式的類別，用來表達物件會隨著不同狀態而有著不同的行為
- 方法:
    1. 在一個需要表達不同狀態的物件中，定義一個可切換當前屬性狀態的函式
    2. 定義不同 state 物件，接收當前須改變的狀態物件，改變其屬性狀態
    3. 在不同 state 物件中，插入可以再變更成另一種 state 物件的條件方式，用於再使 input 物件，切換成不同的狀態物件
- 特色:
    1. React framework 中藉著 state 監聽資料的狀態變化，渲染不同的 UI 畫面
    2. Redux, Vuex, Pinia 等前端狀態管理工具，都可有效率管控不同的資料狀態被改變的方式與來源，以確保每一個狀態的改變都可以被追蹤

## Proxy

- Practice code in TypeScrpt : [Proxy pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/proxy)

### Dynamic Proxy

- 概念: 為被代理的物件提供一個緩衝，讓被代理的物件完成初始化或某些動作前，執行其他的動作(代理)
- 方法:
    1. 將需要被代理的物件方法作為一個動態代理的抽象
    2. 實作抽象的行為，並在初始化中放入被代理的物件
    3. 讓 client 只能藉由動態代理的方式，呼叫被代理物件的方法
- 特色:
    1. 包覆需要代理的物件，使其可以有餘裕完成本身需要被執行方法或執行初始化
    2. 與 decorator pattern 方法不同在於 decorator 可以包裹很多層，而 proxy 一般只會包裹一層
    3. 可以將 decorator pattern 作為 proxy pattern 的延伸或特化版

### Protect Proxy

- 概念: 為被代理的物件提供一種保護，限制外在行為對其的存取行為
- 方法:
    1. 分析需要被代理的對象物件不同行為，形成一個抽象
    2. 實作抽象的行為，並在初始化中放入被代理的物件
    3. 將需要限制存取行為的方法，回傳為一個空值或訊息，藉以限制被代理的物件其屬性被更改
- 特色:
    1. 當引用第三方撰寫的程式時，可藉由限制代理作為一個前置的保護方式，用來避免引用的程式被誤用或亂用，而造成非預期的副作用

## Builder

- Practice code in TypeScrpt : [Builder pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/builder)
- 概念: 建立一個 director(指揮者)類別，用來封裝並指揮一個由多個不同類別組成的複雜實體，其類別實體化過程的先後順序
- 方法:
    1. 拆解複雜實體的結構來源，找出其引入的類別實體
    2. 找出複雜實體在實體化過程中，其內部引入類別的使用順序
    3. 在 director 類別初始化中，放入一個尚未引入內別類別的複雜實體
    4. 在 director 類別中建立組成函式，並依照分析後的順序，逐一將需要引入的類別實體，放入 director 內部的複雜實體中
- 特色:
    1. 如同指派一個師傅，由他去指揮一個團隊去組裝一個機器人，由一個機器人空殼，至能作用各式功能的機器人助手
    2. 與 abstract factory pattern 的差異，在於 Builder pattern 強調其由無到有的 “順序”

## Chain Of Responsibility

- Practice code in TypeScrpt : [Chain Of Responsibility pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/chainOfResponsibility)
- 概念: 定義一個介面函式，讓不同的物件處理 (實作) 同一個請求 (request)，並讓實作介面者(handler) 在達成特定條件下傳遞給另一個 handler 處理相同的請求
- 方法:
    1. 將請求 (request) 這個動作封裝成一個類別，並設置可傳遞給不同 handler 呼叫的條件屬性與對應的存取函示，即定義一個責任鏈(chain of resposibility)
    2. 將可處理 request 的 handler 抽象成一個介面(抽象類別)，並在初始化中，放入在達成請求時，下個處理相同請求的 handler
    3. 實作 handler 的抽象類別，定義應對請求方式的條件，並處理請求
- 特色:
    1. 相較 if else 的方式，包裝在達成條件下，呼叫相關的實體處理相同的請求，使用此 pattern 可以在不更動 hanlder 前提下，藉由更動 request 的屬性，設置可以傳達到某一個責任鏈階段的 handler

## Interpreter

- Practice code in TypeScrpt : [Interpreter pattern](https://github.com/nofear195/ts-design-pattern/tree/main/src/interpreter)
- 概念: 一系列的翻譯工具、解譯器，用來表達或轉換輸入的語言文字意思
- 方法:
    1. 定義轉換輸入的界面函示，然後實作各式的翻譯方法
    2. 走訪輸入的文字並設置不同的條件，用以切換不同的翻譯方法
