---
layout: post
category: python machine_learning k-nn 
---

The k-Nearest Neighbor classifier is by far the most simple machine learning and image classification
algorithm. It doesn’t actually “learn” anything. Instead, this
algorithm relies on the distance between feature vectors 
Simply put, the **k-NN** algorithm classifies unknown data points by finding the most common
class among the **k** closest examples. Each data point in the **k** closest data points casts a vote, and the category with the highest number of votes wins.
<br>
<br>
Predictions are made for a new instance *(x)* by searching through the entire training set for the **k** most similar instances (the neighbors) and summarizing the output variable for those **k** instances. For regression this might be the mean output variable, in classification this might be the mode (or most common) class value.
<br>
<br>
To determine which of the **k** instances in the training dataset are most similar to a new input a distance measure is used. For real-valued input variables, the most popular distance measure is Euclidean distance.
<br>
<br>
Euclidean distance is calculated as the square root of the sum of the squared differences between a new point *(q)* and an existing point *(p)* across all input attributes *i*.
<br>
<br>

$$ d(p,q) = \sqrt{\sum\limits_{i=1}^n (q_i -p_i)^2} $$

### k-NN for Regression
When k-NN is used for regression problems the prediction is based on the mean or the median of the K-most similar instances.

###  k-NN for Classification
When k-NN is used for classification, the output can be calculated as the class with the highest frequency from the K-most similar instances. Each instance in essence votes for their class and the class with the most votes is taken as the prediction.

Class probabilities can be calculated as the normalized frequency of samples that belong to each class in the set of K most similar instances for a new data instance. For example, in a binary classification problem (class is 0 or 1):

$$ p(class=0) = \frac{count(class=0)}{(count(class=0)+count(class=1))} $$
<br>
<br>

## k-NN Classifier Example 
<br>
The example is trying to classify if a set of measurement on a brest canser as benign or malignant. This dataset comes with the sklearn package. 
<br>

#### Load the dataset.




```python
import numpy as np
import pandas as pd
import pprint
from sklearn.datasets import load_breast_cancer
from IPython.display import display, HTML

cancer = load_breast_cancer()
print(cancer.keys())
```

    ['target_names', 'data', 'target', 'DESCR', 'feature_names']


#### Convert the loaded dataset to pandas dataframe
<br>
The loaded data is in sklearn's native Bunch object. It is similar to a dictionary. Converting it to a pandad dataframe makes it easier to handle data slicing and manipulation.
<br>

In the block below let us convert into a pandas dataframe and display the first 5 rows of dataframe


```python
df = pd.DataFrame(cancer.data,columns=cancer.feature_names)
df['target'] = pd.DataFrame(cancer.target)
display(df.head())
```


<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>mean radius</th>
      <th>mean texture</th>
      <th>mean perimeter</th>
      <th>mean area</th>
      <th>mean smoothness</th>
      <th>mean compactness</th>
      <th>mean concavity</th>
      <th>mean concave points</th>
      <th>mean symmetry</th>
      <th>mean fractal dimension</th>
      <th>...</th>
      <th>worst texture</th>
      <th>worst perimeter</th>
      <th>worst area</th>
      <th>worst smoothness</th>
      <th>worst compactness</th>
      <th>worst concavity</th>
      <th>worst concave points</th>
      <th>worst symmetry</th>
      <th>worst fractal dimension</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>17.99</td>
      <td>10.38</td>
      <td>122.80</td>
      <td>1001.0</td>
      <td>0.11840</td>
      <td>0.27760</td>
      <td>0.3001</td>
      <td>0.14710</td>
      <td>0.2419</td>
      <td>0.07871</td>
      <td>...</td>
      <td>17.33</td>
      <td>184.60</td>
      <td>2019.0</td>
      <td>0.1622</td>
      <td>0.6656</td>
      <td>0.7119</td>
      <td>0.2654</td>
      <td>0.4601</td>
      <td>0.11890</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20.57</td>
      <td>17.77</td>
      <td>132.90</td>
      <td>1326.0</td>
      <td>0.08474</td>
      <td>0.07864</td>
      <td>0.0869</td>
      <td>0.07017</td>
      <td>0.1812</td>
      <td>0.05667</td>
      <td>...</td>
      <td>23.41</td>
      <td>158.80</td>
      <td>1956.0</td>
      <td>0.1238</td>
      <td>0.1866</td>
      <td>0.2416</td>
      <td>0.1860</td>
      <td>0.2750</td>
      <td>0.08902</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>19.69</td>
      <td>21.25</td>
      <td>130.00</td>
      <td>1203.0</td>
      <td>0.10960</td>
      <td>0.15990</td>
      <td>0.1974</td>
      <td>0.12790</td>
      <td>0.2069</td>
      <td>0.05999</td>
      <td>...</td>
      <td>25.53</td>
      <td>152.50</td>
      <td>1709.0</td>
      <td>0.1444</td>
      <td>0.4245</td>
      <td>0.4504</td>
      <td>0.2430</td>
      <td>0.3613</td>
      <td>0.08758</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>11.42</td>
      <td>20.38</td>
      <td>77.58</td>
      <td>386.1</td>
      <td>0.14250</td>
      <td>0.28390</td>
      <td>0.2414</td>
      <td>0.10520</td>
      <td>0.2597</td>
      <td>0.09744</td>
      <td>...</td>
      <td>26.50</td>
      <td>98.87</td>
      <td>567.7</td>
      <td>0.2098</td>
      <td>0.8663</td>
      <td>0.6869</td>
      <td>0.2575</td>
      <td>0.6638</td>
      <td>0.17300</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20.29</td>
      <td>14.34</td>
      <td>135.10</td>
      <td>1297.0</td>
      <td>0.10030</td>
      <td>0.13280</td>
      <td>0.1980</td>
      <td>0.10430</td>
      <td>0.1809</td>
      <td>0.05883</td>
      <td>...</td>
      <td>16.67</td>
      <td>152.20</td>
      <td>1575.0</td>
      <td>0.1374</td>
      <td>0.2050</td>
      <td>0.4000</td>
      <td>0.1625</td>
      <td>0.2364</td>
      <td>0.07678</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 31 columns</p>
