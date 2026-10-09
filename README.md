# Smart Expense Intelligence

Smart Expense Intelligence is a machine-learning-based personal expense management web application. It helps users record and analyze expenses, automatically classify transactions, estimate next-month spending, and identify transactions that appear unusual compared with normal spending behavior.

---

## Features
- **User Authentication & Account Management**: Secure user registration, login, and session handling.
- **Transaction Management**: Easily add, edit, and remove expense transactions.
- **Interactive Dashboard**: Real-time spending overviews, metrics, and visual insights.
- **Expense History**: Filterable recent transactions with category breakdowns and summaries.
- **Automatic Expense Classification**: Automatically predicts the expense category from the description.
- **Next-Month Expense Prediction**: Forecasts upcoming monthly spending using regression modeling.
- **Unusual / Anomaly Detection**: Highlights transactions that deviate from the user's normal spending habits.
- **Flexible Database Storage**: Backed by SQLite locally or PostgreSQL/Supabase in production.
- **Web-Based Analytics**: Responsive, intuitive interface for seamless financial tracking.

---

## Machine Learning Components

### 1. Expense Category Classification
Expense descriptions are converted into numerical features using **TF-IDF** (Term Frequency–Inverse Document Frequency) and classified using **Logistic Regression**.

The system predicts nine expense categories:
- Bills
- Electronics
- Entertainment
- Food
- Grocery
- Healthcare
- Shopping
- Transport
- Travel

**Pipeline Details:**
- Uses **233 TF-IDF features**
- Stratified **80:20 train-test split**
- **Evaluation Result:** **99.51%** for Accuracy, Precision, Recall, and F1-score.

---

### 2. Next-Month Expense Prediction
**Random Forest Regression** is used to estimate next-month spending.

**Model Input Features:**
- Current spending
- Previous-month spending
- Transaction count
- Previous transaction count
- Month

**Hyperparameter Configuration:**
- Number of estimators: **200 trees**
- Maximum depth: **10**
- Minimum leaf size: **5**

#### Reported Evaluation Results:
| Metric | Result |
| :--- | :--- |
| **MAE (Mean Absolute Error)** | ₹3,780.28 |
| **RMSE (Root Mean Squared Error)** | ₹7,467.55 |
| **R² Score** | 0.9408 |

---

### 3. Unusual Expense Detection
**Isolation Forest** is used for detecting unusual transactions. It analyzes transaction amounts and their transformed representations to identify transactions that differ from normal spending patterns.

**Feature Transformation:**
```text
Amount_Log = log(1 + Amount)
```

The application considers individual historical spending patterns rather than relying on a single static monetary threshold. An unusual transaction is flagged as a behavioral alert for review, not proof of fraud.

---

## Technology Stack

| Component | Technology |
| :--- | :--- |
| **Programming Language** | Python |
| **Web Framework** | Flask |
| **Frontend** | HTML5, CSS3, JavaScript |
| **Machine Learning** | Scikit-learn |
| **Data Processing** | Pandas, NumPy |
| **Visualization** | Matplotlib, Seaborn |
| **Model Storage** | Joblib |
| **Database** | PostgreSQL / SQLite |
| **Cloud Database** | Supabase |
| **Deployment** | Vercel |
| **WSGI Server** | Gunicorn |

---

## Project Workflow

```text
User
  ↓
Web Interface (HTML/CSS/JS)
  ↓
Flask Backend (app.py)
  ↓
Transaction Data Management
  ↓
Database (SQLite / PostgreSQL) / Data Processing (Pandas)
  ↓
Machine Learning Pipeline
  ├── TF-IDF + Logistic Regression (Classification)
  ├── Random Forest Regression (Next-Month Forecast)
  └── Isolation Forest (Anomaly Detection)
  ↓
Predictions & Insights Generation
  ↓
Dashboard & Visual Analytics
```

---

