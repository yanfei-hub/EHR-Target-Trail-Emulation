# 🎯 EHR Target Trial Emulation: Bias Diagnostics Toolkit

<div align="center">

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Made with Jupyter](https://img.shields.io/badge/Made%20with-Jupyter-orange?logo=Jupyter)](https://jupyter.org/try)


[Quick Start](#-quick-start) • [Why This Exists](#-why-this-exists) • [What You Get](#-what-you-get) • [Notebooks](#-interactive-notebooks) • [Examples](#-real-world-scenarios)

</div>

---

## 🚨 The Problem

You're analyzing EHR data. You've carefully defined your target trial protocol. You've run propensity score matching. You've estimated a treatment effect.

**But have you checked if your study is even identifiable?**

EHR data aren't passively observed experiments—they're produced by complex care processes where:

- ⏰ **Time zero is ambiguous** → immortal time bias
- 💊 **Treatment switching is rampant** → ill-defined strategies  
- 🏃 **Patients disappear** → informative censoring
- ⚖️ **Contraindications exist** → structural non-overlap
- 🔍 **Sicker patients get more tests** → differential outcome detection
- 🏥 **Multi-site heterogeneity** → broken pooled estimands

Most EHR studies discover these problems *after* publishing. This toolkit helps you find them *before*.

---

### Who This Is For

- 📊 **Researchers** using EHR data for causal inference
- 🔬 **Epidemiologists** conducting target trial emulation
- 👨‍⚕️ **Health services researchers** working with real-world data
- ✅ **Reviewers & editors** evaluating observational studies
- 🎓 **Students** learning causal inference with messy data

---

## 🎁 What You Get

### 🔬 Four Interactive Diagnostic Notebooks

Each notebook is a **complete tutorial** with:
- ✅ Toy data simulation demonstrating the bias
- ✅ Visual diagnostics with one-click plots
- ✅ Decision rules (proceed/downgrade/stop)
- ✅ Reporting language for your paper

| Notebook | What It Diagnoses | Red Flags |
|----------|------------------|-----------|
| **🕐 Time Zero Alignment** | Immortal time, baseline misalignment | Treatment defined post-baseline, guaranteed survival windows |
| **💊 Treatment Strategies** | Strategy operationalizability, ITT clarity | Undefined switching rules, fragmented capture |
| **🏃 Follow-up & Censoring** | Informative censoring, IPCW stability | Outcome-dependent dropout, unstable weights |
| **⚖️ Causal Contrasts** | Overlap, exchangeability, competing events | Structural non-overlap, unmeasured confounding |

### 📋 Fast-Pass Decision Checklist

A **6-checkpoint workflow** to decide whether to proceed, modify, or stop your study:

```
✓ Eligibility → ✓ Time Zero → ✓ Treatment Strategy → 
✓ Censoring → ✓ Outcome Detection → ✓ Overlap
                            ↓
                    🎯 Identifiable Study
                            OR
                    🛑 Fundamental Barrier
```

### 📝 Drop-In Reporting Language

Pre-written limitation statements and estimand definitions you can adapt for your manuscript:

```markdown
"Despite adjustment for measured confounders, residual confounding 
from [disease severity, social determinants] may persist. We estimated 
the average treatment effect in the overlap population (ATO), 
representing 73% of the original cohort..."
```

---

## 🚀 Quick Start

### Installation

```bash
git clone https://github.com/yourusername/ehr-target-trial-diagnostics.git
cd ehr-target-trial-diagnostics
pip install -r requirements.txt
jupyter notebook
```

### Run Your First Diagnostic

```python
# Example: Check for immortal time bias
import pandas as pd
from diagnostics import time_zero_check

# Your data: patient_id, treatment_start, outcome_date, baseline_date
df = pd.read_csv('my_cohort.csv')

# One-line diagnostic
results = time_zero_check(df)
results.plot()  # Visual diagnostic
results.decision()  # → "STOP: 35% of patients have guaranteed survival time"
```

---

## 📚 Interactive Notebooks

### 1. 🕐 Time Zero Alignment (`time_zero_bias_tutorial.ipynb`)

**The Problem:** EHR systems record *when drugs are documented*, not when treatment decisions are made. Patients who die early never get their prescriptions recorded.

**What You'll Learn:**
- Distinguish onset, encounter, decision, and documentation times
- Detect immortal time with survival curve diagnostics  
- Understand when time-zero problems are unfixable

**Real Example:**
```python
# Simulated scenario: 40% event rate difference that's pure bias
baseline_choice_hr = 0.62  # Appears protective
true_effect_hr = 1.00      # Actually null
# → Entire effect is immortal time bias!
```

### 2. 💊 Treatment Strategies (`treatment_strategies_assignment.ipynb`)

**The Problem:** "Initiate drug A" sounds simple. But in EHR: patients switch, discontinue, get prescriptions from outside systems, and modify dosing.

**What You'll Learn:**
- Operationalize ITT in settings with fragmented exposure
- Diagnose exposure completeness and switching patterns
- Decide when to use cloning-censoring-weighting (and when not to)

**Decision Framework:**
```
Exposure capture >90% → Proceed with ITT
Exposure capture 70-90% → Sensitivity analysis required  
Exposure capture <70% → Consider PP or stop
```

### 3. 🏃 Follow-up & Censoring (`followup_and_censoring.ipynb`)

**The Problem:** Patients don't just "leave the study"—they disengage *because* they're getting sicker or experiencing side effects.

**What You'll Learn:**
- Identify outcome-dependent vs. treatment-dependent censoring
- Evaluate IPCW weight stability (ESS, max weight)
- Recognize when censoring is fundamentally informative

**Weight Stability Rules:**
```
Max IPCW weight <10, ESS >50% → Proceed
Max weight 10-50, ESS 30-50% → Report with caveats
Max weight >50, ESS <30% → STOP (unstable)
```

### 4. ⚖️ Causal Contrasts & Overlap (`causal_contrasts_and_overlap.ipynb`)

**The Problem:** Drug B is contraindicated for high-risk patients. Your "effect estimate" is extrapolation into impossible counterfactuals.

**What You'll Learn:**
- Diagnose structural non-overlap with propensity score plots
- Understand when trimming changes your estimand (it's not failure!)
- Calculate E-values for unmeasured confounding sensitivity

**Estimand Decisions:**
```
Good overlap → ATE (average treatment effect)
Moderate non-overlap → ATO (overlap population)
Severe non-overlap → STOP or ATT with asymmetric assumptions
```

---

## 🎬 Real-World Scenarios

### Scenario 1: Antidiabetic Drug Comparison

```python
# Check structural overlap
ps_model = LogisticRegression()
ps_model.fit(X_baseline, treatment)
ps_scores = ps_model.predict_proba(X_baseline)[:, 1]

# Diagnostic plot
plot_propensity_overlap(ps_scores, treatment)
# Result: 12% of patients in extreme PS regions → Trim or use overlap weights
```

**Finding:** Drug B contraindicated for eGFR <30. After trimming to overlap region, effect estimate changes from HR=0.75 to HR=0.88.  
**Decision:** Report ATO in manuscript: "Effect in patients without contraindications..."

### Scenario 2: Hospital vs. Home Dialysis

```python
# Check informative censoring
censoring_model = fit_ipcw_model(baseline_covariates, censoring_indicator)
weights = calculate_ipcw_weights(censoring_model)

# Evaluate stability
print(f"Max weight: {weights.max()}")  # → 87 (!)
print(f"ESS: {calculate_ess(weights)}")  # → 23% of N
```

**Finding:** Patients who feel worse preferentially transfer out of system. IPCW weights are unstable.  
**Decision:** STOP. Report that informative censoring cannot be adequately controlled.

### Scenario 3: Cancer Treatment in Multi-Site Network

```python
# Check site-level heterogeneity
for site in sites:
    site_effect = estimate_effect(data[data.site == site])
    # Results: HR ranges from 0.45 to 1.32 across sites
    
# Overlap diagnostic
overlap_by_site = check_positivity_by_site(data)
# Finding: 3/10 sites have structural non-overlap
```

**Finding:** Pooled effect masks fundamental heterogeneity. Some sites can't identify effect.  
**Decision:** Report site-stratified effects. Do NOT pool into single estimate.

---

## 🎯 Decision Checkpoint Reference

Use this table **before estimating any treatment effect**:

| Checkpoint | Stop Signal | Proceed Signal |
|-----------|------------|----------------|
| **Eligibility** | Cohort entry strongly care-driven, selection cannot be characterized | Clear entry criteria, minimal care-seeking selection |
| **Time Zero** | Exposure defined post-baseline, guaranteed survival windows | Aligned eligibility, treatment, follow-up at baseline |
| **Treatment Strategy** | ITT undefined, exposure <70% complete, rampant switching | Clear operationalizable strategies, >90% capture |
| **Censoring** | Outcome-dependent disengagement, max IPCW >50 | Independent censoring or stable IPCW (max <10) |
| **Outcome** | Detection differs by arm/risk, "detected-in-system" outcome | Uniform ascertainment, objective outcomes |
| **Overlap** | Structural non-overlap, >50% trimmed, severe imbalance | Good positivity, <20% trimmed, balance achieved |

**Rule of Thumb:** If you hit 2+ "stop signals," consider fundamentally redesigning your study.

---


## 📚 Reference
- Hernán & Robins (2016). "Using Big Data to Emulate a Target Trial..."
- Hernán et al. (2022). "Target Trial Emulation: A Framework for Causal Inference from Observational Data"
- Groenwold et al. (2021). "Informative Missingness in Electronic Health Record Systems"
- Digitale et al. (2023). "Tutorial on Directed Acyclic Graphs" 
- Lash et al. (2020). *Modern Epidemiology* 4th ed., Chapter on Bias Analysis
- Pearl (2009). *Causality*, Chapter on Identifiability

---

## 📄 License

MIT License - see [LICENSE](LICENSE) for details.

---

## 🌟 Citation

If this repository helps your research, please cite the following paper:

```bibtex
@article{wang2026operational,
  author  = {Wang, Yanfei and Li, Yongqiu and Lin, Tuo and Guo, Yi},
  title   = {An operational target trial emulation framework for causal inference using electronic health record data},
  journal = {npj Digital Medicine},
  year    = {2026}
}

---


<div align="center">

**⭐ Star this repo if it saved you from publishing a biased effect estimate! ⭐**

Made with ❤️ by researchers frustrated with post-hoc bias discovery

</div>