</div>


Code block below will explore the distribution of the target classes. 
This dataset has 569 samples. 357 of them are benign and 212 are malignant 


```python
class_dist = pd.Series(df.target.value_counts())
    
class_dist.index=['benign','malignant']
print(class_dist)
```

    benign       357
    malignant    212
    Name: target, dtype: int64


Code block below will prep the data such that input X and target Y are separated. We can do this by using pandas drop function. Once we have X and Y we need to split this data into Train and Test sets so that the model can be trained on a training set and a new test set, previously unseen by the model is used to evaluate the models accuracy. Sciki-Learn's train_test_split function makes it easy to do this. The default test split size is 0.25 i.e, 25% of the data is split as test.


```python
from sklearn.model_selection import train_test_split

X = df.drop(['target'],axis=1)
y = df.target

X_train, X_test, y_train, y_test = train_test_split(X,y,random_state=0)
```

Next we can feed this train data to train our k-NN model. If the power parameter p is set to 2 it uses Eucledian distance for the distance measure. In the block below we are fitting the model with our training data X,y.


```python
from sklearn.neighbors import KNeighborsClassifier

knn = KNeighborsClassifier(n_neighbors = 1,p=2)
knn.fit(X_train,y_train)
```




    KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
               metric_params=None, n_jobs=1, n_neighbors=1, p=2,
               weights='uniform')



Let us predict what the output might be if the input was the mean of all the features. The model will predict the output class as 1 which is malignant


```python
means = df.mean()[:-1].values.reshape(1, -1)
pred = knn.predict(means)
print(pred[0])
```

    1


We can also get the model accuracy from the score function. This model is 91.6% accurate


```python
print(len(y_test))
knn.score(X_test, y_test)
```

    143





    0.91608391608391604



Alternatively we can also plot the accuracy like so


```python
def accuracy_plot(X_train,y_train,X_test,y_test,knn):
    import matplotlib.pyplot as plt

    %matplotlib notebook

    # Find the training and testing accuracies by target value (i.e. malignant, benign)
    mal_train_X = X_train[y_train==0]
    mal_train_y = y_train[y_train==0]
    ben_train_X = X_train[y_train==1]
    ben_train_y = y_train[y_train==1]

    mal_test_X = X_test[y_test==0]
    mal_test_y = y_test[y_test==0]
    ben_test_X = X_test[y_test==1]
    ben_test_y = y_test[y_test==1]

    scores = [knn.score(mal_train_X, mal_train_y), knn.score(ben_train_X, ben_train_y), 
              knn.score(mal_test_X, mal_test_y), knn.score(ben_test_X, ben_test_y)]


    plt.figure()

    # Plot the scores as a bar chart
    bars = plt.bar(np.arange(4), scores, color=['#4c72b0','#4c72b0','#55a868','#55a868'])

    # directly label the score onto the bars
    for bar in bars:
        height = bar.get_height()
        plt.gca().text(bar.get_x() + bar.get_width()/2, height*.90, '{0:.{1}f}'.format(height, 2), 
                     ha='center', color='w', fontsize=11)

    # remove all the ticks (both axes), and tick labels on the Y axis
    plt.tick_params(top='off', bottom='off', left='off', right='off', labelleft='off', labelbottom='on')

    # remove the frame of the chart
    for spine in plt.gca().spines.values():
        spine.set_visible(False)

    plt.xticks([0,1,2,3], ['Malignant\nTraining', 'Benign\nTraining', 'Malignant\nTest', 'Benign\nTest'], alpha=0.8);
    plt.title('Training and Test Accuracies for Malignant and Benign Cells', alpha=0.8)
    
accuracy_plot(X_train,y_train,X_test,y_test,knn)
```


    <IPython.core.display.Javascript object>



