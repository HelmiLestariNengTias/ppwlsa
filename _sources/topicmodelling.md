# Topic Modelling -LSA(Latent Semantic Analysis)

Topic modelling adalah teknik unsupervised learning tanpa pengawasan yang mampu memindai satu set dokumen, mendeteksi pola kata dan frasa didalamnya secara otomatis mengelompokkan grub kata dan ekspresi serupa

## LSA (Latent Semantic Analysis)

LSA merupakan salah satu cara untuk mengektraksi tulisan dan membuat representasi statistik dari dokument teks. 

## Tahapan LSA

Pada tahapan LSA meliputi:

### Menghitung TF-IDF

Tf-IDF dihitung menggunakan matrix mxn. m disini adalah dokumen, untuk m adalah fitur kata pada dokumen. 

Perhitungan TF-IDF dapat dilakukan dengan rumus sebagai berikut:
$$
W_{i,j}=tfi,j\times \log \dfrac{N}{df^{j}}
$$
Wij= Score TF-IDF

tfi,j= term dari dokumen

N=Total dokumen

Df j= Dokumen 

```python
import pandas as pd
import numpy as np
#Import Library untuk Tokenisasi
import string 
import re #regex library

# import word_tokenize & FreqDist dari NLTK
from nltk.tokenize import word_tokenize 
from nltk.probability import FreqDist
from nltk.corpus import stopwords

from sklearn.feature_extraction.text import CountVectorizer
from sklearn.feature_extraction.text import TfidfVectorizer
```

```python
vect =TfidfVectorizer(stop_words=list_stopwords,max_features=1000) 
vect_text=vect.fit_transform(dataPTA['Abstrak'])
print(vect_text.shape)
print(vect_text)
```

### Menampilkan SVD

SVD adalah suatu pemfaktoran matriks dengan mengurai suatu matriks kedalam dua matriks . Singular value decomposition dari matrix A dinyatakan sebagai : A= USV'

Untuk matriks U: Baris mewakili vektor dokumen pada topik

Untuk matriks V: Baris mewakili vector istilah yang dinyatakan dalam topik

Untuk Matriks S: matriks diagonal yang memiliki elemen- elemen diagonal sebagai nilai singular dari A.

Implementasi SVD pada python

```python
from sklearn.decomposition import TruncatedSVD
lsa_model = TruncatedSVD(n_components=10, algorithm='randomized', n_iter=10, random_state=42)

lsa_top=lsa_model.fit_transform(vect_text)
```

```python
l=lsa_top[0]
print("Document 0 :")
for i,topic in enumerate(l):
  print("Topic ",i," : ",topic*100)
```

```python
print(lsa_model.components_.shape) # (no_of_topics*no_of_words)
print(lsa_model.components_)
```

### Mengekstrak Topic dan Term

Untuk percobaan kali ini kita melakukan percobaan extrak sebanyak 10 topik.

```python
# most important words for each topic
vocab = vect.get_feature_names()

for i, comp in enumerate(lsa_model.components_):
    vocab_comp = zip(vocab, comp)
    sorted_words = sorted(vocab_comp, key= lambda x:x[1], reverse=True)[:10]
    print("Topic "+str(i)+": ")
    for t in sorted_words:
        print(t[0],end=" ")
    print("\n")
```

