# Crawling Data

Crawling adalah sebuah proses di mana mesin pencarian seperti Google dapat mencari dan memindai konten yang berada di situs web berupa artikel, lembar produk, gambar, link, dll.

Salah satu framework pada python yang digunakan untuk crawling data yaitu scrapy. scrapy digunakan untuk mengekstrak, menyimpan data dari sebuah website.

## Instalasi scrapy

Selanjutnya untuk menginstall scrapy dapat dilakukan pada command promt dengan code berikut

```python
pip install scrapy
```



## Membuat File Scrapy

Dalam pembuatan file scrapy baru dapat dilakukan dengan kode dibawah ini  

```python
scrapy startproject namaproject
```




## Menjalankan Spyder baru 

yang pertama yaitu masuk ke dalam projecscrapy kemudian membuat spider baru

```python
cd nama project
```

setelah itu menuliskan command yang berisi nama file yang nantinya kita buat dan juga alamat website yang akan diambil datanya 

```python
scrapy genspider example example.com
```

## Menulis Program Scapper

Setelah kita membuat file pada spider, maka pada file tersebut telah tersedia file dengan nama file yang telah kita buat pada pembuatan spider. 

Pada file tadi telah tersedia code untuk kita melakukan scraping yang nanti bisa kita ubah sesuai yang kita inginkan. 

```python
import scrapy 
import pandas as pd



class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        
        dataCSV = pd.read_csv('ptamanajemen.csv')
        indexData = dataCSV.iloc[:, [0]].values
        arrayData = []
        for i in indexData:
            ambil = i[0]
            arrayData.append(ambil)
        for url in arrayData:
            yield scrapy.Request(url=url, callback=self.parse)

    def parse(self,response):
        yield {
            'judul':response.css('#content_journal > ul > li > div:nth-child(2) > a::text').extract(),
            'penulis':response.css('#content_journal > ul > li > div:nth-child(2) > div:nth-child(2)> span::text').extract(),
            'dosen_1':response.css('#content_journal > ul > li > div:nth-child(2) > div:nth-child(3)> span::text').extract(),
            'dosen_2':response.css('#content_journal > ul > li > div:nth-child(2) > div:nth-child(4)> span::text').extract(),
            'abstrak_ID':response.css('#content_journal > ul > li > div:nth-child(4) > div:nth-child(2) > p::text').extract(),
            'abstrak_EN':response.css('#content_journal > ul > li > div:nth-child(4) > div:nth-child(4) > p::text').extract(),
        
        }

```

Untuk class scrapPTA() gunanya untuk spidering website.

Class parse gunanya untuk melakukan follow link

## Menjalankan File Spider

Pada proses ini kita masuk kedalam direktori spider terlebih dahulu, lalu running spider menggunakan command

```python
scrapy runspider namafile.py
```

## Menyimpan Data Kedalam Excel

Kita dapat menyimpan data yang telah kita ambil dari suatu web dengan cara:

```python
scrapy crawl namafile -O namafileyangdiinginkan.xlsx
```