<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAyAAAAJYCAYAAACadoJwAAAgAElEQVR4XuydCdh913i+34wikUQEEQRBIkIMQY011BRBVTVU6aCDDoaq0lYMRbWltMZWi7ZotUXbv3mKoebEFNUY24hEjCGCyDz8r+fr2en5nZzzTetd613Dva/LheTsddZ6nufs8977Xft8OxkHCqAACqAACqAACqAACqAAChRSYKdC78PboAAKoAAKoAAKoAAKoAAKoIABIIQABVAABVAABVAABVAABVCgmAIASDGpeSMUQAEUQAEUQAEUQAEUQAEAhAygAAqgAAqgAAqgAAqgAAoUUwAAKSY1b4QCKIACKIACKIACKIACKACAkAEUQAEUQAEUQAEUQAEUQIFiCgAgxaTmjVAABVAABVAABVAABVAABQAQMoACKIACKIACKIACKIACKFBMAQCkmNS8EQqgAAqgAAqgAAqgAAqgAABCBlAABVAABVAABVAABVAABYopAIAUk5o3QgEUQAEUQAEUQAEUQAEUAEDIAAqgAAqgAAqgAAqgAAqgQDEFAJBiUvNGKIACKIACKIACKIACKIACAAgZQAEUQAEUQAEUQAEUQAEUKKYAAFJMat4IBVAABVAABVAABVAABVAAACEDKIACKIACKIACKIACKIACxRQAQIpJzRuhAAqgAAqgAAqgAAqgAAoAIGQABVAABVAABVAABVAABVCgmAIASDGpeSMUQAEUQAEUQAEUQAEUQAEAhAygAAqgAAqgAAqgAAqgAAoUUwAAKSY1b4QCKIACKIACKIACKIACKACAkAEUQAEUQAEUQAEUQAEUQIFiCgAgxaTmjVAABVAABVAABVAABVAABQAQMoACKIACKIACKIACKIACKFBMAQCkmNS8EQqgAAqgAAqgAAqgAAqgAABCBlAABVAABVAABVAABVAABYopAIAUk5o3QgEUQAEUQAEUQAEUQAEUAEDIAAqgAAqgAAqgAAqgAAqgQDEFAJBiUvNGKIACKIACKIACKIACKIACAAgZQAEUQAEUQAEUQAEUQAEUKKYAAFJMat4IBVAABVAABVAABVAABVAAACEDKIACKIACKIACKIACKIACxRQAQIpJzRuhAAqgAAqgAAqgAAqgAAoAIGQABVAABVAABVAABVAABVCgmAIASDGpeSMUQAEUQAEUQAEUQAEUQAEAhAygAAqgAAqgAAqgAAqgAAoUUwAAKSY1b4QCKIACKIACKIACKIACKACAkAEUQAEUQAEUQAEUQAEUQIFiCgAgxaTmjVAABVAABVAABVAABVAABQAQMoACKIACKIACKIACKIACKFBMAQCkmNS8EQqgAAqgAAqgAAqgAAqgAABCBlAABVAABVAABVAABVAABYopAIAUk5o3QgEUQAEUQAEUQAEUQAEUAEDIAAqgAAqgAAqgAAqgAAqgQDEFAJBiUvNGKIACKIACKIACKIACKIACAEh8Bj6xxSl8w8zuv8VztvLyfzCzG5vZPczsrK2cuOS1nmMlTqXK0x9nZg83s983s/esM8MbmNlrt7iCD5mZxm/l2NfM3mlmu87++8mtTLySeR5rZj9tZo81s49UMqd9Zhm8g5ldxcx2NrM/NbN/C5ifNNl99r6/YWarrrt3NrO/mL3uTDO7V+Jcdf6fmNmrzOzFc2P9kpk9OlCPxGUNc/oRZvb3ZvYuM9NnbDOH8n3dhRdeYmY/NLPPm9nrzez9mxko02tqvFYsLlW16d3M7Cgzu4mZ7WdmF5vZN83s02b2ZjP7TKI+fDYTBUw9HQBJVTD9/KcvGeIWZnZtM/vS7D/zLxEUvCD9bVeO4AkNnmNlXHLY0JsFkKub2W8tmeWPm5kK90+amcB0/lB2/qnAylRU3d7MfsbMvpLwfg82s9+bnX/erPA7J2G80U6tsah49uxGxpfN7IuzAuIt6xT/OT2bB5A3mtkfrXizac761wBIuiOtg1YKgPyHmZ09k/AKZnY9Mztk9v9fZmb6T8RR47ViXgfdrPgzM1MdJHD7gpl9bXZz6vpzcPd3ZvZXCQICIAnieZwKgHio6D+GoOR+swtU6YvUgWami+Vpsw9/yuo8x0qZR63nbhZAVs1/AryNOig51+8FIK80s5ua2XfM7Kpmps+AilWOzSmgL211HL5lZudu7pSsr1K3Q12478+uZbp7GXlMACIQutYMcC9YmNBeZnacmQmYDssMIPJKnn13dmc8Upuc7z0ygCy7KfOTZva02XerdjLo81r6qO1aMb/+Pczs1WYm0Dh+1j38+oJAh5rZY2afm812pZZpDICUTt7C+wEgwQasePtIAKlTkT5nBYD8r6/XMbN/nxV8f25mf2xmH1vR9ekzCf2tSkWOtq2cZGYqQqOPCUDUPdbnTt229y5MaioOtQXr8ZkBJFqPUu8PgFxeaV3rdM1T8azPCMf/KfA7ZvYwMzvRzH7TzC5aIY5qV3WnUrZhASDByQNAgg1IABBtv3m+mb1utr9YW3RuO7urpj3HbzCzA8zsvmamPdja0nXl2R1JfbjVvtQ2ncVj2bapPc3sA7M7gz83Kyh090Zbg3TH+m2zbs3iXU7PsTTPw81M+7dvPpu09tOqQ6Q7l5MWat1u5riNmd1z1ubVOnabtXnfN9NzcfvPvN6vmO3fvpOZXWnWLdId/HeseGM9T/MLZqZnOXR3WsX1S8xM24428wzIqvVspQPyY2b2s2Z2s9mcz5h5qrV8b+ENdjGzB8z+o9xccVaM6c6w1ii/9589q7Fqbsrc4h3mVa/9dTP7NTP759k+eX0pK3PqAn57xUmaowrGo83shrOundb0n7PPhArf+UNf+PJAOqjDIn+/ambaJqGtahfOXjzt3142/1XbMea3NOgLU0WXnqPa28zuPbvLLd31hXer2edSd/q0n1mfK30Wf7BinZud93rbKtTRfMjs/ae96afMnsV4k5lduvDeum78opndbjZX+ajPub7sXzO7Dqz3GVu2B16vX9zSpM/zI2afQX2O1A0QKPztkjvD88WCCjgVJ/JSe8P1vNBGhdwEIMqLOmvS/QkLi/jr2edDz9LoNcu2YGkeek5Ec7/aTDttPdRnQtdibRmZP7ZT5Ej/R822NupzoPGl+wmzz9wi1M0X+Z+anXvk7DOh7St/aWb65/OHrnfaX6+16I6y1qLPwP/McqH1LB7zGdPWIl2L1bXUoc/di8zsv+dOWpUDvWQzzypdc/b9pS2e6lppu6muVdpyqs+MrkeLh3zW3LS2h86ei1InXluX3z3T4vwl5+lzIc31vaDn0LQOXdP1ftt9BmTVtlTlW99h6oQs6qzO4U/Nrn36vpBP2o3w1tn1cbEgn79eKWv6ftZWrx+Z2QfN7IWz7/z5Ja93rVDnQbWErlPSQTXCdH1apsNWM7HedUPf42+fXfu1jmX1yXrn69/pc6nvVOVf9Y78Uyfl5Wa22EnZ6mdT34PHzLKlbOo7SOPrM6brqDq+HFtQAADZglgFX7qZDshUEKuAUnGjC5O+BPSFpYd5dWH7eTP77dmFWs8IqPjVhVb7UFVU6IIrGJk/1oMGFS2nzz7c+hLQxVIfdL2nigKBT66x9MWgi6keJNU2ilPn1vKvs2JeBcBmAUSv1UVEX7gqBHVxUdGoO7cCm19ZKKAnvbVFQw/F6eKjgkyF+C3NTJ8lFULSfv6YPFBhIs10wdLeVq1DIKKL4Ha3UG0WQB5pZvqPMvI5M1Ohri83fVEpF1rrfKH/zFlhr7woH/pCV9GuQl9fhip4VDCqgBP0qniZ3++s9T9rnbtXix8l7clXgSFA0Pz0xSy4UEGjdvzioYJawKkCVMWE5qgCXoWGts7oAcX5LN5l9v91nnKj/Gj++rK9xhwk6H1SAOT/zYqHKZ9a03QnXaCsYm3KmwqLG80gXppq7dN+8Wm9W5n3qqJCW320T3raUqQvSwGHYEr/Tjcq5NV0yGeBoAp7fdZVBCjb0laF6nM28RC5Hq7W2gX4KvymL2at73mzNxKUq9M1fY6UQ41/8OwcFbfSajqmYkFFpHxXgfVfMx+lu24crHdMAKK8qgty65nvE/jpJoSgQ10RzUvjLQMQPbyuc1T86jOjIke+6hq47EHlrRY5uiap4JMPKpgEG9M1Rj9EoaJ6FYAou/eZXc+kneBVvgssfnl2XZs00vuoaBL0CXD037r2qTBWNhcfmtd5U8YE7AJa5VwZkW+6lugha/3z6VqiHAhiNQd5pc/edPzjgr/LvNM1SzcmpLXyoc+68qHPra5NunGh68X8MQHIR83s7rPnjXTt1TVaNwTk7/Ss2XSe5q/Pp64J+o4TfCi/us7re0I3irbzEPoyANH3jGBCnz19N+i7Zjr0naAOsIBremBd61S+lAGtSd/n85A7Xa90nVTRru8YfTbko8757Azy589Zda1Q8S4IV5aluTKkz73eX9nTDaxFHbaaifU+o9N3rDxQob/VQzfN9B2s65V0VWYOmuVTn1nlZR6Qt/LZ1Jh/M6t3NJYehNezivr+UD0l2EvZDrbVtXbxegCkThu3AiBage4aqGic7uJOq1KBow/J/IVf/+6us0L95NkX2rwK6wGIXqcvHYHL9AtZAhrdndPFU3cXdad0OrzG0hfi1NFRAfMvc++hL+Tfnf3/rQCILna6WM93OlSg6gKmdajA1bqmY7o46v/ri1sF7nQ3Sq+X/osXTn0p68KtzpC+jKe7kNJKRY5+5UNHTgCZftVHRYaAYXpQXJ99gYS81C+yTBqq8FGXQ5CiVriKsPkvSBUT823v1GdA9EWpO4Kal76wdag41BehvgD1pbd4qIBQUaB5aE3zc1ThrC+daY4qtOSBvNWDx4KT+UNgq4Juem4iBUA07h/OCozFOauoUDEw3+nQHUYBitaiu4u6Uz0dW533qqJCQP4TZiZIV6anu7/SScWObl5IQwGkDnUklAkV9cro/KEiXZ0bAdNGx9QhW7YFS/9O46sYe9LszrTGUyb1OVH3Rd7rsz11Z6ZiQa/TuXpYfCvPlcwDiLpS+rzqM6wbJzoEgLorLz8EtKsARFqq2Ji/1urOvLSVlpq/7rhOx1aKHJ0jOFJnVdcY6T+tUZ8JAbmuHasAROcvQrvmo8+5wO0P5ualYlsQqq7KfHGq7os+e+p86vM4/90xZUyezHeddCNKeqrroEJYc5iOlC1YAgA9QyTImT/0PvosC2oEVvPH5LMyKoidYEjrEvQo9/q8zXdPBFSCEEGXOtNT5qYteRo/FUDkm74P1F2Qv8qIvJk/pu1Hyp7WN10r9DnR/9f3trYHzv+4yHS90vexgG1alwBH11UBm6Dlw3NvtOxaoc+evld1Y0rdAhXb0yGonX60YRWAbCUTC8u+7P/qRpg6m6pnnrrqRSv+uWodZU950Wd4vgOu+Sufgg9B2nRs5bOpz/a0a0TznH/OTsAmbxdheItLGO/lAEidnm8FQPRB0FYVffC2cqj40B1WXWTnW5MbQYMKFF345w8VXdqStVhIe42lC6/AQ3dj5y8g0xymL5CtAMgqrXTh1l0ywYIu6NMxAYgu9NJscYuWCgYVjfM/Xzx9oehnF3XneP5QMac7/4KrnAAyPdw9dRcW161/ry96QZSgQ3dzdAdcX+QqyDY6UgFk+jJ86ewLU++n65LuEkqjxVa87jjrC0qv0V0ybaNa79CXkcYQeDxjo8UkdkC289yKIETbgVRkqTCajq3Oe1lRoS9FgYc+N9qWsLg9SMWJsjlfDE3vm7o/fT0AmSBHhdYTFzxRR0Q3G3TnVcWaNNUxFQsqynS92+ovpM0DiGBUhZQKhukzrswrb4ITFXyrAGRVhNTV1HbGxc/6VoocFcnqwuiarq2zugs+f6go013eVQCiu776nM9vqdO2Fq1F163N/qTwdENlEWamjC3rIqgroevvetvDPH9+WV09dcLUZZvfQjr5rGuvQHH+UCGursP8T0FPhaVARdf1xS1O6oyoy78dAFmWFQGlQEdZmd+iqu8d3fiR5w9ckm9Brq6Jmqe2CE7HBCC60SD954/plwUXu1nLrhVTftU1UMYWrxWT3qsAZCuZWPUZmq49+j6ffgp7E5fstZfoZsJ6uwnkubIiYJ1uTm3lszndyJPeGovDQQEAxEHEDENsBUB0B0t3LFcd+rK942xvpIo3FTw61G7Vl8biHbv1oEGtXUHL4jEVFM9d+HsVXmNNXxy6COouxKr33yqAqMgRWGirgu5i6E6eDhUhAjrdaZuOCUBULOpCuXjozpm2G+iO/bR1RHdkpLO2ESxuddP500U9F4BMWw70paWCZtkxPQivDog6IbrDrW1kKsIEBdOX3qp8pQCI4EvvpXnqS28ehPUrJ7oTrruW8z87rTvQ+rLVdgS9ZqNDd/W0dWwZOC87N6UDstidWxxf3SV9kQkMdAd6ypvWpDukys90bHXey4oKZVHdDRU7uqu97NDfn9E8pm7cdLdTd5C15VFwsmzP/Ea6rwcgKmz1jM2yB8E17uT9/E+VTsXCMmjZaC769/MAoiJQ2870OdeNExXp6pIJ1tRZUR7XAxDlSR0t3XDQ50Xfo/pv3XxYvB5vpciZXqtu1OLzKVqDto+pS7aqyF/sok26CNiVPZ2/WFiqAFd3RfCl7wqtRVux5M9iF2zK2HznaHoPfZalsX7VSYA4HSkdEI2h65A6Buq8qgifvr80b1235wvKyWfNRd95i8+gqbBX52b+Dv/U+dLnbdoeOJ8nFfsTjG92i810DZnfliqwlsbqOgly9NmaB7KpwNXWaW1BXXZM36e6ITdt15zeS/Nc7ExOedE1XDcJp2PZtULXWn3uVhX/k3arAGQrmVj1eU0BEAGQsqL6ZNmzhxOM6carbjbo2MpnU7WCbozoWqjrl66bi89ObuY6xGvmFABA6ozDVgBkvd+0V/Gri6oufKuOxSJgo2dAlu3NnD7cuqjq/OnwGkvtXxVGq7a3TEXTVgBEbVTd/dQXw7JDd1j1pTAdE4BMRcriOdNWl/lOg75MpL2+kPWcyeLxlNkzA7kAZOpmbCbl0lhZ0qGCWFqrMNOhLoP2vquQWXyYNQVAVPQKWrWf9lcXJjn98UV1ZQRPU+E0bblb1lVatk4VkiooF++UrtIkBUDmtzItjq/PiGBv+mN4y95//sH3rc57WVExfaFvxn8VKyrMBSO6/uguuA59mevOuopLdfnkx2aO9QBk6lgu7oGfxn3QbGvW/PMpU7Gg4kFFxFaPRQCR1iokdONAd5/1uZ2K2VUAomuFPrPr/SFYdYcFu9OxlSJHnTp5JhjS52Lx0HZX5XMVgKjLqs/F4rEs07rhItiSDquOVcXm4pae6fzp+Yv5TksKgOiuvNakLK06VDTrZsT8HHSjTJ/3xWOZF7rxomvK4pbb6VyBpq5x2+mALHsGRF0ugbWeMZnXcbpZsJlc6yH1aVva5K0gTdut54/N/GjG9EdLt6vDdN3ZSiZWrXG7W7D0PbXZP+yobWm6saZjK59NvV7XcHXWBLjqMmq728dn3XVtTefYogIAyBYFK/TyrQDIqqJbhYQKSpG7vvBVPOgOs9r7+vBo64MeGNzKtil94Oa3iUxybAdAtjKWN4Coda/ug7YlCNDUndBzBNO+bnU59NkQdEzH/K9gLXvQvUYA0b5mea87NfN7gJfFePEPxOluo+4m6a68fhFlKgIWC/8UAFGRJQhRh2bxDylqjvoCVY7V4dOdZR2RADJt11hVmK36ZR/dvRWM67On4llfWnpWarpTN23f8waQqajQloONnttY/NEAZUcArs+KHkIVOGn+gig9O7XRkQIg013nZQCy7OHojeaif78IIIIJAbWuASpgdFdaXTgdqwBkAgQ966WbLdrCpS1hOlcPo+oztAgHWylyUgFk1V+ZXwYg89nQtVD747X9R6CvLUcqkreac08AUd60bVI5UmdHXukaoSJb31/TD1UsfuaWzWHKRw0AorkoZ9pON9/FnbxX91w/FLHeIWienrVMuWEyr10qgKy69q3nx+Iat/sQ+vR5VXdCPxKz3qEfxNDzUDq28tmcxtQNRXWgdMNGP2ygmxc6Vv1gymauTcO+BgCp03oPAFH3Q1uAVCzo1x8Wj+lhxxYAZKMtWNNdts12QLRmdXKW/bE7bVPTBUrFViqAbLQFS9spdCHL1QHRA5e6IKuDoRb6dg9dJ/Swtu6Y6oKr5wmmL8ntAojG0fYr3U3a6JjflpB7C9a09Wn+WZ5pftMX1lYLs+lnhvWZ05ay+UOFsL6k9d/zAOKxBWv6BbZVWxc30n3697pbru6APmerfhhgcayULVjTg9PLtmB5AYjmO92E0f+e36a2CkCm5wGWdW4E6XpwNwVANtqCNd042epzFsuKVMGS7sZPv1Q27586POqAbjXnngAy/RCFnuPQXefFY/IiFUCmrUc5tmCt+hleXUt1F15/3XuCXv1il7o9i79Kt9Fn1AtANtqCpa6Lun8emVi1pu3+DK++n9QB0fY8dYIWtxmuer/tAMj8WLo5pu8JPVuo/62dGPM/iLKRd8P/ewCkzgh4AIjuXOtOybI9pdrjqwudCosWAGR6CF1tTv0y0+Khok53mTcLIPp1Ge3/nn/IdRpTYCJNPABk2gKzbF7ak60737rTlwtAtKZpu8v8synbTf1013F+K5weFtSdcnXT9Ktqmz2mu9yrCgyNo5/31f5l3fXUl4X+e/4hdH3BL/5CzuL7Tx5Ia/0SykbH9CzP4t5ynTc9BLzVL+Gp0F32YLcKwOmhxnkA2eq8l23BmjpgulOvAmPx731spMXil63u2Oo7Q9C80VibeQhd+6iV/flDIKZnD/R8xbKH0D0BRDdpBNA6tG1q6hKtApAJCpXFxUJDvzClPKYAiLoouuuva4+2wC3+LLOeX9CNBA8AUZdXRZNusix6OT2js9WcLwMQXa8FEMsekl4vf9ONhmUP/Uon/XqZrp2pADJ1NfXsih5CX/xlNT07JRjy2oKlNU/FvLpO6ujq0M0ifVerW61/v9m/oeQFINND6NolofdfLOKnm2UemVjP9/k/RKhfMVv1S3eLf4hQOxlUJyz78YFV75cKINO4U0a0pXtxi/JWrrHDvRYAqdNyDwCZfuddWwx0x27a4qIHtVSAT883tAAg8z/Du/hFNm3/kpObBZDp72LoYqq7OtPFVj/lp7vF2n7kASB6yF971rVFQ9uI9KyDDn1xaluZ7nrpyAkg0xe5fuZWALH4U4HqROguzvRzpHr2Qr+OpDtK8z81qqJMv5ilfej6YtAzITqmwnfVA8WrPmHTH+OS/qv+gKPOnS7uKv61DUPHVOzp796ouJ8vBgUoejh1/md4lQtprs/V4h/+UnGhn8edflZR4KHCVwWa1jT9Ko50VAdIRdtWv4SnjKqAF1hMumqeWt/0jNY8gOjzu5V5r/oZ3gkQdcNB24YWf1lJfmub5vS3OvTDC9oeufhHwKZnJvQs0/xDxqv83czP8OrBbWV/+hse+j7Sz3DKA8GsoHnxZ3g9AWTV3FcByLTNUp0OPcg8HbqWajuh4CkFQDTe1JmWX3qwd7o2aVuU4Hi9n+Hdyhas6fm8xS6wIGr6ud6t5nwZgExF3mavzZOm88+A6ftr2nKku+TSWt0gHakAojF0jdbzcvqBE3Umpswp59JHhxeAqOuk7OjzreupPJ2O6UaFrj3yfv4n7fUaPTciaJ7fZuQFIPM/w7uYb3momkGv8cjEqs+d/rnqE33G9d2pH8CQDot/QFA3VuS7tj9OPwygX3LUVj19F+h7bvr1vOm9NK6u4fphAD0jpGMrAKLtVhpDc1r82Wr9XL++dxZ/UXS9dfLvZoFCiPoU8AAQrWq606/iSvvOVUzpg6S7CiogVWy0ACBai9rWujMnGNFDsbpbqWJYFyMVz/ri3OzP9+nOuu5mCjS0PUnjqSukL3ndcVdBoS/61C1YmvfU2tZFS5oLCPW3L/SLM6X+EKEe8J624amwVNdAX7L6QpN+ysP0MOpUaOohfBXm+gNl+tJXbvTLTboA68HP6Utavzajwlav17+bClx9cay6e6X31fNJ6mioAzD/m+qLn8bp11fm90urcFUW5JfG0DM8el/dGVUnTNtL5v8Qob549IyDPBWISQOtSV9yKr6nv1au91YmVCypgFY29Fr9TYTpgX7d0d3ql7B004PFKj5UwKtI1T/Tth2tS7+KJRhZ/OvrW5n3en+IUHf59QWtO+paj5650fq1pulu8qTX9HOW+tLXHVrpK430PI4Odb+W/ZXsRd/WAxC9Vr7Lk63+IcJIAJEGKh611WP+j01KWxX0KpRTAUSFqQop6actOvoMTtem6Q+uKu/6Zb3p2OhB72VF6nRjQmMI5JVL/bqXPhO6jm4n58sARHf21dWZ/vCqsqdrh4q2ZX/JfD5HEzyrYNS1U9dQfWaUY12zdQPHA0B040lbuvSZ0JymP0SoZ5/0zJu64tsBkPlfwdKNC91oUMdl+mO6uhE2FcNat/654ErXVH3u9FnVjUNd73RTSNeIxV//8wIQvf/8HyLUDYDpDxEq99JBN1IWd1Ss91fVNeZWngGZvFfepYO+J+W5tvvqO0ufO+VT10sdi7/uJwDQfPQ6+ai/YaObPdP3gvSdv9ZvBUCmH8bQr2Mqe+pUCTr0HaTv8mV/N6m+yrKyGdEBqcyQ2XS8AEQfRH0p6leE9MWmOwa6gOkuv75gtJe/FQCZLpC6Q6oLkw5dmPTFoQuzLjxb2esuPfTlpdaz7njqAqeiWGCiO0xeAKJ56kInH3RXT8W2vkxVFAqalnmw2VRu9i+hazx98emOstarC7yAQcWAujLaCiNA1aGCQS14AZ8AT/9fX/jSR4WEivvFP3ipL2itRX8AcPqVp8Vien5NKp4ERJv5UleHRq+b9tgKiHQo29rGpX230lWFrH6hScWUiv3FTo++uPRLR7pzqvULWAQYKhJ0B3R+TcqTnjtSd0Tvq0JAOZNmKg63CiCar7bcCdykqy3sTpMAACAASURBVL64VFjoy1wFteYrrZdpttl5r1cICNrlqb58pZUKGn2BylP9OIHWM3VI9dlSXpUTAZO2aepOrPTUVsf5P/C1Xk43AhCdq8JdW5/0XgIyeauCRd2xxV+NW1UsbPazsvgQ+nrnrfczvCrS1M288SyDKtSUHz1rp2eaUgFE81LBpC6c8iD9Bc26Lqkw1md+8Wd6twMgeh9tpdPnUJkQFKiwUr51jdpOzlcVm8q8im0V+lqPjlUPLc/7omuJ1qYbZdJEN2+UV3Up1IXV598DQPSeKvDlqz7zurao+FaHQplcpsV6+ZmgYP410lfXD3mp6638XLbNStcbfU7VfdHNFEGR1q3Pg7679VnVjyBMhyeAaExdb6SDQE866NonHfS/1QFcvMmXA0A0D9WmgmRpIRDUNVMwouuUAFzf1YLzxUNZVm0jH3XNlcb6XtBr9VO96vROHYytAIjqBT0bpXF1Q0rzkS+CHHkgTzf77Mlmr1ndvw4A6d7iIRY4/UqWfqFn2koyxMJZJAqgwDAKTL+UNP9w/jCLZ6GhCky/krXsh1tCJ8abt6sAANKud6PNXHetdSdXDwvOH7oroT2fuqOrTs/i3fnRdGK9KIAC7SqgO83aYjL9MdNpJeoS6W9V6I64uo0b/axyuwow8ygF1PnTFtTFH/bQT6Xr+SJ1E5b9OELUfHnfxhUAQBo3cKDpa+uMfolD2xC0N1qtarWLte1Hz7bogWE9vMeBAiiAAq0qMG3/0vZAbe/Q3zbQlg9tX9KxlW2mrWrAvGMUmH41T9sKBSHaUiQY1vY0/e9lP+IRM1PetQsFAJAubBxiEXqATw9TT38Ub9rHrmcY9Pc2tH+ZAwVQAAVaVkBdXj3npucz9PC/Oh7TQ9f6cQRusrTsbt1zV/dDz+romR39UIue19FD1/pFQf1ogJ694EABNwUAEDcpGQgFUAAFUAAFUAAFUAAFUGAjBQCQjRTi36MACqAACqAACqAACqAACrgpAIC4SclAKIACKIACKIACKIACKIACGykAgGykEP8eBVAABVAABVAABVAABVDATQEAxE1KBkIBFEABFEABFEABFEABFNhIAQBkI4X49yiAAiiAAiiAAiiAAiiAAm4KACBuUvoMdOmll17qMxKjoAAKoAAKoAAKoAAK5FBgp512ooZOEBbxEsTLcSoAkkNVxkQBFEABFEABFEABPwUAkDQtAZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPBkDcJWVAFEABFEABFEABFHBVAABJkxMASdPP/WwAxF1SBkQBFEABFEABFEABVwUAkDQ5AZA0/dzPLg0g9//dN7qvgQHrUODNf/6AkImQqRDZs79pVJ6yL4w3QAEUQIFtKACAbEO0uVMAkDT93M8GQNwlHXbAqIIRAOkzclF56lNNVoUCKNC6AgBImoMASJp+7mcDIO6SDjtgVMEIgPQZuag89akmq0IBFGhdAQAkzUEAJE0/97MBEHdJhx0wqmAEQPqMXFSe+lSTVaEACrSuAACS5iAAkqaf+9kAiLukww4YVTACIH1GLipP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" width="640">



```python

```
