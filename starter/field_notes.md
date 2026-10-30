# Data Literacy Field Notes & Terminal Log

**The scenario:** A climate-and-energy nonprofit wants to use this
country-level energy and emissions dataset to help decide where to direct
community-protection and climate-support efforts — but the file just
landed with zero documentation. Before anyone can trust a decision built
on top of it, you need to figure out what's actually in it — what each
column really holds, and anything that looks off. That's what this whole
file walks through: the real, everyday first pass any analyst runs on an
unfamiliar file, and the real answers you need before this data is safe
to act on.

Dataset: `../data/co2_emissions_2022.csv` (see `../data/SOURCE.md`). Each
section below describes what you're trying to find out — **figuring out
which command (and which flags) actually gets you there is part of what
this project tests**, so the exact command isn't given. Where a command
belongs, you'll see a `# TODO:` comment describing the goal instead of a
ready-to-run command; replace the comment with the real command you used.
Paste the **real output** you got underneath, then write your answer based
on what you actually saw, not what you'd expect. Work through this in
order; open the raw file before anything else.

Run all commands from the repo root — the top-level folder you land in
after cloning *your own* copy of this repo (`git clone ...` then `cd
your-repo-name`, see `README.md` Step 3; not this template) — or `cd` into
`data/` first and drop the `data/` prefix — your choice, just be
consistent.

**This one file is your whole record** — the commands you ran, their real
output, and your written answers, all together in the order you actually
did the work. There's no separate log file to keep in sync with this one;
whenever you write a command down anywhere in this file, use a fenced code
block tagged `bash`, like this:

````
```bash
git status
```
````

Three backticks, the word `bash`, your command, three backticks to close —
it renders with syntax highlighting and makes the command copy-pasteable.

## Setup log

Before jumping into the dataset, log the commands you actually ran to set
this repo up (see `README.md`) — `git init`/clone, creating your
`.gitignore`, your first commit. Add to this as you go, don't
reconstruct it from memory at the end.

```bash
cd ~/Documents/data_literacy_field_notes
pwd
ls
ls starter
```

## 1. First look at the raw file

Before trusting anything about this file's structure, you want a quick,
safe peek at it — not the whole 254-row file, just the **first 5 lines**,
enough to see the shape of it without scrolling past everything.

```bash
head -n 5 data/co2_emissions_2022.csv
```

**Output:**

country,iso_code,year,population,gdp,co2
Afghanistan,AFG,2022,40578847,53303471617,10.170
Africa,,2022,1445862695,,1459.114
Africa (GCP),,2022,,,1459.103
Albania,ALB,2022,2827614,36171005594,4.498

**What I notice:** — your raw observations, in your own words_

I noticed the dataset has six columns. Some rows have missing information, especially in the ISO code and GDP columns. It also includes regions like Africa alongside individual countries.

## 2. Column names

Next you want to see just the column names by themselves — one per line is
a lot easier to scan than one long comma-separated row.

```bash
head -n 1 data/co2_emissions_2022.csv | tr ',' '\n'
```

**Output:**
country
iso_code
year
population
gdp
co2

## 3. Column-by-column data type table

For each column, you want to preview real values — **the first 6 values**
of just that one column — without scrolling through every field on every
row. Column numbers for reference: `country`=1, `iso_code`=2, `year`=3,
`population`=4, `gdp`=5, `co2`=6.

```bash
cut -d ',' -f 1 data/co2_emissions_2022.csv | head -n 7
cut -d ',' -f 2 data/co2_emissions_2022.csv | head -n 7
cut -d ',' -f 3 data/co2_emissions_2022.csv | head -n 7
cut -d ',' -f 4 data/co2_emissions_2022.csv | head -n 7
cut -d ',' -f 5 data/co2_emissions_2022.csv | head -n 7
cut -d ',' -f 6 data/co2_emissions_2022.csv | head -n 7
```

Fill in the table from what you actually saw — not a guess from the column
name alone:

| Column | Inferred type | How I can tell |
| Country | String(text) |Contains country or region name|
| iso_code | String(text) | Contains country codes like AFG, with some missing values |
| Year | Integer (whole number) | Contains years like 2022 without decimals |
| Population | Integer (whole number) | Contains whole-number population counts like 40578847, with some missing values |
| GDP | Integer (whole number) | Contains whole-number GDP values, with some missing data|
| Co2 | Float (decimal number) | Contains decimal calues like 10.170 and 1459.114 |

