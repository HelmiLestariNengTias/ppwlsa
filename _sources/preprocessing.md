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

Sebelum melakukan prepocessing disini kita akan membaca data dari data PTA .trunojoyo.ac.id

```python
dataPTA = pd.read_excel('hasilmanajemen.xlsx')
```

```python
dataPTA.head(10)
```



## Case Folding

Tahapan pertama yang biasanya dilakukan adalah tahapan case folding. Tahapan ini hampir selalu disertakan ketika melakukan text preprocessing. Mengapa? Karena data yang kita miliki tidak selalu terstruktur dan konsisten dalam penggunaan huruf kapital. Jadi, peran dari case folding adalah untuk menyamaratakan penggunaan huruf kapital. 

Misalnya data teks yang kita dapat berupa tulisan "DaTA SCIence" maka dengan case folding artinya kita mengubah semua huruf menjadi huruf kecil (lowercase) semua. Sementara itu, karakter lain yang bukan termasuk huruf dan angka, seperti tanda baca dan spasi dianggap sebagai delimiter. Delimiter ini bisa juga dihapus atau diabaikan dengan menggunakan perintah yang ada di Python.

```python

# gunakan fungsi Series.str.lower() pada Pandas
dataPTA.abstrak = dataPTA.abstrak.astype(str)
dataPTA['abstrak'] = dataPTA['abstrak'].str.lower()

print('Case Folding Result : \n')

#cek hasil case fold
print(dataPTA['abstrak'].head(5))
print('\n\n\n')
```

## Removal

Removal yaitu menghilangkan atau pembuangan kata atau karakter yang tidak dibutuhkan. 

```python
#Import Library untuk Tokenisasi
import string 
import re #regex library

# import word_tokenize & FreqDist dari NLTK
from nltk.tokenize import word_tokenize 
from nltk.probability import FreqDist

def remove_PTA_special(text):
    # menghapus tab, new line, dan back slice
    text = text.replace('\\t'," ").replace('\\n'," ").replace('\\u'," ").replace('\\',"")
    # menghapus non ASCII (emoticon, chinese word, .etc)
    text = text.encode('ascii', 'replace').decode('ascii')
    # menghapus mention, link, hashtag
    text = ' '.join(re.sub("([@#][A-Za-z0-9]+)|(\w+:\/\/\S+)"," ", text).split())
    # menghapus incomplete URL
    return text.replace("http://", " ").replace("https://", " ")
                
dataPTA['abstrak'] = dataPTA['abstrak'].apply(remove_PTA_special)

#menghapus nomor
def remove_number(text):
    return  re.sub(r"\d+", "", text)

dataPTA['abstrak'] = dataPTA['abstrak'].apply(remove_number)

#menghapus punctuation
def remove_punctuation(text):
    return text.translate(str.maketrans("","",string.punctuation))

dataPTA['abstrak'] = dataPTA['abstrak'].apply(remove_punctuation)

#menghapus spasi leading & trailing
def remove_whitespace_LT(text):
    return text.strip()

dataPTA['abstrak'] = dataPTA['abstrak'].apply(remove_whitespace_LT)

#menghapus spasi tunggal dan ganda
def remove_whitespace_multiple(text):
    return re.sub('\s+',' ',text)

dataPTA['abstrak'] = dataPTA['abstrak'].apply(remove_whitespace_multiple)

# menghapus kata 1 abjad
def remove_singl_char(text):
    return re.sub(r"\b[a-zA-Z]\b", "", text)

dataPTA['abstrak'] = dataPTA['abstrak'].apply(remove_singl_char)

# Tokenisasi
def word_tokenize_wrapper(text):
    return word_tokenize(text)

dataPTA['abstrak_token'] = dataPTA['abstrak'].apply(word_tokenize_wrapper)

print('Tokenizing Result : \n') 
print(dataPTA['abstrak_token'].head())
```



## Tokenizing

