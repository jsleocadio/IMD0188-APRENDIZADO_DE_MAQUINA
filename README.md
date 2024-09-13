# IMD0188 - MACHINE LEARNING

## Identification & Affiliation

Jefferson dos Santos Leocadio and Yuri Iohanssen Ribeiro Damasceno are currently pursuing a postgraduate degree in Information Technology at the Federal University of Rio Grande do Norte (UFRN). As part of their academic journey, they enrolled in the course "Machine Learning"[IMD0188] taught by Professor Daniel Sabino.

## Introduction

Introduction to Machine Learning. Data Classification: classification problems; classification algorithms; classification validation; classifier committees. Notions of cluster analysis and multi-label learning. Applications.

# Final Project

## Introduction

The Regional Electoral Court of Rio Grande do Norte (TRE-RN) uses the GLPI ("Gestionnaire Libre de Parc Informatique") system to manage its IT assets, problem tracking and service center. Currently, there is a department that analyzes all support tickets and classifies them, if this has not been done by the user, so that they can reach the responsible location. Failure to classify the tickets properly leads to longer wait times, resulting in a customer who is unable to actively perform their activity.

This project consists of using classification models to optimize the efficiency of the triage sector and ensure faster service to the end customer. A pipeline was developed to classify the tickets descriptions by the customer. The dataset consists of approximately 65 thousand tickets.

## Pipeline

The pipeline used  in this project consists of four steps shown in the flowchart below. The green boxes represent a step taken and the blue boxes represent the output artifact of that step.

![pipeline.png](./pipeline.png)

## Fetch Data

The first step involves acquiring data from the source. In this case, we use a database dump that contains GLPI data and recreate the database using a MariaDB container. From the recreated database we extract a dataset containing the tickets titles, descriptions and categories and save it to a CSV file.

## Preprocessing

This step takes the CSV file as input and cleans the data. The description and title columns are concatenated,punctuation and stopwords are removed, and word tokenization is performed. To conclude we use TF-IDV method to vectorize our tokens and create five versions of dataframes change the min_df parameter that been 0.025, 0.05, 0.01, 0.1, 0.075.

## Data Segregation

This step occurs at the end of `preprocessing.ipynb`. We split the datasets created at the step above about 50% to train the model and 50% to validate the model. After that we save all datasets in CSV files named of each parameter.

## Train

This step receives the datasets divided in the previous step and carries out the training of k-NN and Random Forest model. There is the steps that we follow:

### Evaluate accuracy with k-NN

We use k-NN model to get the dataset with the best accuracy. After that, with the five train datasets that we have, the best result is the 0.01 dataset.

### Training Models with the best dataset

We use the best dataset without changes and get the following results:

**K-Nearest Neighbors (KNN)**

Acurácia: 0.5782

Recall (weighted): 0.5782

Confusion Matrix:

![knn_confusion_matrix.png](https://github.com/user-attachments/assets/eee14989-a6cf-4898-b957-b180431f4d9c)

**Random Forest**

Acurácia: 0.6633

Recall (weighted): 0.6633

Confusion Matrix:

![rf_confusion_matrix.png](https://github.com/user-attachments/assets/8af353ee-7511-468d-9985-22d9db837673)

### Training Models with reduced labels

We reduce the labels to four because on data analyses 88% of our dataset original was on three classes and we create another class named "Outros". The results:

**K-Nearest Neighbors (KNN)**

Acurácia: 0.5979

Recall (weighted): 0.5979

Confusion Matrix:

![image](https://github.com/user-attachments/assets/fc7e0e63-9ef2-430e-bc54-91bc88541a26)

**Random Forest**

Acurácia: 0.6837

Recall (weighted): 0.6837

Confusion Matrix:

![image](https://github.com/user-attachments/assets/0f67d194-521d-4220-affd-6665313d8c58)

### Training Models with Oversampling

We use `SMOTE` method to Ovesampling the dataset to all classes have the same number of samples that the biggest class. Let's see the results:

**K-Nearest Neighbors (KNN)**

Acurácia: 0.5339

Recall (weighted): 0.5339

Confusion Matrix:

![image](https://github.com/user-attachments/assets/2ab79765-b972-40b4-9ce4-91e295aac069)

**Random Forest**

Acurácia: 0.6845

Recall (weighted): 0.6845

Confusion Matrix:

![image](https://github.com/user-attachments/assets/33de3092-7af1-4204-8791-5206c62ba828)

### Training Models with Downsampling

We use `RandomUnderSampler` method to Downsampling the dataset to all classes have the same number of samples that the lowest class. Let's see the results:

**K-Nearest Neighbors (KNN)**

Acurácia: 0.5791

Recall (weighted): 0.5791

Confusion Matrix:

![image](https://github.com/user-attachments/assets/a8be76f4-62c0-446d-bd0a-daaf8a10d767)


**Random Forest**

Acurácia: 0.6711

Recall (weighted): 0.6711

Confusion Matrix:

![image](https://github.com/user-attachments/assets/678f12be-2bc0-4674-be88-be1df0755580)

### Hyperparameters tuning with GridSearchCV

We use `GridSearchCV` to tune the models hyperparameters. There some suggestion that we pass to ``GridSearch`:

```
knn_param_grid = {
    'n_neighbors': list(range(2, 21)),  # Testando valores de 2 a 20 para o número de vizinhos
    'metric': ['euclidean', 'manhattan', 'cosine'],  # Diferentes métricas de distância
    'weights': ['uniform', 'distance']  # Pesos uniformes ou baseados na distância
}
rf_param_grid = {
    'n_estimators': [50, 100, 200],  # Número de árvores na floresta
    'max_depth': [None, 10, 20, 30],  # Profundidade máxima da árvore
    'min_samples_split': [2, 5, 10],  # Número mínimo de amostras para dividir um nó
    'min_samples_leaf': [1, 2, 4],  # Número mínimo de amostras em um nó folha
    'max_features': ['sqrt', 'log2', None],  # Número de recursos considerados para melhor divisão
    'bootstrap': [True, False]  # Usar ou não bootstrap
}
```
After 5 hours the "Search" returns:

```
Melhores parâmetros para o KNN: {'metric': 'cosine', 'n_neighbors': 16, 'weights': 'distance'}
Acurácia média do KNN com os melhores parâmetros: 0.761531074289695
Melhores parâmetros para o Random Forest: {'bootstrap': False, 'max_depth': None, 'max_features': 'log2', 'min_samples_leaf': 1, 'min_samples_split': 2, 'n_estimators': 200}
Acurácia média do Random Forest com os melhores parâmetros: 0.7786772810406115 
```

Applying that hyperparameters to the models the results:

**K-Nearest Neighbors (KNN)**

Acurácia: 0.6187

Recall (weighted): 0.6187

Confusion Matrix:

![image](https://github.com/user-attachments/assets/c9929ebc-ce90-414f-a18b-77791951eb09)

**Random Forest**

Acurácia: 0.6911
Recall (weighted): 0.6911

Confusion Matrix:

![image](https://github.com/user-attachments/assets/e8b4b2e3-fbcd-4c44-bf7d-0e01b8b08109)

## Results

Reducing the labels, oversampling and tuning the hyperparameters of the models we recommend using the **Random Forest** model. We almost got an accuracy of 70% and the same to Recall.
Our guess are that our vectorization was poorly managed and our improvement to this project is to revisit the ``Preprocessing` step and refactor the script.

