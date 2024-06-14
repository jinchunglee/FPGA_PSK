# 基於FPGA實現PSK調變之期末報告
班級：電機三乙<br>
報告撰寫者：李晉丞<br>
學號：11028257<br>
組員：林士軒、張星瑀、盧翊禎<br>
-----------------------------
## 1. 引言 Introduction
### 1.1 背景介绍 Background
相移鍵控 (PSK) 是一種數位調製技術，廣泛應用於無線通訊系統中。隨著通信系統對資料傳輸速率和可靠性的要求不斷提高，FPGA作為一種高度可程式設計的硬體平臺，為實現高性能的PSK調製和解調提供了理想的解決方案。

### 1.2 研究目的 Objective
本研究旨在利用FPGA實現PSK調製和解調，重點探討FPGA在實現PSK過程中所展現的高效性和靈活性。

## 2. 理輪基礎 Theoretical Background
首先，我們可以將PSK分類為以下幾種：
- BPSK
- QPSK
- M-PSK (M=2^n)等

### 2.1.1 BPSK (Binary Phase Shift Keying)
BPSK 是最簡單的PSK形式，通過兩個相位狀態（通常是0度和180度）來表示二進位資料的0和1。BPSK 的調製過程如下：

- 對於二進位“0”，載波信號保持初始相位。
- 對於二進位“1”，載波信號的相位會翻轉180度。

BPSK 的優點是其實現簡單且抗雜訊能力強，但其頻譜效率較低，即每個符號只能攜帶1bit資訊。

#### BPSK之頻譜效率
這裡要補通信調變的上課點

### 2.1.2 QPSK (Quadrature Phase Shift Keying)
QPSK 是另一種常見的PSK形式，通過四個相位狀態（0度、90度、180度和270度）來表示兩個二進位位元。因此，每個符號攜帶2 bits資訊，提高了頻譜效率。QPSK 的調製過程如下：

- “00” 對應於0度相位。
- “01” 對應於90度相位。
- “10” 對應於180度相位。
- “11” 對應於270度相位。


QPSK 的優點是頻譜效率較高，但相對於BPSK，其抗雜訊能力稍差。

### 2.1.3 PSK的優缺點

**優點：**
- PSK 調製技術簡單，實現成本低。
- BPSK 具有較強的抗雜訊能力，適用於低信噪比環境。
- QPSK 頻譜利用率高，適用於高速資料傳輸。

**缺點：**
- 隨著調製階數的增加（例如8PSK，16PSK），系統的抗雜訊能力會下降。
- PSK 在多徑傳播環境中可能會出現較嚴重的碼間干擾（ISI）。<br>

### 2.2 數位信號處理基礎 (Fundamentals of Digital Signal Processing)

PSK 調製和解調過程中涉及以下基本的數位信號處理技術：

- **濾波 (Filtering)：** 濾波器用於去除信號中的雜訊和干擾，提高信號品質。
- **混頻 (Mixing)：** 混頻器用於將基帶信號與載波信號進行混合，生成調製信號。
- **相位檢測 (Phase Detection)：** 在解調過程中，相位檢測器用於從接收到的信號中提取相位資訊，從而還原出原始資料。
- **同步 (Synchronization)：** 信號的時域和頻域同步是成功解調的關鍵。時域同步確保符號的準確定位，頻域同步則確保載波的頻率和相位一致。

PSK 是一種重要的數位調製技術，在現代通信系統中得到了廣泛應用。通過利用 FPGA 實現 PSK 調製和解調，可以進一步提高通信系統的性能和靈活性。


## 3. FPGA基礎 (Basics of FPGA)
### 3.1 FPGA簡介 (Introduction to FPGA)
FPGA是一種集成了大量可程式設計邏輯單元的晶片，具有高並行處理能力，非常適合即時信號處理任務。而本專題我們用的Basy3這塊板子。

### 3.2 FPGA的優點 (Advantages of FPGA)
- 高度可程式設計性
- 靈活性
- 高速即時處理


