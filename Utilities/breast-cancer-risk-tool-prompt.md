# Claude Code Prompt: Breast Cancer Risk Calculator

> Paste the content below this line into Claude Code.

---

Build a self-contained, single-file HTML breast cancer risk assessment tool for Dr. Carrie Leff, DO, a physician at Monarch Medical. This is a patient-facing tool that runs entirely in the browser — no data is ever sent to a server, saved to a database, or stored in localStorage. Refreshing the page clears all inputs.

## Brand Standards

- **Burgundy** `#5B0048` — headings, section titles, progress bar fill
- **Rose** `#FF467D` — highlight color, call-to-action buttons, risk flags
- **Tan** `#F1EEE9` — page background
- **Light tan** `#F8F6F3` — card/form background
- **Rose light** `#FFD6E3` — callout box backgrounds
- **Fonts:** Use Google Fonts: `Playfair Display` for headings (approximates the brand serif), `Inter` for body text (clean sans-serif). Load from CDN.
- **Header:** Display "Dr. Carrie Leff, DO | Monarch Medical" at top of page in brand colors.

## What to Build

A multi-step, wizard-style form that collects patient inputs for two breast cancer risk models and displays results in plain language.

### Models to Implement

**1. GAIL Model (primary)**
Implement the NCI Breast Cancer Risk Assessment Tool (BCRAT) / Gail Model. The formula and published relative risk tables are in the public domain via the National Cancer Institute. Use the published age-specific baseline hazard rates and relative risk factors for:
- Age at menarche (<12, 12-13, ≥14)
- Age at first live birth and number of first-degree relatives (combined factor per the published table)
- Number of previous breast biopsies
- Atypical hyperplasia on biopsy
- Race/ethnicity (the model has race-specific baseline hazard tables)

Output: 5-year absolute risk and lifetime risk (to age 90).

**2. Tyrer-Cuzick Simplified Estimate (secondary)**
The full TC8 model is proprietary, but implement a reasonable simplified estimate based on published literature that incorporates:
- Age, BMI
- Menopausal status and HRT use
- Family history weighted by degree of relation and bilateral/unilateral
- Breast density (BI-RADS category)
- Known BRCA status

Be transparent in the UI with a note like: *"This is a simplified estimate. Your provider can run a full Tyrer-Cuzick Version 8 calculation for a more precise result."*

Output: Lifetime risk estimate and a risk category (Average / Intermediate / High).

---

## Form Steps

Structure the form as a 5-step wizard with a progress bar at the top.

### Step 1 — About You
- Current age (number input)
- Height (ft/in) and Weight (lbs) — calculate BMI automatically
- Race/ethnicity (dropdown: White non-Hispanic / Black or African American / Hispanic / Asian American / Other — explain this affects baseline risk calculations)

### Step 2 — Menstrual & Reproductive History
- Age at first menstrual period (dropdown: Under 12 / 12–13 / 14 or older)
- Have you ever been pregnant? (Yes/No)
  - If yes: Age at first live birth (number input; or "No live births")
- Menopausal status (Pre-menopausal / Post-menopausal / Not sure)
  - If post: Age at menopause (number input)
- Current or past hormone replacement therapy (HRT) use? (Never / Estrogen-only / Estrogen + Progestin / Not sure)

### Step 3 — Family History
Brief intro text: *"Blood relatives means people genetically related to you — not step-relatives or relatives by marriage."*
- Number of first-degree relatives (mother, sisters, daughters) diagnosed with breast cancer (0 / 1 / 2 or more)
- Any first-degree relatives with ovarian cancer? (Yes / No / Not sure)
- Any second-degree relatives (grandmothers, aunts) with breast or ovarian cancer? (Yes / No / Not sure)
- Any male relatives diagnosed with breast cancer? (Yes / No / Not sure)
- Have you or any close relatives had genetic testing for BRCA1 or BRCA2?
  - Options: No testing done / Tested negative / Tested positive for BRCA1 / Tested positive for BRCA2 / Not sure

### Step 4 — Breast History
- Have you ever had a mammogram? (Yes / No)
  - If yes: What was your breast density result? (dropdown with plain-language labels)
    - Almost entirely fatty (BI-RADS A)
    - Scattered areas of density (BI-RADS B)
    - Heterogeneously dense (BI-RADS C)
    - Extremely dense (BI-RADS D)
    - I don't know / not reported
  - Add a tooltip/info button explaining breast density in plain language
- Have you ever had a breast biopsy? (Yes / No)
  - If yes: How many? (1 / 2 / 3 or more)
  - Did any biopsy show atypical hyperplasia? (Yes / No / Not sure)
    - Add tooltip: *"Atypical hyperplasia means abnormal cells that are not cancer but may increase risk."*

### Step 5 — Your Results
Display results on a clean summary card. Show:

**Risk Summary section:**
- A clear heading: "Your Breast Cancer Risk Estimate"
- Subheading in smaller text: "These are statistical estimates based on the information you provided. They are not a diagnosis."

**Two result cards side by side (or stacked on mobile):**

*Card 1 — Gail Model*
- 5-Year Risk: X.X%
- Lifetime Risk (to age 90): X.X%
- Average woman your age: X.X% (show comparison)
- Risk category badge: Average / Slightly Elevated / Elevated

*Card 2 — Tyrer-Cuzick Estimate*
- Lifetime Risk Estimate: X.X%
- Risk Category: Average / Intermediate / High
- Note in small text: "Simplified estimate. Ask your provider for a full TC8 calculation."

**If lifetime risk ≥ 20% on either model**, display a prominent callout box (rose background, burgundy border) with:
> "Based on this estimate, you may qualify as **high risk** for breast cancer. This means you may benefit from additional screening, such as annual breast MRI in addition to mammography. **Please share these results with your provider.**"

**What This Means section:**
- Brief plain-language explanation of what the numbers mean
- 2–3 bullet points on what patients with elevated risk can do (talk to provider, ask about supplemental screening, consider genetic counseling if strong family history)

**Buttons:**
- "Print These Results" (triggers window.print())
- "Start Over" (reloads the page)

**Footer disclaimer:**
*"This tool is for educational purposes only and does not constitute medical advice. Risk estimates are based on statistical models and may not reflect your individual situation. Always consult your healthcare provider before making decisions about your health."*

---

## Technical Requirements

- **Single .html file** — fully self-contained. Fonts loaded from Google Fonts CDN (only external dependency). No backend, no API calls, no data storage.
- Vanilla JavaScript only — no frameworks.
- Mobile-responsive layout (works well on phones and tablets).
- Accessible: all form inputs have associated `<label>` elements, keyboard navigable, logical tab order.
- Print stylesheet: when printed, hide navigation buttons and show only the results summary cleanly on one page.
- Add a subtle animation when transitioning between steps (CSS fade or slide).
- Validate inputs before allowing progression to the next step; show inline error messages in rose color if required fields are missing.

---

## Important Notes for Implementation

1. The GAIL model formula and baseline hazard tables are publicly available from the NCI at: https://dceg.cancer.gov/tools/risk-assessment/bcra — use the published relative risk coefficients and race-specific baseline hazard data.
2. For the simplified TC8 estimate, weight family history heavily (especially bilateral or young-onset cancer in relatives), apply a multiplier for high breast density (BI-RADS C/D), and adjust for BRCA status.
3. Never display false precision. Round risk percentages to one decimal place (e.g., 8.3%, not 8.347%).
4. The tool should feel warm and reassuring in tone — this is a potentially anxiety-inducing topic. Use calm, clear language throughout.
