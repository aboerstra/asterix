# Asterix ROI Calculator – Full Alignment Checklist

_A step-by-step guide for a developer to align the calculator with Asterix Global Services' business model and the recommendations from the logic review._

---

## 0  Preparation
1. **Create a feature branch**  
   ```bash
   git checkout -b feat/full-roi-alignment
   ```
2. **Install fresh dependencies & run linter/tests**  
   ```bash
   pnpm install
   pnpm run lint
   pnpm run test   # add in §6 if missing
   ```
3. **Locate key files**  
   * `src/App.jsx`  – state & main maths  
   * `src/components/...` (if you break out new sliders)  
   * `public/`, `vite.config.js`, `package.json`

---

## 1  Monetise Retention & Upsell Improvements
### 1.1  State / UI
Add (if not already present) sliders in the **Business Metrics ➜ Additional Revenue Drivers** section:
```jsx
const [retentionImprovement, setRetentionImprovement] = useState(2)   // %
const [upsellingRate,       setUpsellingRate]       = useState(8)   // %
```
*Tooltip text*  → _"Percent lift in client retention / upsell rate delivered by better experience."_

### 1.2  Formulas (`calculateResults`)
```jsx
// ▲ place after revenueEnablement
const retentionRevenue = clientsPerYear * revenuePerClient * (retentionImprovement / 100);
const upsellRevenue    = clientsPerYear * revenuePerClient * (upsellingRate        / 100);

// ▼ add to benefits
const totalAnnualBenefits = laborEfficiency + errorReduction + softwareConsolidation +
                            docHandlingBenefit + revenueEnablement +
                            retentionRevenue + upsellRevenue;
```
Expose `retentionRevenue` & `upsellRevenue` in the returned object for display/export.

### 1.3  Output Tables & Exports
* Add new rows in the on-screen summary, PDF and Excel export.

---

## 2  Remove Unused Legacy Variables

---

## 3  Compliance-Risk Fine Avoidance (optional but recommended)
### 3.1  State / Slider
```jsx
const [complianceFineRisk, setComplianceFineRisk] = useState(25000); // $ baseline p.a.
const [riskReductionRate,  setRiskReductionRate]  = useState(60);    // % avoided
```

### 3.2  Formula integration
```jsx
const residualRisk      = complianceFineRisk * (1 - riskReductionRate / 100);
const complianceSavings = complianceFineRisk - residualRisk;

const docHandlingBenefit = docHandlingSavings + riskBufferSavings + complianceSavings; // extend existing line
```
### 3.3  UI additions
* Slider in **Document Handling & Risk** card.  
* Add row *"Compliance fine avoidance"* in savings table/export.

---

## 4  Unit Tests (Vitest)
1. `pnpm add -D vitest @testing-library/react`  (if not present).  
2. Create `src/__tests__/calc.spec.ts` covering:
   * **Baseline checks:** all benefits = 0 when all improvement sliders = 0.  
   * **Capacity only:** capacityIncrease → revenueEnablement only.  
   * **Retention vs upsell:** ensure new revenue lines add correctly and are not part of payback.
3. Add CI step in GitHub Actions.

---

## 5  Sensitivity & Sanity Guards
* Increase `sensitivityData` array to include retention & upsell.  
* Warn if any percentage slider >100 or <0 (clamp values via min/max on sliders).

---

## 6  UX / Docs
1. **Section descriptions** – update copy to reflect new drivers & costs.  
2. **README** – add explanation of new formulas and default values.  
3. **Tooltips** – clearly state what is and is not included in payback vs ROI.

---

## 7  Release Workflow
1. `git commit -am "feat: full ROI alignment (retention, upsell, opex, compliance)"`
2. Push branch & open PR for review.  
3. After approval → merge → `pnpm run deploy` to publish.

---

### ✔ Deliverable
After completing all steps above, the calculator will:
* Reflect **all** hard & soft ROI levers cited in the strategy brief.  
* Avoid double-counting while capturing previously-omitted revenue & cost items.  
* Provide transparent cost breakdown for executive scrutiny. 