
## 第 6 章：Assembler（組譯器）

 **Hack 組合語言（Assembly）轉換成機器碼（Binary）** 包含：

* 處理 A-instruction（@value）
* 處理 C-instruction（dest=comp;jump）
* 建立 Symbol Table（labels、變數）

* 作業形式為軟體程式（非 HDL）
* **理解整體流程**，但實作細節對我來說偏困難

* 參考：

  * GitHub 上的 [sake](https://github.com/sake92/nand2tetris/tree/master/projects)
  全部複製sake沒修改
---

## 第 7 章：VM I – Stack Arithmetic

實作 **Virtual Machine（VM）**，負責把 VM 指令轉換為 Hack Assembly。

第 7 章重點在：

* Stack-based 架構
* Arithmetic / Logical 指令（add, sub, neg, eq, gt, lt, and, or, not）

* 理解 stack 操作（push / pop）
* 對比較指令（eq / gt / lt）較不熟，尤其是：

  * label 產生
  * jump 條件控制

* 參考：

  * GitHub 上的 [sake](https://github.com/sake92/nand2tetris/tree/master/projects)
* 修改狀態：

  * 有理解後自行修改

---

## 第 8 章：VM II – Program Control


第 8 章延續 VM Translator，加入：

* function / call / return
* 多檔案 VM 程式
* 呼叫堆疊（call stack）

* **這一章是我覺得最難的一章之一**
* call / return 的流程理解困難：

  * push return address
  * 保存 LCL / ARG / THIS / THAT
  * frame 與 ret 的處理

* 參考：

  * GitHub 上的 [sake](https://github.com/sake92/nand2tetris/tree/master/projects)
  全部複製sake沒修改

---

## 第 9 章：High-Level Language（Jack 語言）

* 使用 Jack 語言撰寫小程式
* 熟悉：

  * class
  * function / method
  * while / if

* Jack 語言語法大致能理解
* 實作難度相對前面章節低
* 可以閱讀並理解範例程式

* 參考：

  * GitHub 上的 [sake](https://github.com/sake92/nand2tetris/tree/master/projects)
  全部複製sake沒修改
---

## 第 10 章：Compiler I – Syntax Analysis

* 撰寫 Jack Compiler 的前半段
* Tokenizer（詞法分析）
* Parser（語法分析）
* 輸出 XML syntax tree

* 能理解 Tokenizer 的角色
* Parser 規則很多，實作困難

* 參考：

  * GitHub 上的 [sake](https://github.com/sake92/nand2tetris/tree/master/projects)
  參考sake修改
  參考 楊丞皓 修改
* 狀態：

  * 未獨立完成

---

## 第 11 章：Compiler II – Code Generation

* 將 syntax tree 轉換成 VM code
* symbol table（class / subroutine）
* expression / statement 處理

* 屬於「**幾乎看不懂，無法實作**」
* code generation 流程複雜
* 多層抽象（Jack → VM → ASM）

* 僅閱讀：

  * GitHub 上的 [sake](https://github.com/sake92/nand2tetris/tree/master/projects)
  * GitHub 完整專案

---

## 第 12 章：Operating System

* 撰寫 Jack OS
* 包含：

  * Memory
  * Math
  * String
  * Array
  * Output
  * Keyboard

* 僅閱讀官方解答與他人版本
* 理解 OS 是提供高階語言可用的基礎函式庫

* 參考：

  * nand2tetris 官方 OS 解答
---

