---
title: "Design Patterns in TypeScript"
date: 2022-12-31
lastmod: 2023-01-02
draft: false
authors: ["nofear195"]
description: ""

categories: ["learning-note"]
series: [""]

lightgallery: true

---

For more practice code, please click the github repository --> https://github.com/nofear195/ts-design-pattern :smile: :smile: :smile:
<!--more-->

{{< admonition type=info title="infomation"  >}}
For more practice code, please click the github repository [ts-design-pattern](https://github.com/nofear195/ts-design-pattern) :smile: :smile: :smile:
{{< /admonition >}}

## Reference

1. [7天學會設計模式：設計模式也可以這樣學](https://www.drmaster.com.tw/Bookinfo.asp?BookID=MP22251)
    - 書中程式碼範例撰寫語言為 JAVA
    - 筆者參照書中程式碼，依照練習需求改寫為 TypeScript
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
