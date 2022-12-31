---
title: "Design Patterns in TypeScript"
date: 2022-12-31
draft: false
authors: ["nofear195"]
description: ""

categories: ["learning-note"]
series: [""]

lightgallery: true

---

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

