
# ✈️ Airline Passenger Satisfaction Analysis

Bu proje, bir havayolu şirketine ait müşteri verilerini kullanarak veri temizleme, aykırı değer analizi, görselleştirme ve bazı temel istatistiksel çıkarımlar yapmayı amaçlamaktadır. 

---

## Kullanılan Kütüphaneler

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

- `pandas`: Veri okuma ve manipülasyonu  
- `numpy`: Sayısal işlemler  
- `matplotlib`, `seaborn`: Görselleştirme

--

### 1. Veriyi Yükleme

df = pd.read_csv("train.csv")
---

### 2.İlk Gözlemler

 
df.head()
df.shape
print(df.columns)
df.info()
df.describe()
```

- `head()`: İlk 5 satırı gösterir.  
- `shape`: Satır ve sütun sayısını döner.  
- `columns`: Sütun isimlerini listeler.  
- `info()`: Veri türleri ve eksik veri bilgileri.  
- `describe()`: Sayısal sütunlar için istatistiksel özet verir.

---

### 3. Değişken Türlerini Ayırma

 
from pandas.api.types import is_numeric_dtype, is_object_dtype

numeric_columns = [col for col in df.columns if is_numeric_dtype(df[col])]
categorical_columns = [col for col in df.columns if is_object_dtype(df[col])]
```

- Sayısal (`int`, `float`) ve kategorik (`object`) sütunlar ayrıştırılır.

---

### 4. Eksik Değer Analizi

 
df.isnull().sum()
```

- Her sütunda kaç eksik değer bulunduğu hesaplanır.

---

### 5. Aykırı Değer Analizi

 
def outlier_summary(df, numeric_columns):
    for col in numeric_columns:
        Q1 = df[col].quantile(0.25)
        Q3 = df[col].quantile(0.75)
        IQR = Q3 - Q1
        lower = Q1 - 1.5 * IQR
        upper = Q3 + 1.5 * IQR
        outliers = df[(df[col] < lower) | (df[col] > upper)]
        print(f"{col}: {len(outliers)} aykırı değer")

numeric_columns = df.select_dtypes(include=[np.number]).columns
outlier_summary(df, numeric_columns)
```

- IQR (Interquartile Range) yöntemi ile aykırı değer sayısı hesaplanır.

---

### 6. Boxplot Görselleştirme (Aykırı Değerler)

 
for col in ["Flight Distance", "Checkin service", "Departure Delay in Minutes", "Arrival Delay in Minutes"]:
    plt.figure(figsize=(10, 4))
    sns.boxplot(x=df[col])
    plt.title(f"{col} - Box Plot")
    plt.show()
```

- Belirli sütunlar için boxplot grafik çizilir.

---

### 7. Aykırı Değer Temizleme

 
def replace_outliers_with_mean(df, column):
    Q1 = df[column].quantile(0.25)
    Q3 = df[column].quantile(0.75)
    IQR = Q3 - Q1
    lower = Q1 - 1.5 * IQR
    upper = Q3 + 1.5 * IQR

    mean_value = df[column].mean()
    mean_value = int(mean_value)
    df.loc[(df[column] < lower) | (df[column] > upper), column] = mean_value

replace_outliers_with_mean(df, "Departure Delay in Minutes")
replace_outliers_with_mean(df, "Arrival Delay in Minutes")
```

- Aykırı değerler, ilgili sütunun ortalaması ile değiştirilir.

---

### 8. Temizleme Sonrası Boxplot

 
plt.figure(figsize=(12, 6))

plt.subplot(1, 2, 1)
sns.boxplot(data=df, y="Departure Delay in Minutes")
plt.title("Departure Delay in Minutes")

plt.subplot(1, 2, 2)
sns.boxplot(data=df, y="Arrival Delay in Minutes")
plt.title("Arrival Delay in Minutes")

plt.show()
```

---

### 9. Kategorik Veri Analizi

 
print(df["Checkin service"].value_counts())

sns.countplot(x="Checkin service", data=df)
plt.title("Checkin Service Dağılımı")
plt.show()
```

- Check-in hizmeti derecelendirmesi dağılımı görselleştirilir.

---

### 10. Benzersiz Değer İnceleme

 
df["Age"].unique()
df["Customer Type"].unique()
df["satisfaction"].unique()
df["Type of Travel"].unique()
```

- Seçilen sütunlardaki benzersiz değerler listelenir.

---

### 11.Memnuniyet Yüzdesi

 
satisfaction_counts = df["satisfaction"].value_counts(normalize=True) * 100
print("Memnuniyet yüzdeleri:
", satisfaction_counts)
```

- `satisfaction` sütunundaki oranlar yüzde olarak hesaplanır.


 
satisfaction_counts = df["satisfaction"].value_counts()
plt.figure(figsize=(8, 6))
plt.pie(satisfaction_counts, labels=satisfaction_counts.index, autopct='%1.1f%%', startangle=140)
plt.title("Yolcu Memnuniyet Yüzdeleri")
plt.show()
```

- Memnun vs. memnun olmayan yolcuların yüzdesi görselleştirilir.


satisfied_age_mean = df[df["satisfaction"] == "satisfied"]["Age"].mean()
print("Memnun yolcuların yaş ortalaması:", round(satisfied_age_mean, 2))
```

- Sadece memnun olan yolcuların yaş ortalaması hesaplanır.

satisfied_loyal = df[df["satisfaction"] == "satisfied"]["Customer Type"].value_counts(normalize=True) * 100
print("Memnun yolcularda loyal/disloyal oranı:
", satisfied_loyal)
```

- Sadık/sadık olmayan müşteri oranları hesaplanır (yalnızca memnun yolcular için).


disloyal_travel_type = df[df["Customer Type"] == "disloyal Customer"]["Type of Travel"].value_counts(normalize=True) * 100
print("Disloyal yolcuların seyahat amacı:
", disloyal_travel_type)
```

- Disloyal müşterilerin seyahat amacı yüzdesel olarak verilir.

---

Loyal_travel_type = df[df["Customer Type"] == "Loyal Customer"]["Type of Travel"].value_counts(normalize=True) * 100
print("Loyal yolcuların seyahat amacı:
", Loyal_travel_type)
```

- Loyal müşterilerin seyahat amacı yüzdesel olarak verilir.

---

## SONUÇ

Bu proje kapsamında:
- Veriler incelendi ve aykırı değerler analiz edildi.
- Görselleştirme ile dağılımlar keşfedildi.
- Müşteri tipi, yaş ve seyahat türü ile memnuniyet arasında ilişkiler değerlendirildi.
