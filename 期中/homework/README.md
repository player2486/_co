# 期中作業－Homework 1

## 使用工具與來源說明

* **參考教材**：[nand2tetris](https://gittest2121.gitbook.io/nand2tetris)
---

## 1. And / And16

* 原理：`And(a,b) = Not(Nand(a,b))`

```text
 a ──┐
     NAND ── Not ── out
 b ──┘
```

* `And16` 將 16 組 `And` 並排，逐位元運算（bitwise AND）

---

## 2. DMux / DMux4Way / DMux8Way

### DMux 完成方法

* 使用 `sel` 控制輸出流向
* 當 `sel=0` → 輸出到 `a`
* 當 `sel=1` → 輸出到 `b`

```text
        sel ── Not ──┐
 in ── And ───────── a
 in ── And(sel) ──── b
```

### DMux4Way

* 使用 **一個 DMux 將輸入分成兩半**
* 再用兩個 DMux 進一步分流

### DMux8Way

* 第 1 層：依 `sel[2]` 分成 4-bit 與 4-bit 群組
* 第 2 層：各自交由 `DMux4Way`

```text
            sel[2]
               │
          ┌────DMux────┐
        abcd         efgh
         │               │
   DMux4Way        DMux4Way
```

---

## 3. Mux / Mux16 / Mux4Way16 / Mux8Way16

### Mux 完成方法

* 核心公式：

```text
out = (a AND NOT sel) OR (b AND sel)
```

```text
 a ── And ──┐
      Not   │
 sel ───────┘
             Or ── out
 b ── And(sel)
```

### Mux16

* 對 16 個 bit 各自套用 `Mux`

### Mux4Way16

* 先用 `sel[0]` 選 ab / cd
* 再用 `sel[1]` 決定最終輸出

### Mux8Way16

* 兩組 `Mux4Way16`
* 最後用 `Mux16` 合併

```text
      Mux4Way16 ── abcd ──┐
                           Mux16 ── out
      Mux4Way16 ── efgh ──┘
              sel[2]
```

---

## 4. Not / Not16


* `Not` 使用 Nand 自反：

```text
Not(a) = Nand(a,a)
```

* `Not16` 為 16 組 `Not`

---

## 5. Or / Or16 / Or8Way / Xor

### Or 完成方法

* 使用 **德摩根定律**：

```text
Or(a,b) = Not( Nand( Not(a), Not(b) ) )
```

### Or8Way

* **串接**

```text
(((in0 OR in1) OR in2) ... OR in7)
```

### Xor

* 邏輯定義：

```text
Xor = (a OR ¬b) AND (¬a OR b)
```

```text
 a ──┐        ┌── Or ──┐
     │ Not    │        │
 b ──┘        └── And ── out
```
---

# Homework 2：ALU 與加法器

## 使用工具與來源說明

* **參考教材**：[nand2tetris](https://gittest2121.gitbook.io/nand2tetris)
---

## 1. HalfAdder

* `sum = a XOR b`
* `carry = a AND b`

```text
a ──┐        ┌── XOR ── sum
    ├── AND ── carry
b ──┘
```
---

## 2. FullAdder
* 流程：

  1. a + b
  2. (a+b) + c
  3. 合併進位

```text
a,b ── HalfAdder ── sumab, carryab
sumab,c ── HalfAdder ── sum, temp
carry = temp OR carryab
```
---

## 3. Add16

* 最低位使用 HalfAdder，其餘使用 FullAdder
* 每一位的 carry 傳遞至下一位

```text
bit0 → HalfAdder → c1 → FullAdder → c2 → ... → c16
```
---

## 4. Inc16

* 將輸入加上常數 1

```text
out = in + 1
```

* 實作上：

  * `b[0]=true`
  * 其餘位元預設為 0
---

## 5. ALU
---

### (1) zx / nx 處理流程

```text
x → Mux(zx) → zerox → Not → notx → Mux(nx) → inpx
```

* `zx=1` → x 變成 0
* `nx=1` → x 取反

---

### (2) zy / ny 處理流程

* 與 x 相同邏輯

```text
y → zeroy → noty → inpy
```

---

### (3) f 運算選擇

```text
f=0 → AND16(inpx, inpy)
f=1 → ADD16(inpx, inpy)
```

* 使用 `Mux16` 選擇結果

---

### (4) no 與 ng

* `no=1` → 輸出取反
* `ng` 直接取 `out[15]`

```text
ng = out[15]
```

---

### (5) zr（是否為 0）

* 將 16 位分成兩組 Or8Way
* 若任一 bit 為 1 → 非 0

```text
zr = NOT( OR(out[0..15]) )
```

---

### 電路整體結構

```text
x,y → preprocess → AND / ADD → postprocess → out
                     │                     │
                     └──────── flags ◄─────┘
```
---

# Homework 3：Sequential Logic（暫存器與記憶體）

## 使用工具與來源說明

* **參考老師的**
---

## 3-a. Bit

* 核心為 **DFF（Data Flip-Flop）**
* 使用 `Mux` 決定是否寫入新值

```text
         load
           │
 in ──┐    ▼
     Mux ──► DFF ──► out
 do ──┘
```

* `load = 1`：寫入 `in`
* `load = 0`：保持原本的 `do`
---

## 3-a. Register（16-bit）

* 所有 Bit 共用同一個 `load`

```text
Register = Bit × 16
```

---

## 3-a. PC（Program Counter）

### 功能說明

* PC 儲存目前指令位置
* 需支援：

  * `reset`：歸零
  * `load`：直接跳躍
  * `inc`：+1


```text
reset > load > inc > hold
```

### 資料流設計

```text
out → Inc16 → oInc
  │
  ├─ Mux(inc)
  ├─ Mux(load)
  └─ Mux(reset)
        ↓
     Register
```

* 最後統一寫回 Register
---

## 3-a. RAM8 / RAM64

### RAM8 完成方法

* 使用 `DMux8Way` 將 `load` 分配到 8 個 Register
* 使用 `Mux8Way16` 依 address 讀取

```text
address → DMux → load[i]
Register[i] → Mux → out
```
---

## 3-b. RAM512 / RAM4K / RAM16K

* **分層式記憶體架構（Memory Hierarchy）**
* 每一層：

  * DMux → 寫入選擇
  * Mux → 讀取選擇

---

### RAM512

```text
8 × RAM64
address[6..8] → 選 bank
address[0..5] → bank 內位址
```

---

### RAM4K

```text
8 × RAM512
address[9..11] → 選 bank
address[0..8] → bank 內位址
```

---

### RAM16K

```text
4 × RAM4K
address[12..13] → 選 bank
address[0..11] → bank 內位址
```
---
# Homework 4：Machine Language（Hack Assembly）

## 使用工具與來源說明

* **參考網路作者**：[Sake](https://github.com/sake92/nand2tetris/tree/master/projects/04)
---

## 4-a. Mult.asm（整數乘法）

### 題目說明

* 計算 `R0 × R1`
* 結果存入 `R2`
* 僅能使用加法與跳躍（Hack 指令集限制）

---

### 完成方法

*  **重複加法（Repeated Addition）**

```text
R2 = 0
while (R1 != 0) {
  R2 = R2 + R0
  R1 = R1 - 1
}
```
* 每次迴圈將 `R0` 加到 `R2`
* 使用 `R1` 作為計數器
---

## 4-b. Fill.asm（螢幕填滿）

* 持續監聽鍵盤輸入
* **有按鍵** → 畫面全黑
* **無按鍵** → 畫面全白

### 完成方法

#### (1) 顏色控制

* 使用變數 `color`
* `0` 代表白色
* `-1` 代表黑色（16-bit 全 1）

---

#### (2) 鍵盤判斷

```text
if (KBD > 0) → BLACK
else → WHITE
```

* 使用 `D;JGT` 判斷是否有按鍵

---

#### (3) 螢幕填色

* SCREEN 共 **8192 個 word**
* 位址範圍：

```text
16384 ~ 24575
```

* 使用指標 `pixels` 逐一寫入

```text
pixels = SCREEN
while (pixels < 24576) {
  M[pixels] = color
  pixels++
}
```

# Homework 5：Computer Architecture（CPU / Computer / Memory）

## 使用工具與來源說明

* **參考網路作者**：[Sake](https://github.com/sake92/nand2tetris/tree/master/projects/05)
---

## 5-a. CPU（核心處理器）【最困難】

### CPU 的角色

* 負責 **解碼指令、運算、寫入暫存器、控制 PC**
* 支援 Hack 指令集：

  * **A-instruction**
  * **C-instruction**

---

### (1) 指令判斷（Instruction Decode）

```text
instruction[15] = 0 → A-instruction
instruction[15] = 1 → C-instruction
```

* 使用 `Not` 與 `Or` 判斷指令型態

---

### (2) A Register 載入邏輯

```text
loadA = isAInstruction OR (isCInstruction AND dest==A)
```

* A 指令：直接載入常數
* C 指令：dest 包含 A 時，載入 ALU 結果

---

### (3) D Register

* 僅在 **C 指令且 dest=D** 時寫入

```text
loadD = isCInstruction AND dest==D
```

---

### (4) ALU 運算

* x = D Register
* y = A Register 或 M（由 a bit 決定）
* c1~c6 控制 ALU 行為

```text
ALU(x=D, y=A/M) → outALU
```

* ALU 同時產生 `zr`、`ng` 旗標

---

### (5) writeM（是否寫入記憶體）

```text
writeM = isCInstruction AND dest==M
```

* 僅 C 指令可寫記憶體

---

### (6) Jump 判斷（重點）

* 依 ALU 輸出旗標決定是否跳躍

```text
JGT : out > 0
JEQ : out = 0
JLT : out < 0
```

```text
jump = (JGT && pos) || (JEQ && zero) || (JLT && neg)
```

* 若 jump 成立 → `PC = A`
* 否則 → `PC++`

---

### CPU 資料流總覽（簡化）

```text
instruction → decode → registers → ALU → flags
                               │        │
                               └── PC ◄─┘
```

---

## 5-b. Memory（記憶體）

### Hack 記憶體配置

| 裝置     | 位址範圍            |
| ------ | --------------- |
| RAM    | 0x0000 – 0x3FFF |
| SCREEN | 0x4000 – 0x5FFF |
| KBD    | 0x6000          |

---

* DMux 決定 **寫入對象**
* Mux 決定 **輸出來源**

```text
address → DMux → RAM / SCREEN / KBD
```
---

## 5-c. Computer

### 系統組成

```text
ROM32K → CPU → Memory
```

* ROM 提供指令
* CPU 執行指令
* Memory 提供資料與 I/O

---

### reset 行為

* `reset=1` → PC 歸零
* 程式重新執行

