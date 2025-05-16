# Log Anomaly Detection Pipeline with PySpark (Jupyter Notebook)

## Name: Lohit Aryan Gopikonda

## UID: 120476501

This Jupyter Notebook demonstrates an end-to-end pipeline for anomaly detection in EC2 log data using PySpark. It includes training/evaluation of three different anomaly detection models: Isolation Forest (via SynapseML), Local Outlier Factor (LOF via scikit-learn).

## Prerequisites

Before running this notebook, ensure you have the following installed and configured:

1.  **Jupyter Environment**:
    - Jupyter Notebook or JupyterLab.
    - A Python 3 kernel (Python 3.7 or higher recommended).
2.  **Apache Spark**: Version 3.x recommended. While the notebook initializes a SparkSession, your system must have a Spark installation that PySpark can connect to or manage. Ensure `SPARK_HOME` is set if you're using a local Spark installation.
3.  **Java**: JDK 8 or 11 (required by Spark).
4.  **Input Log File**:

    - A log file named `ec2.log` is expected in the same directory as the notebook.
    - If your log file has a different name or path, modify the line `raw = spark.read.text("ec2.log")` in the relevant notebook cell.

5.  **Python Libraries**:
    The notebook requires several Python libraries. You can install them using `pip` in your terminal/Anaconda prompt within the environment used by your Jupyter kernel, or by running pip commands directly in a notebook cell (prefixing with `!`).

    ```bash
    pip install pyspark synapseml==1.0.11 matplotlib scikit-learn pandas numpy
    ```

    Or in a notebook cell:

    ```python
    # !pip install pyspark synapseml==1.0.11 matplotlib scikit-learn pandas numpy
    ```

    - `pyspark`: For Spark functionality.
    - `synapseml==1.0.11`: For the Isolation Forest model (Note: `spark.jars.packages` in the notebook's SparkSession setup handles its Spark package dependency).
    - `matplotlib`: For plotting graphs (which will be displayed inline in the notebook).
    - `scikit-learn`: For the LOF model and metrics.
    - `pandas`: For data manipulation, especially for interfacing with scikit-learn.
    - `numpy`: For numerical operations.

## Setup

1.  **clone the Repository**: use git clone.
2.  **Open the Notebook**: Navigate to and open the `.ipynb` file in the Jupyter interface.
3.  **Select Kernel**: Ensure the notebook is using a Python 3 kernel that has access to the installed prerequisites.

    - The notebook automatically attempts to download the `com.microsoft.azure:synapseml_2.12:1.0.11` Spark package when the SparkSession is initialized. Ensure you have an internet connection when running the SparkSession initialization cell for the first time, or that this package is otherwise available in your Spark environment.

## How to Run

1.  **Execute Cells Sequentially**: Run the notebook cells one by one, starting from the top. You can run a cell by selecting it and pressing `Shift + Enter` or using the "Run" button in the Jupyter interface.
2.  **Alternatively, "Run All"**: You can use the "Cell" -> "Run All" option in the Jupyter menu to execute all cells in the notebook.

## Notebook Overview

The notebook is structured into several cells, performing the following main steps:

1.  **SparkSession Initialization**: A cell configures and creates a SparkSession with settings optimized for performance (AQE, Kryo, Arrow) and includes the SynapseML package.
2.  **Log Parsing & Preprocessing**:
    - Reads raw log lines from `ec2.log`.
    - Parses log lines using regular expressions to extract fields like timestamp, process name, PID, and message.
    - Cleans the message content by removing dates, times, and IP addresses.
3.  **Keyword-based Labeling**: Assigns a binary label (`0` for normal, `1` for anomaly) to log messages based on the presence of predefined anomaly-indicating keywords (e.g., "ERROR", "WARN", "timeout"). The first 5 rows of the resulting DataFrame are displayed.
4.  **Exploratory Data Analysis (EDA)**:
    - Generates and displays (inline) a bar chart of log message counts by process name.
    - Generates and displays (inline) a bar chart of the keyword-flag rate per process name.
5.  **ML Feature Engineering Pipeline**:
    - Defines a PySpark ML `Pipeline` consisting of:
      - `RegexTokenizer`, `StopWordsRemover`, `HashingTF`, `IDF`, `PCA`, `StandardScaler`.
6.  **Evaluation Function**: Defines a Python function `evaluate_and_plot` to calculate and print common classification metrics and plot a confusion matrix (inline).
7.  **Model Training and Evaluation**:
    - **Isolation Forest (SynapseML)**:
      - Appends the `IsolationForest` model to the feature engineering pipeline.
      - Trains the model and evaluates its performance, displaying metrics and a confusion matrix.
    - **Local Outlier Factor (LOF) (scikit-learn)**:
      - Preprocesses data, collects features, trains a scikit-learn `LocalOutlierFactor` model, and evaluates it, displaying metrics and a confusion matrix..

## Output

The notebook will produce:

- **Inline Cell Outputs**:
  - Spark logs (set to `WARN` level, visible in the console where Jupyter is running or sometimes in notebook outputs).
  - A sample of the parsed DataFrame (first 5 rows shown as a table).
  - Model training progress messages.
  - Evaluation metrics (TP, FP, FN, TN, Precision, Recall, F1-score) for each of the three models, printed below the relevant cells.
- **Inline Plots**:
  - Bar chart: "Total log message counts by process name".
  - Bar chart: "Proportion of messages with error-keyword flags by service".
  - Confusion Matrix: For Isolation Forest.
  - Confusion Matrix: For Local Outlier Factor.
