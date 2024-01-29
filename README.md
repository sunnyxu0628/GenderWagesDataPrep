# GenderWages Data Preparation

## Overview
This section is dedicated to the preparation of data for the UC Gender Wages project. The process includes a series of .do files and one Jupyter Notebook, each meticulously crafted to scrape, clean, merge, and analyze the data. The final result is a comprehensive dataset ready for thorough analysis.

---

## File Structure
```
01_dataprep/
│
├── data/
│   ├── _GenderWages/raw           # Directory containing raw data files
│   └── _GenderWages/raw/CleanDataSets"         # Directory where processed data will be stored
│
├── code/
│   ├── Scopus Webscraper2024.ipynb   # Jupyter Notebook for web scraping
│   ├── 0_setup.do    # Install Reclink dependency
│   ├── 1_MergeClean.do    # Merging salary and department information
│   ├── 2_Business_Merging.do  # Merging business school citation information 
│   ├── 3_SociScience_Merging.do  # Merging social science department citation information
│   └── 4_Gender_Merging.do   # Merging estimated gender information
│   └── main.do   # Run 4 do files in sequence at once
│
└── README.md
```

---

## Prerequisites
- Stata (version 17)
–	the program “0_setup.do” will install the package needed locally and should be run once.
- Python (version 3.12)
–	pandas 2.1.1
–	selenium 4.15.2
–	bs4 0.0.1
–	openpyxl 3.1.2
–	joblib 1.3.2
–	lxml
–	"The “Scopus Webscraper2024.ipynb” notebook lists the necessary dependencies and includes instructions for installing these packages as the initial step. These installation commands are commented out and should be executed only once."
–	As the notebook uses Firefox browser to web-scrape data, please ensure Firefox is downloaded.

## Data Preparation Steps

### 1. Web Scraping (`Scopus Webscraper2024.ipynb`)
This notebook performs web scraping of UC business school professors' citation information and social science department professors' citation information from Scopus Review (https://www.scopus.com/home.uri?zone=header&origin=AuthorProfile). The input file is manually collected from the UC official website by gathering the names of the professors and then finding the corresponding Scopus link. For business, the information is stored in `_GenderWages/raw/BusinessSchoolData/merged_complete.xlsx`, and for social science, in `_GenderWages/raw/SocialScience/SocSci_Data_Collection.xlsx`. It collects citation and paper counts for each available year, total paper counts, total citation counts, and exports it to a JSON file, finally combining all observations into one CSV file. The logic of web scraping involves locating the HTML attribute containing the desired information, in this case, `aria_label`. The citation information will be integrated into the main dataset in `2_Business_Merging.do` for business citation information and `3_SociScience_Merging.do` for social science information. 
- 421 out of 517 business professors' citation information was successfully collected
- 1870 out of 1928 social science professors' citation information was collected successfully.
- The data for professors not collected was due to either the absence of a Scopus profile or a lack of citation information on the profile.

### 2. Data Merging (`1_MergeClean.do`)
This file filters the data to include only professors. It then merges detailed salary data and department information for each professor, creating separate files for each year from 2014 to 2020. Subsequently, it merges these annual files into a single file. The merging process uses the number of years a professor has been on campus as a checkpoint. Therefore, the final dataset does not account for professors who have moved across different locations on campus; it only considers their initial work location. Additionally, the file generates new indicators such as total earnings change over time, a 10% earnings increase, and total earnings increase not attributable to job title changes, among others.

Challenges: 
1. Name Variation across years and datasets: This is a significant difficulty when merging salary data and department information, leading to a large number of unmatched cases. To solve this, we used fuzzy matching(reclink) and manually checked to ensure it matched the same individuals. 

Potential issue using MacBook
1. When running this do file on MacBook for the first time, there could be an error of "Variables id_2019_dist firstname lastname location reporting year department reporting year do not uniquely identify observations in the master data". However, re-running the do file one more time usually resolves the issue. The reason for this error requires further investigation.

### 3. Business Merging (`2_Business_Merging.do`)
This file merges Business School Professors' citation information into the main dataset. 
437 out of 517 business professor information was integrated into the main dataset.

### 4. Social Science Merging (`3_SociScience_Merging.do`)
This file merges Social Science Department Professors' citation information into the main dataset. 
1138 out of 1928 social science professor information was integrated into the main dataset.

### 5. Estimated Gender (`4_Gender_Merging.do`)
This file integrates the Estimated Gender Information and the Probability of Gender Estimation data into the main dataset. The estimation is based on first names, so it merges based on the first name. 
14650 unique first names with their gender estimation were included, and 24447 out of 24568 professors' first names were found in the gender file.

---

## Running the Scripts
When running the scripts for the first time, ensure to run `0_setup.do`, then execute the `Scopus Webscraper2024.ipynb` on Jupyter Notebook. Finally, running the `main.do` file will execute the 4 `.do` files in sequence.