Kita ambil contoh adalah data tweet atau kumpulan dataset pesan spam pasti terdiri dari kalimat. Nah, untuk memudahkan proses analisis data kita harus memecah kalimat-kalimat tersebut menjadi kata atau disebut dengan token. Dengan tokenizing kita dapat membedakan mana antara pemisah kata atau bukan. Jika menggunakan bahasa pemrograman python biasanya tokenizing juga mencakup proses removing number, removing punctuation seperti simbol dan tanda baca yang tidak penting, serta removing whitespace. Selain itu tokenizing juga akan merujuk pada NLTK, tetapi yang sangat disayangkan adalah NLTK belum support bahasa Indonesia. Tapi, jangan khawatir karena kita masih bisa menggunakan modul sastrawi.

## Stopword  

Tujuan utama dalam penerapan proses stopwords ini adalah mengurangi jumlah kata dalam sebuah dokumen yang nantinya akan berpengaruh dalam kecepatan dan performa NLP.

Karakteristik utama dalam pemilihan stopwords biasanya adalah kata yang mempunyai frekuensi kemunculan yang tinggi misalnya kata penghubung seperti “dan”, “atau”, “tapi”, “akan” dan lainnya.

```python
from nltk.corpus import stopwords

list_stopwords = stopwords.words('indonesian')

# Mengubah List ke dictionary
list_stopwords = set(list_stopwords)


#remove stopword pada list token
def stopwords_removal(words):
    return [word for word in words if word not in list_stopwords]

#aStopwording
dataPTA['abstrak_stop'] = dataPTA['abstrak_token'].apply(stopwords_removal) 


print(dataPTA['abstrak_stop'].head(20))
```

```python
dataPTA.head()
```



## Stemming

Tahap stemming adalah tahapan yang juga diperlukan untuk memperkecil jumlah indeks yang berbeda dari satu data sehingga sebuah kata yang memiliki suffix maupun prefix akan kembali ke bentuk dasarnya. Selain itu juga untuk melakukan pengelompokan kata-kata lain yang memiliki kata dasar dan arti yang serupa namun memiliki bentuk yang berbeda karena mendapatkan imbuhan yang berbeda pula. Di library NLTK juga sudah tersedia modul untuk proses stemming antara lain, porter, lancester, wordnet, dan snowball. Tapi, kembali lagi modul-modul tersebut belum support untuk text berbahasa Indonesia. 

```python
# import Sastrawi package
from Sastrawi.Stemmer.StemmerFactory import StemmerFactory
import swifter


# membuat stemmer
factory = StemmerFactory()
stemmer = factory.create_stemmer()

# sfungsi stemmer
def stemmed_wrapper(term):
    return stemmer.stem(term)

term_dict = {}

for document in dataPTA['abstrak_stop']:
    for term in document:
        if term not in term_dict:
            term_dict[term] = ' '
            
print(len(term_dict))
print("------------------------")

for term in term_dict:
    term_dict[term] = stemmed_wrapper(term)
    print(term,":" ,term_dict[term])
    
print(term_dict)
print("------------------------")


# stemming pada dataframe
def get_stemmed_term(document):
    return [term_dict[term] for term in document]

dataPTA['abstrak_stem'] = dataPTA['abstrak_stop'].swifter.apply(get_stemmed_term)
print(dataPTA['abstrak_stem'])
```

```python
dataPTA.head()
```

## TF-IDF

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
vectorizer = TfidfVectorizer(stop_words='english')
berita = []
for data in dataPTA['abstrak_stem']:
    isi = ''
    for term in data:
        isi += term + ' '
    berita.append(isi)

vectorizer.fit(berita)
X = vectorizer.fit_transform(berita)
print (X)
```

```python
vect =TfidfVectorizer(stop_words=list_stopwords,max_features=1000) 
vect_text=vect.fit_transform(dataPTA['abstrak'])
print(vect_text.shape)
print(vect_text)
```