**In your own words:** what's the actual difference between a list/array
and a table? (Not a definition you looked up — explain it the way you'd
explain it to a friend who's never coded. No command needed for this one,
just think about the table you just filled in above.)

A list or array stores values in a sequence while a table organizes data into rows and columns. For example a list could contain country names but a table can show each country's name, population, GDP, and CO2 emissions together.
 
## 4. Structured or unstructured?

State whether this dataset is structured or unstructured, and why.

This dataset is structured because the information is organized into rows and columns. Each row represents a country or region and each column contains a specific type of information such as population, GDP, or CO2 emissions. Even though some values are missing, the data still follows a consistent format.

## 5. Before you trust this data

Your first look (see section 1) turned up a row with a suspiciously
**blank** `iso_code`. Before you build anything on this data, you want to
know exactly how many of the 254 rows share that blank — is it a one-off,
or a real pattern worth flagging before anyone draws conclusions from it?
You may need more than one command for this — piping (`|`) feeds one
command's output into the next as input.

```bash
cut -d ',' -f 2 data/co2_emissions_2022.csv | grep -c '^$'
```

**Output:**
36

```bash
cut -d ',' -f 5 data/co2_emissions_2022.csv | grep -c '^$'
```

**Output:**
89

List **at least 2** specific things you'd want to know about how this
dataset was collected, before trusting any conclusion drawn from it — ground
at least one in the counts you just found.

**My 2 concerns:**
1. There are 89 rows with missing GDP values. I would want to know why those values are missing and whether they were unavailable or left out during data collection.
2. There are 36 rows with missing ISO codes. I would want to know if these are mostly regional entries rather than individual countries, since that could affect comparisons.

## 6. Data-quality question for the source

Take the **most specific and checkable** of the two concerns above, and
phrase it as a real question you'd send to the data's source/maintainer. A
good test: could they actually answer it with a concrete yes/no or fact —
not "is this data good?"

Why are GDP values missing for 89 rows in the 2022 dataset? Were these values unavailable from the original source or were they excluded during data collection?

## 7. Something you're actually curious about

Different question from #6 — that one was about checking the data's
trustworthiness. This one's about you: what's something you're genuinely
curious to explore using this data? Pick two specific countries you're
curious to compare, and look up their full rows.

```bash
grep -i 'Trinidad' data/co2_emissions_2022.csv                          
grep '^United States,' data/co2_emissions_2022.csv
```

**Output:**
Trinidad and Tobago,TTO,2022,1495923,37513383973,37.493
United States,USA,2022,341534041,19493170182843,5055.403

**My question:** How do CO2 emissions compare between Trinidad and Tobago and the United States in 2022 when accounting for their different population sizes?

## 8. AI-literacy reflection

Name **2 concrete risks** of trusting an AI assistant's output about this
dataset (or any data question) without checking it yourself. Then state
**one specific personal rule** for when you'll double-check an AI-provided
fact before relying on it professionally — specific enough that you could
actually follow it, not "I'll be careful."

**Risks:**
1. AI could give me incorrect information about the dataset, such as saying there are fewer missing GDP values than there actually are.


2. AI could make assumptions about missing data without evidence, which could lead me to draw the wrong conclusions.

**My rule:** Before using any information or conclusions AI gives me, I'll double-check them against the original dataset to make sure they're accurate.

## 9. Something that went wrong (and how you figured it out)

Somewhere in this project you're going to run a command that either throws
an error or gives you output that isn't what you expected — everyone hits
this at least once. Paste the actual command and what actually happened
(not a paraphrase), then explain what that told you and how it led to the
fix — not "I googled it and it worked."

```bash
cut -d ',' -f 5 data/co2_emissions_2022.csv | grep -c '^$'
```
89

**What happened:**
While inspecting the GDP column, I noticed that some rows were blank instead of having numerical values.
**What it told me and how I fixed it:** The dataset contains missing GDP values. I don't know exactly why they're missing, but some information may not have been available from the original source. I used the terminal to count the blank GDP values and found that 89 rows were missing GDP data. 
