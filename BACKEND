# APP.PY

```
from flask import Flask, jsonify, request
from flask_cors import CORS
from pymongo import MongoClient
from sklearn.ensemble import RandomForestClassifier
import numpy as np
import pickle

app = Flask(__name__)
CORS(app)

# MongoDB connection
client = MongoClient('mongodb://localhost:27017/')
db = client['commercial_court_db']
case_collection = db['cases']

# Load pre-trained models
with open('case_outcome_model.pkl', 'rb') as f:
    case_outcome_model = pickle.load(f)

with open('fake_doc_model.pkl', 'rb') as f:
    fake_doc_model = pickle.load(f)

# Load document drafting model (if applicable)
with open('AI documentation.pkl', 'rb') as f:
    document_drafter_model = pickle.load(f)

@app.route('/predict_outcome', methods=['POST'])
def predict_outcome():
    data = request.json
    try:
        evidence_data = np.array(data['evidence']).reshape(1, -1)
        prediction = case_outcome_model.predict(evidence_data)[0]
        return jsonify({'outcome': prediction})
    except Exception as e:
        return jsonify({'error': str(e)}), 400

@app.route('/detect_fake_document', methods=['POST'])
def detect_fake_document():
    try:
        document_data = np.array(request.json['document']).reshape(1, -1)
        prediction = fake_doc_model.predict(document_data)[0]
        return jsonify({'is_fake': prediction})
    except Exception as e:
        return jsonify({'error': str(e)}), 400

@app.route('/add_case', methods=['POST'])
def add_case():
    case_data = request.json
    try:
        case_collection.insert_one(case_data)
        return jsonify({'message': 'Case added successfully'}), 201
    except Exception as e:
        return jsonify({'error': str(e)}), 500

# New route for document drafting
@app.route('/draft_document', methods=['POST'])
def draft_document():
    try:
        # Get user input and evidence from the request
        data = request.get_json()

        # Access each column individually and handle missing fields
        case_id = data.get('Case ID', '')
        defendant_name = data.get('Defendant Name', '')
        plaintiff_name = data.get('Plaintiff Name', '')
        charges = data.get('Charges', '')
        judge_name = data.get('Judge Name', '')
        prosecution_evidence = data.get('Prosecution Evidence', '')
        defensive_evidence = data.get('Defensive Evidence', '')
        judge_decision = data.get("Judge's Decision", '')
        sentence = data.get('Sentence', '')
        final_judgment = data.get('Final Judgment', '')

        # Combine the inputs into a feature array
        features = np.array([
            case_id,
            defendant_name,
            plaintiff_name,
            charges,
            judge_name,
            prosecution_evidence,
            defensive_evidence,
            judge_decision,
            sentence,
            final_judgment
        ])

        # Ensure all features are properly processed
        # (For example, categorical features may need label encoding before prediction)

        # Check if the features need to be preprocessed for the model
        # Example: If the model was trained with numerical values, you may need to convert these inputs.

        # Predict the document using the AI model
        drafted_document = document_drafter_model.predict(features.reshape(1, -1))

        # Format the response with the generated document
        document_response = {
            "drafted_document": drafted_document[0]  # The generated document
        }

        return jsonify(document_response), 200

    except KeyError as e:
        return jsonify({"error": f"Missing key: {str(e)}"}), 400
    except ValueError as e:
        return jsonify({"error": f"Value error: {str(e)}"}), 400
    except Exception as e:
        return jsonify({"error": str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True, use_reloader=False)
```

# train_modelai.py
```
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.preprocessing import LabelEncoder
import pickle

# Load the CSV data
data = pd.read_csv('C:/Users/shrik/Desktop/aic/backend/AI documentation.csv')

# Adjust these names according to your actual CSV structure
X = data[['Case ID','Defendant Name','Plaintiff Name','Charges','Judge Name','Prosecution Evidence','Defensive Evidence',"Judge's Decision",'Sentence','Final Judgment']]  # Features
y = data['Final Judgment']  # Target variable

# Check the datatypes of the features
print(X.dtypes)

# Initialize LabelEncoder for converting categorical data to numeric values
le = LabelEncoder()

# Apply label encoding to each column that has object (categorical) data type
for column in X.columns:
    if X[column].dtype == 'object':  # Only encode categorical columns
        X[column] = le.fit_transform(X[column].astype(str))  # Convert to string before encoding to avoid issues

# Split the dataset into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train a Random Forest Classifier
model = RandomForestClassifier()
model.fit(X_train, y_train)

# Save the trained model to a .pkl file
with open('AI documentation.pkl', 'wb') as file:
    pickle.dump(model, file)

print("Model trained and saved as 'AI documentation.pkl'")
```

# train_modelfdm.py

```
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.preprocessing import LabelEncoder
import pickle

# Load the new dataset
data = pd.read_csv('C:/Users/shrik/Desktop/aic/backend/fake_doc_model.csv')

# Adjust these names according to your new dataset structure
X = data[['Case ID', 'Defendant Name', 'Plaintiff Name','Charges','Judge Name','Prosecution Evidence','Defensive Evidence',"Judge's Decision",'Sentence','Fraud Detection Results','Final Judgement']]  # Replace with actual feature names
y = data['Final Judgement']  # Replace with actual target variable name

# Check the datatypes of the features
print(X.dtypes)

# Initialize LabelEncoder
le = LabelEncoder()

# Encode categorical variables
for column in X.columns:
    if X[column].dtype == 'object':
        X[column] = le.fit_transform(X[column].astype(str))

# Split the dataset into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train the model
model = RandomForestClassifier()
model.fit(X_train, y_train)

# Save the model
with open('fake_doc_model.pkl', 'wb') as file:
    pickle.dump(model, file)

print("Model trained and saved.")

```

#train_modelscom.py

```
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.preprocessing import LabelEncoder
import pickle

# Load the CSV data
data = pd.read_csv('C:/Users/shrik/Desktop/aic/backend/case_outcome_model.csv')

# Adjust these names according to your actual CSV structure
X = data[['Case ID','Defendant Name', 'Plaintiff Name', 'Charges', 'Judge Name', 'Prosecution Evidence', 
          'Defensive Evidence', "Judge's Decision", 'Sentence', 'AI Long Script']]  # Features
y = data['Final Judgement']  # Target variable

# Check the datatypes of the features
print(X.dtypes)

# Initialize LabelEncoder for converting categorical data to numeric values
le = LabelEncoder()

# Apply label encoding to each column that has object (categorical) data type
for column in X.columns:
    if X[column].dtype == 'object':  # Only encode categorical columns
        X[column] = le.fit_transform(X[column].astype(str))  # Convert to string before encoding to avoid issues

# Split the dataset into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train a Random Forest Classifier
model = RandomForestClassifier()
model.fit(X_train, y_train)

# Save the trained model to a .pkl file
with open('case_outcome_model.pkl', 'wb') as file:
    pickle.dump(model, file)

print("Model trained and saved as 'case_outcome_model.pkl'")

```