## 4. 系統設計 (System Design)
### 4.1 總體設計 (Overall Design)
本設計包括：
- 調製模組
- 解調模組
- 載波生成模組
- 控制模組(除頻器、M序列產生器、testbench)


各模組通過匯流排互連，形成完整的PSK系統。

### 4.2 模組劃分 (Module Division)
1. 調製模組負責將輸入的數位資料轉換為PSK信號
2. 解調模組則將接收到的PSK信號還原為數位資料
3. 載波生成模組生成所需的正弦波載波
4. 控制模組協調各部分的工作


## 5. Verilog實現 (Verilog Implementation)
### 5.1 調變模組 (Modulation Module)
```verilog
// PSK Modulation
always @(posedge clk_1M or negedge rst) begin
  if (!rst) begin
    Mod_out0 <= 0;
    Mod_out1 <= 0;
  end else begin
    if (M_out == 1'b0)
      Mod_out0 <= cos0;
    else
      Mod_out1 <= sin1;
  end
end
```

### 5.2 解調模組 (Demodulation Module)
```verilog
// PSK Demodulation
always @(posedge clk_1M or negedge rst) begin
  if (!rst)
    demod_out <= 0;
  else if (received_signal == cos0)
    demod_out <= 1'b0;
  else if (received_signal == sin1)
    demod_out <= 1'b1;
end
```
### 5.3 載波生成 (Carrier Generate)
在 Vivado 中生成正弦波和餘弦波通常是通過使用數字信號處理技術來實現的。以下是一些常見的方法和原理：

1. 查找表法 (Look-Up Table, LUT)
查找表法是生成正弦波和餘弦波最常見的方法之一。其基本思想是預先計算和存儲一個週期內的正弦波和餘弦波值，然後根據需要進行查詢和輸出。

2. 數字直接合成 (Direct Digital Synthesis, DDS)
DDS 是一種數字信號處理技術，通過數字方式生成各種波形，包括正弦波和餘弦波。DDS 系統包括一個相位累加器和一個查找表。

### 5.3.1 查找表法 (Look-Up Table, LUT)
**步驟：**
1. 生成查找表：
- 使用 **MATLAB** 或其他工具生成正弦波和餘弦波的一個週期內的數據點。
- 將這些數據點存儲在 Verilog 設計中的記憶體或陣列中。



2. 查找和輸出：
- 設計一個計數器，用於指示查找表中的位置。
- 根據計數器的值查詢對應的正弦波或餘弦波數據，並輸出。  



**例子(不是本專題內的程式碼)：**

```verilog
module sine_wave_gen(
    input wire clk,
    input wire rst,
    output reg [11:0] sin_out,
    output reg [11:0] cos_out
);

reg [7:0] addr;
reg [11:0] sine_lut[0:255];
reg [11:0] cosine_lut[0:255];

// Initialize the lookup table
initial begin
    $readmemh("sine_lut.hex", sine_lut);
    $readmemh("cosine_lut.hex", cosine_lut);
end

always @(posedge clk or posedge rst) begin
    if (rst) begin
        addr <= 8'd0;
    end else begin
        addr <= addr + 8'd1;
    end
end

always @(posedge clk) begin
    sin_out <= sine_lut[addr];
    cos_out <= cosine_lut[addr];
end

endmodule

```

### (補充)使用 MATLAB 生成查找表
使用 MATLAB 可以方便地生成正弦波和餘弦波的查找表數據，並導出為 Verilog 可以讀取的格式。

```matlab
N = 256;
sine_lut = sin(2*pi*(0:N-1)/N) * 2047 + 2048;
cosine_lut = cos(2*pi*(0:N-1)/N) * 2047 + 2048;

fileID = fopen('sine_lut.hex','w');
fprintf(fileID, '%03X\n', round(sine_lut));
fclose(fileID);

fileID = fopen('cosine_lut.hex','w');
fprintf(fileID, '%03X\n', round(cosine_lut));
fclose(fileID);
```

然而，上述程式碼對於波形繪畫並不細膩，因為只取了零點與上下峰值點。如下圖所示：
![alt text](image-2.png)

