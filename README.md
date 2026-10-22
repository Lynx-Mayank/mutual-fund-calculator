# SIP vs FD Calculator

A single-page web app that shows what a monthly mutual fund SIP can grow into, and how that compares with a fixed deposit (FD). Amounts are in Indian rupees, formatted with lakh/crore grouping (for example ₹1,00,00,000 and "1.00 crore").

No build step, no dependencies, no backend. It is one HTML file.

## Features

### 1. SIP calculator
- Inputs: monthly SIP amount, expected annual return (%), investment period (years)
- Outputs:
  - Estimated corpus at the end of the period
  - Total invested vs. estimated returns, with a split bar and percentages
  - Money multiple (corpus ÷ amount invested)
  - Year-by-year stacked bar chart (invested vs. returns), with hover readout
  - Year-by-year table

### 2. SIP vs FD comparison
- One shared investment period, separate inputs for each option
- SIP: monthly amount and expected annual return
- FD:
  - Funding mode: **monthly deposit** (like a recurring deposit) or **lump sum**
  - Deposit amount and interest rate (%)
  - Compounding: quarterly (default), monthly, half-yearly or yearly
- Outputs:
  - Verdict line showing which option ends ahead and by how much
  - Side-by-side tiles with final value, amount invested, returns and money multiple
  - Line chart of both values over time, with hover readout
  - Year-by-year table including the SIP-minus-FD gap

### Other
- Every input has both a number field and a slider, kept in sync
- Works on phone and desktop widths
- Light and dark theme follow the viewer's system setting
- Open the page with `#compare` at the end of the URL to land on the comparison tab

## How the numbers are calculated

**SIP.** Each instalment is invested at the start of the month and compounds monthly.

```
i = annual return / 12 / 100
balance = (balance + monthly amount) × (1 + i)      repeated for every month
```

This is the same as the standard formula `FV = P × [((1 + i)^n − 1) / i] × (1 + i)`, where `n` is the number of months. For example, ₹10,000 a month at 12% for 15 years gives about ₹50.46 lakh.

**FD, lump sum.**

```
A = P × (1 + r/q)^(q × t)
```

`P` is the amount, `r` the annual rate, `q` the number of compounding periods per year and `t` the number of years.

**FD, monthly deposit.** Each month's deposit compounds for the time remaining until the end of the period:

```
A = Σ  P × (1 + r/q)^(q × (n − j) / 12)      for j = 0 … n−1
```

where `n` is the number of months and `j` is the month the deposit was made.

The two calculation functions, `sipSeries` and `fdSeries`, sit between `// <calc>` markers at the top of the script, so they are easy to find and test on their own.

## Assumptions and limitations

- The SIP return is one fixed annual rate. Real mutual fund returns are market-linked and vary from year to year. This is an illustration, not a forecast.
- FD interest is shown **before tax**. Tax on FD interest and on mutual fund gains differs, so post-tax results will differ from what the page shows.
- Bank recurring deposits may compute interest slightly differently from the monthly-deposit model here, so treat small differences from a bank's own calculator as expected.
- Expense ratios, exit loads, inflation and step-up SIPs are not modelled.
- This is not financial advice.

## Running it

Save the page as `index.html` and open it in a browser. That is all.

To host it, upload the file to any static host (GitHub Pages, Netlify, Cloudflare Pages, or any web server).

The fonts (Bricolage Grotesque and Instrument Sans) load from Google Fonts. If they are blocked, the page falls back to system fonts and still works.

## Project structure

```
index.html    markup, styles and script, all in one file
README.md     this file
```

Inside `index.html`:

| Section | What it does |
| --- | --- |
| `<style>` | Design tokens at the top (colours, fonts), then component styles |
| `// <calc>` block | Pure functions for the SIP and FD maths |
| `makeField` | Builds a number input and slider pair and keeps them in sync |
| `drawBars`, `drawLines` | Draw the charts as inline SVG, sized to the container |
| `renderSip`, `renderCmp` | Recompute and update each tab when an input changes |

## Customising

- **Change defaults.** Edit the `AMT`, `RATE`, `YRS`, `FD_RD` and `FD_LUMP` objects in the script. Each holds the min, max, step and starting value of an input.
- **Change colours.** Edit the tokens in `:root`, and the dark-theme overrides below them. SIP is blue and FD is orange throughout.
- **Change currency.** Edit the `inr`, `short` and `words` helpers, and the `'en-IN'` locale in `inr`.

## Ideas for later

- Annual step-up on the SIP amount
- Lump-sum mutual fund investment alongside a SIP
- Post-tax comparison
- Inflation-adjusted values
- Shareable links that carry the input values

## License

MIT
