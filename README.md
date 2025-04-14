# Task - 05
import pandas as pd  
df = pd.read_csv("titanic.csv")  # Replace with your dataset
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from fpdf import FPDF

# Load Titanic Dataset
df = pd.read_csv("titanic.csv")  # Replace with actual dataset path

# Create a folder for saving plots
import os
if not os.path.exists("eda_plots"):
    os.makedirs("eda_plots")

# Function to Save Plots
def save_plot(fig, filename):
    fig.savefig(f"eda_plots/{filename}", bbox_inches="tight")

# Basic Info & Summary Statistics
summary_info = str(df.info())
summary_stats = df.describe().to_string()

# Missing Values Analysis
missing_values = df.isnull().sum().to_string()

# Pairplot Visualization
sns.pairplot(df)
save_plot(plt.gcf(), "pairplot.png")

# Heatmap of Correlation
plt.figure(figsize=(10,6))
sns.heatmap(df.corr(), annot=True, cmap="coolwarm", linewidths=0.5)
save_plot(plt.gcf(), "heatmap.png")

# Survival Rate Analysis
plt.figure(figsize=(6,4))
sns.barplot(x="Pclass", y="Survived", data=df)
plt.title("Survival Rate by Passenger Class")
save_plot(plt.gcf(), "survival_by_class.png")

# Creating a PDF Report
pdf = FPDF()
pdf.set_auto_page_break(auto=True, margin=15)
pdf.add_page()

pdf.set_font("Arial", "B", 16)
pdf.cell(200, 10, "Exploratory Data Analysis (EDA) - Titanic Dataset", ln=True, align="C")

pdf.set_font("Arial", "B", 12)
pdf.cell(0, 10, "1. Dataset Overview", ln=True)
pdf.set_font("Arial", "", 10)
pdf.multi_cell(0, 5, summary_info)

pdf.set_font("Arial", "B", 12)
pdf.cell(0, 10, "2. Summary Statistics", ln=True)
pdf.set_font("Arial", "", 10)
pdf.multi_cell(0, 5, summary_stats)

pdf.set_font("Arial", "B", 12)
pdf.cell(0, 10, "3. Missing Values Analysis", ln=True)
pdf.set_font("Arial", "", 10)
pdf.multi_cell(0, 5, missing_values)

# Adding Images
pdf.set_font("Arial", "B", 12)
pdf.cell(0, 10, "4. Pairwise Relationships", ln=True)
pdf.image("eda_plots/pairplot.png", x=10, w=180)

pdf.cell(0, 10, "5. Feature Correlation Heatmap", ln=True)
pdf.image("eda_plots/heatmap.png", x=10, w=180)

pdf.cell(0, 10, "6. Survival Rate by Passenger Class", ln=True)
pdf.image("eda_plots/survival_by_class.png", x=10, w=150)

pdf.set_font("Arial", "B", 12)
pdf.cell(0, 10, "7. Key Insights:", ln=True)
pdf.set_font("Arial", "", 10)
pdf.multi_cell(0, 5, "- Female passengers had a significantly higher survival rate.\n"
                     "- First-class passengers had better chances of survival.\n"
                     "- Younger passengers had higher survival probabilities.\n"
                     "- Higher ticket fare correlated with increased survival rates.")
