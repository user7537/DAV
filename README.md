# DAV


== Importing 
```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt

from sklearn.datasets import load_iris
from scipy import stats
```

Next up are the operations on dataset.

== Data cleaning + correlation (use seaborn Titanic dataset)

```python
df = sns.load_dataset('titanic')
print(df.head())

print(df.isnull().sum())

df = df.drop_duplicates()

num_cols = df.select_dtypes(include=np.number).columns

Q1 = df[num_cols].quantile(0.25)
Q3 = df[num_cols].quantile(0.75)
IQR = Q3 - Q1

df_clean = df[~((df[num_cols] < (Q1 - 1.5 * IQR)) | 
                (df[num_cols] > (Q3 + 1.5 * IQR))).any(axis=1)]

corr = df_clean.corr(numeric_only=True)

print("Most positive correlation:")
print(corr.unstack().sort_values(ascending=False)[1:6])

print("Most negative correlation:")
print(corr.unstack().sort_values().head())
```
The output of this code is:
#figure(
  image("2.png", width: 80%),
  caption:
  [Output of the code above],
)

== Iris Data Analysis

```python
iris = load_iris(as_frame=True)
df_iris = iris.frame

for col in df_iris.columns[:-1]:
    data = df_iris[col]

    mean = data.mean()
    median = data.median()
    mode = data.mode()[0]
    std = data.std()
    sem = stats.sem(data)

    ci = stats.t.interval(0.95, len(data)-1, loc=mean, scale=sem)

    print(f"\n{col}")
    print("Mean:", mean)
    print("Median:", median)
    print("Mode:", mode)
    print("Std Dev:", std)
    print("Std Error:", sem)
    print("95% CI:", ci)

corr = df_iris.corr()

sns.heatmap(corr, annot=True, cmap='coolwarm')
plt.title("Correlation Heatmap")
plt.show()

cov = np.cov(df_iris['sepal length (cm)'], df_iris['petal length (cm)'])
print("Covariance:\n", cov)

ct = pd.crosstab(df_iris['target'], columns="count")
print(ct)
```

The output of this code is:
#figure(
  image("3.png", width: 80%),
  caption:
  [Output of the code above],
)

== Titanic Visualization

```python
df = sns.load_dataset('titanic')
sns.countplot(data=df, x='sex', hue='survived')
plt.title("Survival Count by Gender")
plt.xlabel("Gender")
plt.ylabel("Count")
plt.legend(title="Survived")
plt.savefig("5.1.png")

plt.scatter(df['age'], df['fare'])
plt.xlabel("Age")
plt.ylabel("Fare")
plt.title("Age vs Fare")
plt.savefig("5.2.png")

sns.kdeplot(df['age'].dropna(), label='Age')
sns.kdeplot(df['fare'].dropna(), label='Fare')
plt.legend()
plt.title("Density Distribution")

plt.savefig("5.3.png")


sns.pairplot(df[['age','fare','pclass','survived']].dropna())

plt.savefig("5.4.png")
sns.pairplot(df[['age','fare','pclass','survived']].dropna())

plt.savefig("5.5.png")
```
#figure(
  image("5.1.png", width: 80%),
  caption:
  [Output of the code above],
)
#figure(
  image("5.2.png", width: 80%),
  caption:
  [Output of the code above],
)
#figure(
  image("5.3.png", width: 80%),
  caption:
  [Output of the code above],
)
#figure(
  image("5.4.png", width: 80%),
  caption:
  [Output of the code above],
)
#figure(
  image("5.5.png", width: 80%),
  caption:
  [Output of the code above],
)

== Titanic Analysis

```python
df = sns.load_dataset('titanic')
sns.countplot(data=df, x='sex', hue='survived')
print(len(df[df['age'] < 30]))
print(df[df['pclass'] == 1]['fare'].sum())
print(df.groupby('pclass')['survived'].sum())

```
#figure(
  image("6.png", width: 80%),
  caption:
  [output of the code above],
)


== New Dataset


```python
tips = sns.load_dataset('tips')
num = tips.select_dtypes(include=np.number).shape[1]
cat = tips.select_dtypes(exclude=np.number).shape[1]

print("Numeric:", num)
print("Categorical:", cat)


corr = tips.corr(numeric_only=True)
print(corr)

tips = tips.drop(columns=['total_bill'])

print(tips.describe())

sns.boxplot(data=tips)
plt.title("Five-number summary visualization")
plt.show()
```
#figure(
  image("7.png", width: 80%),
  caption:
  [output of the code above],
)
