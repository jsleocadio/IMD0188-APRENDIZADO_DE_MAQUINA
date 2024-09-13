# IMD0188 - MACHINE LEARNING

## Identification & Affiliation

Jefferson dos Santos Leocadio and Yuri Iohanssen Ribeiro Damasceno are postgraduate students in Information Technology at the Federal University of Rio Grande do Norte (UFRN). As part of their studies, they are enrolled in the course "Machine Learning" (IMD0188), instructed by Professor Daniel Sabino.

## Introduction

The Regional Electoral Court of Rio Grande do Norte (TRE-RN) employs the GLPI ("Gestionnaire Libre de Parc Informatique") system for IT asset management, problem tracking, and service desk operations. A department is responsible for reviewing and classifying support tickets if users have not done so, ensuring they are directed to the appropriate team. Improper classification can lead to increased response times, hampering users' ability to perform their tasks efficiently. This project aims to optimize the classification process using machine learning models to enhance the efficiency of ticket triage and improve service delivery.

## Pipeline

The project's pipeline comprises four main steps, as depicted in the flowchart below. Green boxes indicate the processes executed, while blue boxes represent the resulting artifacts at each stage. These steps are essential to prepare, process, and analyze the data to build and evaluate the classification models.

![pipeline.png](./pipeline.png)

## Fetch Data

The initial step focuses on acquiring data from a GLPI database dump. We recreated the database environment using a MariaDB container to ensure consistency and accuracy in data extraction. From this setup, we extracted a dataset containing ticket titles, descriptions, and categories, which was then saved as a CSV file for further processing.

## Preprocessing

In this preprocessing step, the CSV file is cleaned to prepare the data for modeling. The ticket title and description columns are concatenated, punctuation and stopwords are removed, and tokenization is performed to break text into meaningful words. We applied the TF-IDF (Term Frequency-Inverse Document Frequency) method for vectorization, creating five versions of the dataset by varying the `min_df` parameter (0.025, 0.05, 0.01, 0.1, and 0.075) to assess its impact on model performance.

## Data Segregation

At the conclusion of the preprocessing phase (`preprocessing.ipynb`), the datasets are split into training and validation sets, each containing 50% of the data. The split ensures that the model can be trained effectively and evaluated on unseen data. The resulting datasets are saved as CSV files, named according to their respective `min_df` parameter settings.

## Train

In this step, the training datasets generated from the previous phase are used to train two machine learning models: k-Nearest Neighbors (k-NN) and Random Forest. These models were selected due to their effectiveness in handling classification problems. The following steps outline the training process and model evaluation:

### Evaluate accuracy with k-NN

The k-NN model was evaluated using accuracy as the primary metric to identify the dataset configuration that yields the best results. Among the five training datasets, the dataset with a `min_df` of 0.01 achieved the highest accuracy.

### Training Models with the Best Dataset

Using the dataset identified as having the best performance (`min_df` = 0.01), we trained both the k-NN and Random Forest models without any further modifications. The results obtained are as follows:

**K-Nearest Neighbors (KNN)**

Accuracy: 0.5782  
Recall (weighted): 0.5782  

Confusion Matrix:

![knn_confusion_matrix.png](https://github.com/user-attachments/assets/eee14989-a6cf-4898-b957-b180431f4d9c)

**Random Forest**

Accuracy: 0.6633  
Recall (weighted): 0.6633  

Confusion Matrix:

![rf_confusion_matrix.png](https://github.com/user-attachments/assets/8af353ee-7511-468d-9985-22d9db837673)

### Training Models with Reduced Labels

After analyzing the data distribution, we noticed that 88% of the tickets belonged to three classes. We reduced the labels to four by consolidating less frequent classes into a new category labeled "Outros." The models were retrained using this reduced label set, resulting in the following performance:

**K-Nearest Neighbors (KNN)**

Accuracy: 0.5979  
Recall (weighted): 0.5979  

Confusion Matrix:

![image](https://github.com/user-attachments/assets/fc7e0e63-9ef2-430e-bc54-91bc88541a26)

**Random Forest**

Accuracy: 0.6837  
Recall (weighted): 0.6837  

Confusion Matrix:

![image](https://github.com/user-attachments/assets/0f67d194-521d-4220-affd-6665313d8c58)

### Training Models with Oversampling

We use the `SMOTE` method to oversample the dataset so that all classes have the same number of samples as the largest class. The results are as follows:

**K-Nearest Neighbors (KNN)**

Accuracy: 0.5339  
Recall (weighted): 0.5339  

Confusion Matrix:

![image](https://github.com/user-attachments/assets/2ab79765-b972-40b4-9ce4-91e295aac069)

**Random Forest**

Accuracy: 0.6845  
Recall (weighted): 0.6845  

Confusion Matrix:

![image](https://github.com/user-attachments/assets/33de3092-7af1-4204-8791-5206c62ba828)

### Training Models with Downsampling

We use the `RandomUnderSampler` method to downsample the dataset so that all classes have the same number of samples as the smallest class. The results are as follows:

**K-Nearest Neighbors (KNN)**

Accuracy: 0.5791  
Recall (weighted): 0.5791  

Confusion Matrix:

![image](https://github.com/user-attachments/assets/a8be76f4-62c0-446d-bd0a-daaf8a10d767)

**Random Forest**

Accuracy: 0.6711  
Recall (weighted): 0.6711  

Confusion Matrix:

![image](https://github.com/user-attachments/assets/678f12be-2bc0-4674-be88-be1df0755580)

### Hyperparameters Tuning with GridSearchCV

We use `GridSearchCV` to tune the models' hyperparameters. The following parameter grids were used for tuning:

```
knn_param_grid = {
    'n_neighbors': list(range(2, 21)),
    'metric': ['euclidean', 'manhattan', 'cosine'],
    'weights': ['uniform', 'distance']
}
rf_param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [None, 10, 20, 30],
    'min_samples_split': [2, 5, 10],
    'min_samples_leaf': [1, 2, 4],
    'max_features': ['sqrt', 'log2', None],
    'bootstrap': [True, False]
}
```

After 5 hours, the GridSearch returned:

```
Best parameters for KNN: {'metric': 'cosine', 'n_neighbors': 16, 'weights': 'distance'}
Average accuracy of KNN with the best parameters: 0.761531074289695
Best parameters for Random Forest: {'bootstrap': False, 'max_depth': None, 'max_features': 'log2', 'min_samples_leaf': 1, 'min_samples_split': 2, 'n_estimators': 200}
Average accuracy of Random Forest with the best parameters: 0.7786772810406115
```

Applying these hyperparameters to the models, the results were:

**K-Nearest Neighbors (KNN)**

Accuracy: 0.6187  
Recall (weighted): 0.6187  

Confusion Matrix:

![image](https://github.com/user-attachments/assets/c9929ebc-ce90-414f-a18b-77791951eb09)

**Random Forest**

Accuracy: 0.6911  
Recall (weighted): 0.6911  

Confusion Matrix:

![image](https://github.com/user-attachments/assets/e8b4b2e3-fbcd-4c44-bf7d-0e01b8b08109)

## Results

After evaluating various configurations, including reduced labels, oversampling, and hyperparameter tuning, we recommend the **Random Forest** model due to its superior performance, achieving nearly 70% accuracy and weighted recall. However, we identified potential shortcomings in the vectorization process, suggesting that revisiting and refining the preprocessing step could further improve model performance. Future work will focus on optimizing text vectorization techniques to enhance classification accuracy.