# 📊 Personal Finance Management Dashboard

This project is a Power BI solution designed to help individuals monitor, analyze, and optimize their personal financial habits. It: 
- Aggregates and visualizes transaction data from multiple bank sources,
- Categorizing spending and income trends, and
- Breaks down between the "Wants", "Needs" & "Investment" expenses.

---

## 🔍 Overview

- Combining data from multiple bank accounts and credit cards (i.e. Affin, HSBC, Maybank)  
- Categorizing expenses and income streams  
- Visualizing spending trends over time  
- Identifying top spending categories  
- Highlighting monthly financial performance  

---

## 📁 Project Structure
```
Personal Finance Management/
│
├── DataSource/ # Source Excel files for transactions and categories
│ ├── Affin Credit Card.xlsx
│ ├── Affin Debit Card.xlsx
│ ├── HSBC Credit Card.xlsx
│ ├── HSBC Debit Card.xlsx
│ ├── Maybank Debit Card.xlsx
│ └── Transaction Category
│
├── PBIP_Files/ # Power BI Project files
│ ├── *.pbip # PBIP project definition
│ ├── *.pbism # Semantic model definitions
│ ├── *.pbir # Report layout and visuals
│ └── DAXQueries/ # DAX queries used in the report
│
├── README.md
└── LICENSE
```
---

## 📌 Key Features

- **Multi-source data integration** (Affin, HSBC, Maybank)  
- **Categorized transactions** using a custom mapping file  
- **KPI indicators**: Total Spend, Income, Net Balance  
- **Time series analysis** for tracking financial trends  
- **Custom themes** for consistent visual aesthetics  
- **Reusable DAX queries** for calculated measures  

---

## 🛠️ Requirements

- **Power BI Desktop** (June 2023 or later)  
- Enable support for **Power BI Project (.pbip)** preview feature in Power BI settings  

---

## 🚀 How to Use

1. Clone or download this repository.  
2. Open Power BI Desktop.  
3. Navigate to `File > Open` and select the `.pbip` project file.  
4. Load the `DataSource` files when prompted, or update the file paths in Power BI.  

---

## 📸 Screenshots

- Main dashboard and breakdown pages:

![Dashboard Screenshot](https://github.com/user-attachments/assets/47e0774c-0813-4e83-a4ad-1ce1a18fdfff)

- Data model showing relationships:

![Data Model Screenshot](https://github.com/user-attachments/assets/09088174-28d2-4acb-a593-6caaf86ea861)

- Combined transaction listing table:

![Transaction Listing Screenshot](https://github.com/user-attachments/assets/8d9075f4-5cb9-4b51-a9b4-6da983b74cde)

---

## 📄 License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.


