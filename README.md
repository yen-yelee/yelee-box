# TLM Contact Resistance Analyzer

A single-file, browser-based tool for analyzing **Transmission Line Method (TLM)** measurements. Paste I–V data for up to five channel lengths and get sheet resistance, contact resistance, transfer length, specific contact resistivity, and per-channel conductivities. No installation, no server, no build step.

[한국어 설명은 아래](#한국어)

## Files

| File | Description |
|---|---|
| `tlm_analyzer_en.html` | English interface |
| `tlm_analyzer.html` | Korean interface (same calculation logic) |

## Quick start

1. Open either HTML file in a modern browser (double-click is enough).
2. For each enabled Column, paste **voltage [V]** and **current [A]** (same number of points, separated by newline, comma or space) and enter the film thickness **t [nm]**.
3. Press **Calculate**.

Press **Fill example data** to load a synthetic data set (R_sh = 50 Ω/□, R_C = 20 Ω) that the tool should reproduce exactly.

> The chart library (Chart.js) is loaded from a CDN, so an internet connection is needed for the plot. All calculations run locally in your browser; no data leaves your machine.

## Inputs

- **5 columns**, one per channel spacing: L = 200, 400, 600, 800, 1000 μm.
- Per column: voltage list [V], current list [A], thickness t [nm].
- Voltage and current may have **any number of points** (≥ 2), as long as the counts match. A partial range (e.g. 0–1.5 V) is fine.
- Each column has an **on/off switch**. Disabled columns are ignored completely, even if they still contain data. At least **2 columns** must be enabled.
- Channel width is fixed at **W = 4000 μm**.

## Method

1. **Per-channel resistance.** Least-squares fit of current vs. voltage. `slope = I/V [A/V = S]`, and `R_total = 1/slope [Ω]`.
2. **R_total–L fit.** Linear fit of R_total against L:

   ```
   R_total(L) = (R_sh/W)·L + 2R_C  =  a·L + b
   ```

3. **Extracted parameters.**

   | Quantity | Expression | Unit |
   |---|---|---|
   | Sheet resistance | `R_sh = a · W` | Ω/□ |
   | Contact resistance | `R_C = b / 2` | Ω |
   | Transfer length | `L_T = R_C / a` (equivalently `R_total = (R_sh/W)(L + 2L_T)`) | μm |
   | Specific contact resistivity | `ρ_C = R_C · L_T · W` | Ω·μm² (also shown as Ω·cm²) |

4. **Conductivity per channel** (computed in SI, displayed in S/cm = S/m × 0.01):

   | Quantity | Expression |
   |---|---|
   | Intrinsic conductivity σ₀ | `σ₀ = 1 / (R_sh · t)` |
   | Effective conductivity σ_eff | `σ_eff = L / (R_total · W · t)` |

   σ₀ excludes the contact contribution (uses R_sh only); σ_eff includes it (uses each channel's measured R_total). The thickness t is taken per column, so films with varying thickness are handled.

## Outputs

- R_total–L plot with the linear fit, the extrapolation to the x-intercept (−2L_T), and a **Save as PNG** button.
- Table of per-channel slope and R_total, and table of per-channel t, σ₀, σ_eff. Both tables are laid out **horizontally**: drag-select a row and paste it into Excel.
- Extracted parameters: R_sh, R_C, L_T, ρ_C, and the fit equation.

## Unit conventions

| Quantity | Input unit | Used internally |
|---|---|---|
| Voltage | V | V |
| Current | A | A |
| L, W | μm | μm for R_sh, ρ_C; converted to m for σ |
| Thickness t | nm | converted to m (× 10⁻⁹) |

Keep the length units consistent: the R_total–L slope is in Ω/μm, so W must be in μm (4000), not in m.

## Customizing

Constants are at the top of the `<script>` block:

```js
const W_FIXED = 4000;                   // μm, channel width
const L_VALUES = [200, 400, 600, 800, 1000]; // μm, one per column
```

Change them to match your pattern geometry.

## Assumptions and limitations

- I–V curves are assumed **linear (ohmic)** over the entered range. Enter only the linear region if the curve bends.
- Ideal TLM assumptions: uniform film, identical contact pads, current flow confined to the channel.
- The fit has **no uncertainty estimate and no R² output**; with 2 columns the line passes exactly through both points.
- `ρ_C = R_C · L_T · W` is equivalent to `R_sh · L_T²` for an ideal TLM structure.
- Only five channel spacings are supported by the interface.

## Verification

With synthetic data generated from R_sh = 50 Ω/□, R_C = 20 Ω, W = 4000 μm (five columns, 0–10 V and 101 points, or three columns with 0–1.5 V and 16 points), the extracted values are R_sh = 50.00 Ω/□, R_C = 20.00 Ω, L_T = 1600 μm, ρ_C = 1.28 Ω·cm².

## References

- H. H. Berger, "Models for contacts to planar devices," *Solid-State Electronics* **15**, 145–158 (1972).
- G. K. Reeves and H. B. Harrison, "Obtaining the specific contact resistance from transmission line model measurements," *IEEE Electron Device Letters* **3**, 111–113 (1982).
- D. K. Schroder, *Semiconductor Material and Device Characterization*, Wiley.

Please verify the citation details against the original publications before citing them.

## Dependencies and license

- [Chart.js](https://www.chartjs.org/) (MIT License), loaded from cdnjs and not redistributed in this repository.
- Add your own `LICENSE` file for this project.

---

## 한국어

### 개요

TLM(Transmission Line Method) 측정 데이터를 분석하는 **단일 HTML 파일** 도구입니다. 설치나 서버 없이 브라우저에서 바로 열어 사용합니다. 계산은 모두 브라우저 안에서 이루어지며 데이터는 외부로 전송되지 않습니다. 그래프용 Chart.js만 CDN에서 불러오므로 그래프를 보려면 인터넷 연결이 필요합니다.

| 파일 | 설명 |
|---|---|
| `tlm_analyzer.html` | 한국어 화면 |
| `tlm_analyzer_en.html` | 영어 화면 (계산 로직 동일) |

### 사용법

1. HTML 파일을 브라우저에서 엽니다.
2. 사용할 Column마다 **전압 [V]**, **전류 [A]** (개수 동일, 줄바꿈/쉼표/공백 구분)와 **두께 t [nm]**를 입력합니다.
3. **계산하기**를 누릅니다.

### 입력 규칙

- L = 200, 400, 600, 800, 1000 μm에 해당하는 Column 5개, 채널 폭 **W = 4000 μm** 고정
- 전압·전류는 2개 이상이면 몇 개든 가능합니다 (예: 0–1.5 V만 입력해도 됨)
- 각 Column의 스위치를 끄면 값이 남아 있어도 계산에서 완전히 제외됩니다. 최소 2개 Column이 켜져 있어야 합니다.

### 계산 방법

1. 각 Column에서 전류–전압 선형 피팅 → `slope = I/V`, `R_total = 1/slope`
2. R_total–L 선형 피팅: `R_total(L) = (R_sh/W)·L + 2R_C = a·L + b`
3. `R_sh = a·W`, `R_C = b/2`, `L_T = R_C/a`, `ρ_C = R_C·L_T·W` (접촉비저항)
4. 진성 전도도 `σ₀ = 1/(R_sh·t)`, 유효 전도도 `σ_eff = L/(R_total·W·t)` (S/m로 계산 후 ×0.01 하여 S/cm로 표시)

### 출력

R_total–L 그래프(PNG 저장 가능), 채널별 결과 표, 채널별 전도도 표(엑셀에 가로로 붙여넣기 가능), 추출된 파라미터(R_sh, R_C, L_T, ρ_C)

### 주의사항

- 입력 구간에서 I–V가 선형(ohmic)이어야 합니다. 비선형이면 선형 구간만 입력하세요.
- 피팅의 불확실도와 R²는 계산하지 않습니다.
- 기울기 단위가 Ω/μm이므로 W는 μm(4000)로 곱해야 합니다. m로 곱하면 10⁶배 어긋납니다.
- `L_VALUES`, `W_FIXED` 상수를 코드 상단에서 바꾸면 다른 패턴 간격과 폭에 맞출 수 있습니다.
