# Topic Modelling -LSA(Latent Semantic Analysis)

Topic modelling adalah teknik unsupervised learning tanpa pengawasan yang mampu memindai satu set dokumen, mendeteksi pola kata dan frasa didalamnya secara otomatis mengelompokkan grub kata dan ekspresi serupa

## LSA (Latent Semantic Analysis)

Latent Semantic Analysis (LSA) merupakan sebuah metode yang memanfaatkan model statistik matematis untuk menganalisa struktur semantik suatu teks. LSA bisa digunakan untuk menilai esai dengan mengkonversikan esai menjadi matriks-matriks yang diberi nilai pada masing-masing term untuk dicari kesamaan dengan term referensi. Secara umum, langkah-langkah LSA dalam penilaian esai adalah sebagai berikut:

1. Text preprocessing
2. Stopword removal
3. Stemming

Untuk Preprocessing nya saya sudah menjelaskan di bab preprocessing jadi disini saya  langsung masuk di tahapan LSA

## Tahapan LSA

### Menampilkan SVD

Singular Value Decomposition (SVD) adalah salah satu teknik reduksi dimensi yang bermanfaat untuk memperkecil nilai kompleksitas dalam pemrosesan term-document matrix. SVD merupakan teorema aljabar linier yang menyebutkan bahwa persegi panjang dari term-document matrix dapat dipecah/didekomposisikan menjadi tiga matriks, yaitu :

– Matriks ortogonal U

– Matriks diagonal S

– Transpose dari matriks ortogonal V

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