所以將要對上述程式碼進行修改，以下是對細膩度的程式碼修改：



### 5.3.2 數字直接合成 (Direct Digital Synthesis, DDS)
**步驟：**
1. 相位累加器：
- 設計一個相位累加器，用於生成相位增量。每個時鐘週期增加固定的相位增量。
- 相位累加器的輸出作為查找表的地址。

2. 查找和輸出：
- 用相位累加器的輸出查詢查找表，獲取對應的正弦波或餘弦波值。


**例子(不是本專題內的程式碼)：**
```verilog
module dds_wave_gen(
    input wire clk,
    input wire rst,
    input wire [31:0] phase_inc, // Phase increment
    output reg [11:0] sin_out,
    output reg [11:0] cos_out
);

reg [31:0] phase_acc;
reg [7:0] addr;
reg [11:0] sine_lut[0:255];
reg [11:0] cosine_lut[0:255];

// Initialize the lookup table
initial begin
    $readmemh("sine_lut.hex", sine_lut);
    $readmemh("cosine_lut.hex", cosine_lut);
end

always @(posedge clk or posedge rst) begin
    if (rst) begin
        phase_acc <= 32'd0;
    end else begin
        phase_acc <= phase_acc + phase_inc;
    end
end

assign addr = phase_acc[31:24];

always @(posedge clk) begin
    sin_out <= sine_lut[addr];
    cos_out <= cosine_lut[addr];
end

endmodule
```


### 5.輸出結果
![alt text](image-1.png)
我們該如何解讀這張圖，首先先看到**測試Binary訊號**。這個是一個隨意生成的輸入Binary訊號。<br>

這個輸入Binary訊號是利用M-ary產生器，所產生的信號。

#### 
以下 Verilog 代碼展示了兩個模塊的實例化：一個是 clk_div，另一個是 M_gen。這兩個模塊協同工作來生成一個多相序列（M序列）。

1. clk_div 模塊
代碼分析：
```verilog
clk_div #(
  .Max (500)  // 設定參數 Max 的值為 500
) u3 (
  .clk(clk),  // 輸入時鐘信號
  .rst(rst),  // 輸入重置信號
  .clk_div(clk_M)  // 輸出分頻後的時鐘信號
);
```
#### 解釋：

- 參數設置：

1. Max (500)：這個參數設定了分頻器的最大計數值。在這個例子中，分頻器將計數到 500。


- 端口連接：

1. clk：輸入的時鐘信號。
2. rst：輸入的重置信號，用於初始化或重置模塊。
3. clk_div：輸出的分頻時鐘信號。在這裡，該信號被命名為 clk_M，表示這是經過分頻後的時鐘信號。


clk_div 模塊通過計數來實現時鐘分頻，當計數達到設定的最大值 Max 時，輸出時鐘信號 clk_div 翻轉一次。這樣，輸出時鐘的頻率會是輸入時鐘的頻率除以 (Max+1)。


2. M_gen 模塊
代碼分析：
```verilog
M_gen #(
  .width (8)  // 設定參數 width 的值為 8
) u4 (
  .clk(clk_M),  // 輸入分頻後的時鐘信號
  .rst(rst),    // 輸入重置信號
  .M_out(M_out)  // 輸出 M 序列信號
);
```

#### 解釋：
- 參數設置：

1. width (8)：這個參數設定了 M 序列生成器的寬度。在這個例子中，M 序列的寬度為 8 位。


- 端口連接：

1. clk：輸入的分頻後的時鐘信號，這裡是 clk_M。
2. rst：輸入的重置信號，用於初始化或重置模塊。
3. M_out：輸出的 M 序列信號。


M_gen 模塊是一個多相序列生成器，通常用於生成偽隨機序列。這些序列在數字通信、加密和測試中廣泛應用。M 序列（Maximum Length Sequence）是由線性反饋移位寄存器 (LFSR) 生成的，具有最大周期性且擁有良好的統計特性。





再來，將載波利用






