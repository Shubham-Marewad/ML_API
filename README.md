# Machine Learning Model API

A Flask-based REST API for training, testing, and making predictions with a Linear Regression model using CSV/Excel datasets.

## Features

- **Train**: Upload a dataset and train a Linear Regression model
- **Test**: Evaluate the trained model on a test dataset
- **Predict**: Make predictions using the trained model with JSON input
- Automatic handling of categorical variables (one-hot encoding)
- Support for CSV and Excel files
- Model persistence using pickle

## Requirements

```
flask
pandas
scikit-learn
openpyxl  # for Excel file support
```

## Installation

1. Install the required packages:
```bash
pip install flask pandas scikit-learn openpyxl
```

2. Run the Flask application:
```bash
python app.py
```

The server will start at `http://127.0.0.1:5000`

## API Endpoints

### 1. Train Model
**Endpoint**: `POST /train`

**Description**: Upload a dataset and train a Linear Regression model.

**Parameters**:
- `file` (form-data): CSV or Excel file containing the dataset
- `target` (form-data): Name of the target column (dependent variable)

**Postman Setup**:
1. Method: `POST`
2. URL: `http://127.0.0.1:5000/train`
3. Body → form-data:
   - Key: `file` | Type: File | Value: Select your CSV/Excel file
   - Key: `target` | Type: Text | Value: `your_target_column_name`

**Example Response**:
```json
{
  "message": "Model trained successfully",
  "mse": 0.234,
  "r2": 0.956
}
```

---

### 2. Test Model
**Endpoint**: `POST /test`

**Description**: Evaluate the trained model on a new test dataset.

**Parameters**:
- `file` (form-data): CSV or Excel file containing the test dataset
- `target` (form-data): Name of the target column

**Postman Setup**:
1. Method: `POST`
2. URL: `http://127.0.0.1:5000/test`
3. Body → form-data:
   - Key: `file` | Type: File | Value: Select your test CSV/Excel file
   - Key: `target` | Type: Text | Value: `your_target_column_name`

**Example Response**:
```json
{
  "message": "Model tested successfully",
  "mse": 0.298,
  "r2": 0.943
}
```

---

### 3. Predict
**Endpoint**: `POST /predict`

**Description**: Make a prediction using the trained model with JSON input.

**Parameters**:
- JSON body containing feature values

**Postman Setup**:
1. Method: `POST`
2. URL: `http://127.0.0.1:5000/predict`
3. Body → raw → JSON:
```json
{
  "age": 35,
  "bmi": 28.5,
  "children": 2,
  "sex": "female",
  "smoker": "yes",
  "region": "southeast"
}
```

**Example Response**:
```json
{
  "prediction": 123.45
}
```

---

## Usage Workflow

### Step 1: Prepare Your Dataset
Ensure your CSV/Excel file has:
- A target column (the value you want to predict)
- Feature columns (input variables)
- Consistent column names across train and test datasets

**Example Dataset** (`insurance.csv`):
```csv
age,bmi,children,sex,smoker,region,charges
35,28.5,2,female,yes,southeast,32548.34
42,31.2,1,male,no,northwest,6940.91
25,26.8,0,female,no,southwest,3756.62
52,34.1,3,male,yes,northeast,45702.02
19,27.9,0,female,yes,southwest,16884.92
```

### Step 2: Train the Model
1. Open Postman
2. Create a POST request to `http://127.0.0.1:5000/train`
3. Add form-data:
   - `file`: Upload `insurance.csv`
   - `target`: Enter `charges`
4. Send the request
5. Check the response for MSE and R² scores

### Step 3: Test the Model (Optional)
1. Create a POST request to `http://127.0.0.1:5000/test`
2. Add form-data:
   - `file`: Upload your test dataset (e.g., `insurance_test.csv`)
   - `target`: Enter `charges`
3. Send the request

### Step 4: Make Predictions
1. Create a POST request to `http://127.0.0.1:5000/predict`
2. Select Body → raw → JSON
3. Enter feature values:
```json
{
  "age": 35,
  "bmi": 28.5,
  "children": 2,
  "sex": "female",
  "smoker": "yes",
  "region": "southeast"
}
```
4. Send the request to get the predicted insurance charges

---

## Important Notes

- **Train First**: You must train the model before testing or making predictions
- **Consistent Features**: Test and prediction inputs must have the same features as the training data
- **Categorical Variables**: The API automatically handles categorical variables using one-hot encoding
- **Model Persistence**: Trained models are saved as `model.pkl` and `encoder_columns.pkl`
- **Supported Formats**: CSV (`.csv`) and Excel (`.xlsx`, `.xls`)

---

## Error Handling

Common errors and solutions:

| Error | Solution |
|-------|----------|
| "Model not trained yet" | Train the model first using `/train` endpoint |
| "Target column not found" | Ensure the target column name matches exactly (case-sensitive) |
| "Unsupported file format" | Upload only CSV or Excel files |
| Missing columns in prediction | Include all features that were present during training |

---

## Example Use Case: Health Insurance Cost Prediction

**Training Data** (`insurance.csv`):
```csv
age,bmi,children,sex,smoker,region,charges
35,28.5,2,female,yes,southeast,32548.34
42,31.2,1,male,no,northwest,6940.91
25,26.8,0,female,no,southwest,3756.62
52,34.1,3,male,yes,northeast,45702.02
```

**Step 1 - Train**:
- Endpoint: `/train`
- File: `insurance.csv`
- Target: `charges`

**Step 2 - Predict**:
- Endpoint: `/predict`
- JSON Body:
```json
{
  "age": 35,
  "bmi": 28.5,
  "children": 2,
  "sex": "female",
  "smoker": "yes",
  "region": "southeast"
}
```
- Response: `{"prediction": 32450.75}`

---

## License

This project is open-source and available for educational purposes.