## Dataset
The machine learning models were developed using the **Personal Finance Dataset** from Kaggle:
- **Dataset Link**: [Personal Finance Dataset on Kaggle](https://www.kaggle.com/datasets/saraswathyyy/personal-finance-dataset)
- **Key Attributes**: Date, Expense Description, Amount, Category, Payment Method.

The Kaggle dataset serves as the benchmark training data. Active application transactions are stored and managed through the application's configured database.

---

## Project Structure

```text
Smart_Expense_Intelligence/
│
├── app.py                      # Flask application entry point and routes
├── requirements.txt            # Python dependencies
├── README.md                   # Project documentation
├── migrate_database.py         # Database migration script
├── .gitignore                  # Git ignore rules
│
├── dataset/                    # Training and benchmark datasets
│   └── ...
├── models/                     # Saved ML models (.joblib / .pkl)
│   ├── tfidf_vectorizer.joblib
│   ├── category_model.joblib
│   ├── next_month_rf_model.joblib
│   └── anomaly_model.joblib
├── notebooks/                  # Jupyter notebooks for model training & EDA
│   └── expense_intelligence.ipynb
│
├── static/                     # CSS stylesheets, JavaScript files, images
│   ├── css/
│   └── js/
├── templates/                  # HTML templates (Jinja2)
│   ├── login.html
│   ├── register.html
│   ├── dashboard.html
│   └── ...
└── expense_intelligence.db     # Local SQLite database
```

---

## Installation and Local Setup

### 1. Clone the repository
```bash
git clone <YOUR-GITHUB-REPOSITORY-URL>
cd Smart_Expense_Intelligence
```

### 2. Create a virtual environment
**Windows (PowerShell):**
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```
*If PowerShell blocks script execution, run:*
```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\venv\Scripts\Activate.ps1
```

**macOS / Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Configure Database & Environment Variables
If configuring PostgreSQL / Supabase, set your database URL and secret key in your environment or a `.env` file:
```env
SECRET_KEY=your_secret_key_here
DATABASE_URL=your_postgresql_database_url
```
> **Security Reminder:** Never commit passwords, API keys, or production database credentials to a public GitHub repository.

### 5. Run the application
```bash
python app.py
```
Open your browser and navigate to:
```text
http://127.0.0.1:5000
```

---

## Model Evaluation

### Classification
- **Accuracy, Precision, Recall, F1-score**: Evaluated via stratified 80:20 train-test split.
- **Confusion Matrix**: Used to evaluate class-level precision and misclassification trends across all 9 categories.

### Regression
- **Mean Absolute Error (MAE)**: Measures average magnitude of prediction errors.
- **Root Mean Squared Error (RMSE)**: Penalizes larger forecasting errors.
- **R² Score**: Evaluates proportion of spending variance captured by the model.
- **Feature Importance & Baseline Comparison**: Validated against simple current-month baseline heuristics.

### Anomaly Detection
- Evaluated through behavioral and score-distribution analysis since ground-truth anomaly labels are naturally unverified in raw personal spending logs.

---

## Mathematical Formulations

- **TF-IDF Formulation:**
  $$\text{TF-IDF}(t,d) = \text{TF}(t,d) \times \text{IDF}(t)$$
  $$\text{IDF}(t) = \log\left(\frac{N}{\text{DF}(t)}\right)$$

- **Random Forest Prediction:**
  $$\hat{y} = \frac{1}{T} \sum_{t=1}^{T} h_t(x)$$
  *(where $T$ is the total number of trees and $h_t(x)$ is the output of individual tree $t$)*

- **Monthly Spending Aggregate:**
  $$\text{Monthly Spending} = \sum \text{Amount}_i$$

- **Mean Absolute Error (MAE):**
  $$\text{MAE} = \frac{1}{n} \sum_{i=1}^n |y_i - \hat{y}_i|$$

- **Root Mean Squared Error (RMSE):**
  $$\text{RMSE} = \sqrt{\frac{1}{n} \sum_{i=1}^n (y_i - \hat{y}_i)^2}$$

- **Coefficient of Determination ($R^2$):**
  $$R^2 = 1 - \frac{\sum_{i=1}^n (y_i - \hat{y}_i)^2}{\sum_{i=1}^n (y_i - \bar{y})^2}$$

- **Log-Scale Transformation for Isolation Forest:**
  $$\text{Amount\_Log} = \log(1 + \text{Amount})$$

---

## Important Notes
- Predicted spending is an estimation tool for planning, not a guaranteed financial outcome.
- An unusual transaction flag is an advisory behavioral alert, not an accusation of fraud.
- Anomaly detection accuracy improves dynamically as more personal transaction history is logged.
- Model performance metrics reflect evaluation on the Kaggle Personal Finance benchmark dataset.
- Keep database credentials and secrets secure outside version control.

---

## Future Scope
- **Personalized Budgeting Insights**: Dynamic smart-saving advice based on recurring habits.
- **Time-Series Forecasting**: Integration of Prophet / ARIMA models for granular multi-month trends.
- **Granular Categories**: Subcategory support and customizable user tags.
- **Automated Financial Reports**: Downloadable monthly PDF/Excel summary reports.
- **Mobile Application Support**: Responsive PWA or native mobile app companion.
- **Continuous / Online Retraining**: Automated pipeline to fine-tune models on user feedback.
- **Interactive Financial Visualizations**: Advanced charts using Plotly or Chart.js.

---

## License
This project was developed as an academic machine-learning project for personal expense intelligence and financial analytics.
