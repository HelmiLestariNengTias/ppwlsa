# Tekt Processing 

Tekt processing adalah suatu proses untuk menyeleksi data agar menjadi lebih terstruktur, dengan tahapan case folding, tokenisasi, removal, dan stopword

## Import Library 

Sebelum melakukan teks prepocessing kita melakukan import library terlebih dahulu, disini kita menggunakan library pandas, numpy, string, dan regex 

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

## Read Data PTA

Sebelum melakukan prepocessing disini kita akan membaca data dari data PTA 

```
dataPTA = pd.read_excel('PTAscrawl.xlsx')
```

## Case Folding

Case folding adalah tahapan pertama yang biasa dilakukan saat melakukan tekt prepocessing. case folding digunakan untuk menyamaratakan penggunaan huruf kapital dan diubah menjadi huruf kecil.

misalnya: tulisan dari "HanDphOne" maka pada proses case folding akan dirubah menjadi "handphone"

```

# gunakan fungsi Series.str.lower() pada Pandas
dataPTA['Abstrak'] = dataPTA['Abstrak'].str.lower()

print('Case Folding Result : \n')

#cek hasil case fold
print(dataPTA['Abstrak'].head(5))
print('\n\n\n')
```

## Tokenizing

Tahap tokenizing adalah tahap pemotongan kata pada white space atau spasi dan membuang karakter tanda baca.

misalnya: tulisan "dia sangat pintar" maka pada pada proses tokenizing akan menjadi dia, sangat, pintar. 

## Stopword Removal 

Pada tahap ini adalah tahap pemilihan kata- kata yang dianggap penting. misalnya pada penggunaan karakter, kata hubung, nomor dll

```
def remove_PTA_special(text):
    # menghapus tab, new line, dan back slice
    text = text.replace('\\t'," ").replace('\\n'," ").replace('\\u'," ").replace('\\',"")
    # menghapus non ASCII (emoticon, chinese word, .etc)
    text = text.encode('ascii', 'replace').decode('ascii')
    # menghapus mention, link, hashtag
    text = ' '.join(re.sub("([@#][A-Za-z0-9]+)|(\w+:\/\/\S+)"," ", text).split())
    # menghapus incomplete URL
    return text.replace("http://", " ").replace("https://", " ")
                
dataPTA['Abstrak'] = dataPTA['Abstrak'].apply(remove_PTA_special)

#menghapus nomor
def remove_number(text):
    return  re.sub(r"\d+", "", text)

dataPTA['Abstrak'] = dataPTA['Abstrak'].apply(remove_number)

#menghapus punctuation
def remove_punctuation(text):
    return text.translate(str.maketrans("","",string.punctuation))

dataPTA['Abstrak'] = dataPTA['Abstrak'].apply(remove_punctuation)

#menghapus spasi leading & trailing
def remove_whitespace_LT(text):
    return text.strip()

dataPTA['Abstrak'] = dataPTA['Abstrak'].apply(remove_whitespace_LT)

#menghapus spasi tunggal dan ganda
def remove_whitespace_multiple(text):
    return re.sub('\s+',' ',text)

dataPTA['Abstrak'] = dataPTA['Abstrak'].apply(remove_whitespace_multiple)

# menghapus kata 1 abjad
def remove_singl_char(text):
    return re.sub(r"\b[a-zA-Z]\b", "", text)

dataPTA['Abstrak'] = dataPTA['Abstrak'].apply(remove_singl_char)

print('Result : \n') 
print(dataPTA['Abstrak'].head())
print('\n\n\n')
```

## Stemming

Tahap stemming adalah tahapan dimana untuk memperkecil jumlah indeks yang berbeda dari satu data sehingga nantinya akan menjadi dalam bentuk dasar. 