# UK Insolvency SQL Agent

## Objective

UK insolvency trends provide a useful signal of household and corporate financial stress. This repository explores how a SQL agent can convert plain-English questions into grounded database queries, execute them against UK Insolvency Service data and return cited results for risk analysis.

The agent is designed to distinguish individual and corporate distress, compare compulsory liquidations with forced bankruptcies, calculate yearly trader-bankruptcy trends and estimate insolvency ratios. Its responses are checked against Pandas calculations and direct SQLite queries to reduce hallucinations.

## Architecture

```text
UK-Insolvency-SQL-Agent/
├── data/
│   └── .gitkeep                        # Local CSV and SQLite data inputs
├── docs/
│   └── Macro Risk AI Agent - Deployment Guide.pdf
└── notebooks/
    └── Macro_Risk_Analyst.ipynb       # SQL-agent analysis and verification
```

## Data

The analysis uses the following UK Insolvency Service datasets:

- Company insolvencies
- Individual insolvencies
- The cleaned inputs are loaded into SQLite tables named `corporate_risk` and `retail_risk`

The source CSV and SQLite database are intentionally excluded from version control. Place local copies in `data/` before running the notebook.

## Quickstart

Install the Python dependencies used by the notebook:

```bash
pip install pandas langchain-groq langchain-community jupyter
```

Set `GROQ_API_KEY` in the notebook environment or enter it when prompted, then launch the analysis:

```bash
jupyter notebook notebooks/Macro_Risk_Analyst.ipynb
```

## SQL Agent Output

The notebook's recorded run demonstrates that the agent executes grounded SQL for each natural-language question:

```text
--- MACRO RISK ANALYST ---

Analyst: Calculate the yearly total Trader Bankruptcies for 2020, 2021, 2022, and 2023.
Agent: The yearly total Trader Bankruptcies for 2020, 2021, 2022, and 2023 are 2234, 1402, 1227, and 1493 respectively.

Analyst: Compare the yearly total Company Compulsory Liquidations vs Individual Forced Bankruptcies for the years 2015 through 2023.
Agent: 2015: Corp 2853 vs Ind 4404 -> Ind is higher
2016: Corp 2885 vs Ind 3901 -> Ind is higher
2017: Corp 2747 vs Ind 3305 -> Ind is higher
2018: Corp 3090 vs Ind 3661 -> Ind is higher
2019: Corp 2941 vs Ind 3128 -> Ind is higher
2020: Corp 1354 vs Ind 1347 -> Corp is higher
2021: Corp 492 vs Ind 1113 -> Ind is higher
2022: Corp 1968 vs Ind 1143 -> Corp is higher
2023: Corp 2838 vs Ind 1697 -> Corp is higher

Analyst: For the year 2023, what is the ratio of Total Individual Insolvencies to Total Corporate Insolvencies?
Agent: The ratio of Total Individual Insolvencies to Total Corporate Insolvencies for the year 2023 is 103434:25162, which simplifies to approximately 4.11:1.
```

## Ground Truth Verification

The recorded notebook verification independently checks the agent against Pandas and direct SQL:

```text
Q1 Correct Answers (Pandas):
2020    2234
2021    1402
2022    1227
2023    1493

Q1 SQL Check (Direct DB Query):
[(2020, 2234), (2021, 1402), (2022, 1227), (2023, 1493)]

Q2 Correct Answers:
      Compulsory Liq  Forced Bankruptcies
2015            2853                 4404
2016            2885                 3901
2017            2747                 3305
2018            3090                 3661
2019            2941                 3128
2020            1354                 1347
2021             492                 1113
2022            1968                 1143
2023            2838                 1697

Q3 Correct Answer: Ind: 103434, Corp: 25162, Ratio: 4.11
```
