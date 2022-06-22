## Crawling Data

Crawling data merupakan proses pengambilan data yang tersedia secara online, crawling dilakukan untuk extraksi data yang mengacu pada pengumpulan data dari dokumen, berita, artikel, media sosial, dll. 

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

class scrapPTA(scrapy.Spider):
    name = 'PTA'
    allowed_domains = ['pta.trunojoyo.ac.id']
    start_urls = ['https://pta.trunojoyo.ac.id/c_search/byprod/10/'+str(x)+" " for x in range(2,20)]

    def parse(self, response):
        for link in response.css('a.gray.button::attr(href)') :
            yield response.follow(link.get(),callback=self.parse_categories)

    def parse_categories(self, response):
        products = response.css('div#content_journal ul li')
        for product in products:
            yield {
                'judul' : product.css('div a.title::text').get().strip(),
                'penulis' : product.css('div div:nth-child(2) span::text').get().strip(),
                'dosen 1' : product.css('div div:nth-child(3) span::text').get().strip(),
                'dosen 2' : product.css('div div:nth-child(4) span::text').get().strip(),
                'abstrak' : product.css('div div:nth-child(2) p::text').get().strip()
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

