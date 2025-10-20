# PMC-Literature-Downloader
A unified Python toolkit for large-scale literature retrieval — automating the download of PDFs and supplementary files from PMC, Unpaywall, Sci-Hub, and ScienceDirect. Includes intelligent filtering, metadata tracking, and institutional-access support.


# PMC Literature Downloader

A collection of automated Python scripts for downloading academic articles and supplementary materials from **PubMed Central (PMC)**, **Unpaywall**, **Sci-Hub**, and **ScienceDirect**.  
This toolkit is designed for large-scale literature collection and processing workflows, such as reference validation, supplementary file retrieval, and data curation.

---

## Included Scripts

### 1. `download_from_pmc.py`
Simulates searching for articles on **PubMed Central (PMC)**.  
For each PMCID, it checks whether a **PDF** download button and **Supplementary** links exist, then downloads all available files.

**Outputs**
- `missing_row_pmcid_results_temp.csv` — temporary log file updated every 10 records  
- `missing_row_pmcid_results.csv` — final summary of all downloads  
- `failed.txt` — failed supplementary downloads

**Inputs**
- `csv_path` → input CSV with a PMCID column  
- `out_dir` → directory where all files are saved  

Each article is saved as `{PMID}.pdf` and `{PMID}_supp_{i}`.

---

### 2. `download_from_unpaywall.py`
Retrieves PDFs using the **Unpaywall** service via DOI.  
It builds Unpaywall API URLs and downloads accessible open-access versions.

**Inputs**
- `csv_path` → CSV containing a `DOI` column  
- `out_dir` → folder to store downloaded PDFs  
- `email` → valid academic email (must end with `.edu`)

**Notes**
- Some PDFs may be unreadable or incomplete.  
- Check and remove leftover `.part` files if necessary.

---

### 3. `download_from_scihub.py`
Downloads PDFs using **Sci-Hub**, primarily targeting older (pre-2021) articles.  
It mimics the method used by Zotero to retrieve full texts via Selenium.

**Part 1:**  
Uses Selenium to access Sci-Hub URLs and automatically download PDFs.  
- **Input:** `src_dir`  
- **Output:** `dst_dir`

**Part 2:**  
Filters and categorizes papers:
1. Detects pre/post-print versions or watermarked PDFs for manual review.  
2. Separates ScienceDirect papers for supplementary downloads.  
3. Marks papers without any “Supplementary” or “Supporting Information” as completed.

---

### 4. `download_sciencedirect_supplementaries.py`
Uses the **ScienceDirect API** to retrieve supplementary materials via PII.  
It builds compact download URLs of the form:
https://ars-els-cdn-com.ezproxy.{your library.edu}/content/image/1-s2.0-{pii}-mmc{i}.{ext}


**Process**
- Given a cleaned PII, iterate over `mmc` indices (1, 2, 3, …)  
- Try multiple extensions (`pdf`, `docx`, `xlsx`, `png`, etc.)  
- Stop when no valid file is found (indicating all supplements are downloaded)

**Inputs**
- `pii` → cleaned Elsevier PII (e.g., `S0167739X24012345`)  
- `out_dir` → save location  
- `session` → optional persistent `requests.Session` for authentication  

**Note:**  
On the first run, you will have about **1 minute** to complete institutional login through **EZproxy** for access authorization.

---

## Environment Requirements

- Python ≥ 3.9  
- Dependencies:
  ```bash
  pip install requests pandas selenium beautifulsoup4 tqdm
