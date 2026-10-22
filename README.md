# 作業五：PWM and Logic Analyzer

## 基本題：PWM 波形

使用 **STM32 B-L475E-IOT01A** 的 **TIM2_CH1（PA15／D9）** 輸出 PWM，以 **ZEROPLUS LAP-C(16128)** Logic Analyzer 觀察。應用情境為 LED 亮度控制；本次實作及驗證的是 PWM 訊號，**沒有另外接 LED 測試亮度**。

| 項目 | 設定／結果 |
|---|---|
| Timer Clock | 4 MHz |
| Prescaler (PSC) | 3 |
| Period (ARR) | 999 |
| PWM 頻率（理論值） | 1 kHz |
| PWM 週期（理論值） | 1000 μs |
| Duty Cycle | 每 5 秒循環切換 25% → 50% → 75% |

Logic Analyzer 的 A0 通道有觀察到規律方波，三種占空比的高、低電位時間比例也有跟著改變。

| Duty Cycle | 理論高電位時間 | 理論低電位時間 | 波形觀察 |
|---|---:|---:|---|
| 25% | 250 μs | 750 μs | 高電位較短 |
| 50% | 500 μs | 500 μs | 高、低電位時間接近 |
| 75% | 750 μs | 250 μs | 高電位較長 |

> 表中的 μs 數值由 Timer 設定計算，並非 Logic Analyzer 游標的實測讀值。

## 加分題 Option 1(a)：Input Capture

使用 **TIM3_CH1（PB4／D5）** 接收 TIM2 的 PWM 輸出；以一條杜邦線連接 **PA15（D9）→ PB4（D5）**。TIM3 的 Prescaler 設為 3、ARR 設為 65535，在 4 MHz Timer Clock 下，每個計數相當於 1 μs。

程式透過 Input Capture 記錄訊號邊緣的計數值，計算 PWM 週期、高電位時間、頻率與占空比；在 STM32CubeIDE 的 Debug Expressions 查看結果。

### 實測結果

| 項目 | PWM 設定／理論值 | TIM3 實測值 |
|---|---:|---:|
| 頻率 | 1000 Hz | **1000 Hz** |
| 週期 | 1000 μs | **1000 μs** |
| 高電位時間 | 750 μs | **750 μs** |
| Duty Cycle | 75% | **75%** |

以上為 **75% Duty Cycle** 時的 Input Capture 結果。

### 結果討論

TIM3 量得的 75% PWM 數據與 TIM2 設定一致，證明使用 Timer Input Capture 可以量測 PWM 的週期與占空比。TIM3 的計數解析度為 **1 μs**，因此微小時間差可能受到計數取整和時脈誤差影響。另外，本次程式以捕捉中斷發生時 PB4 的電位判斷上升緣或下降緣；若訊號更快或中斷延遲過大，可能影響判斷準確度。

## 專案與 Demo

- **開發環境：** STM32CubeMX、STM32CubeIDE 2.2.0
- **基本題接線：** PA15（D9）→ Logic Analyzer A0；STM32 GND → Logic Analyzer GND
- **加分題接線：** PA15（D9）→ PB4（D5）（同一塊板子內部共地）
- **Demo：** 展示 Logic Analyzer 上的三種 PWM 波形，以及 STM32CubeIDE Expressions 的 Input Capture 測量數據

## 參考資料

- [PWM — Wikipedia](https://en.wikipedia.org/wiki/Pulse-width_modulation)
- [ST — How to use the input capture feature](https://community.st.com/t5/stm32-mcus/how-to-use-the-input-capture-feature/ta-p/704161)
