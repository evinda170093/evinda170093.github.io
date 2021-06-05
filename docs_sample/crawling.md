![crawling.png](https://blog.apify.com/content/images/max/1132/1-YfeP5WFbn0MwI76kuTM38w.png)

## Apa itu Web Crawling?

Web scraping sering disebut web crawling atau web spidering atau "secara terprogram menelusuri kumpulan halaman web dan mengekstraksi data," adalah alat yang ampuh untuk bekerja dengan data di web.

Namun web crawling dan web scraping adalah dua konsep yang berbeda tetapi terkait. Web crawling adalah komponen scraping web, logika crawling menemukan URL untuk diproses oleh kode scraping.

Web crawling dimulai dengan daftar URL untuk dikunjungi, yang disebut seed. Untuk setiap URL, crawling menemukan tautan dalam HTML, memfilter tautan tersebut berdasarkan beberapa kriteria dan menambahkan tautan baru ke antrean. Semua HTML atau beberapa informasi spesifik diekstraksi untuk diproses oleh saluran yang berbeda.

Dengan web scraping, kita dapat menambang data tentang sekumpulan produk, mendapatkan kumpulan teks atau data kuantitatif yang besar untuk dimainkan, mendapatkan data dari situs tanpa API resmi, atau hanya memuaskan rasa ingin tahu pribadi.

Dalam tutorial ini, Anda akan belajar tentang dasar-dasar proses crawling saat menjelajahi kumpulan data

## Strategi web crawling

Dalam praktiknya, web crawling hanya mengunjungi sebagian halaman bergantung pada crawler budget, yang dapat berupa jumlah halaman maksimum per domain, kedalaman (depth), atau waktu eksekusi (execution time).

Situs web paling populer menyediakan file robots.txt untuk menunjukkan area situs web mana yang tidak boleh dirayapi oleh setiap agen pengguna. Kebalikan dari file robots adalah file sitemap.xml, yang berisi daftar halaman yang dapat di crawling.

Kasus penggunaan web crawler yang populer meliputi:

- Search engines (Googlebot, Bingbot, Yandex Bot…) mengumpulkan semua HTML untuk bagian penting dari Web. Data ini diindeks untuk membuatnya dapat dicari.
- Alat analisis SEO selain mengumpulkan HTML juga mengumpulkan metadata seperti waktu respons (response time), status respons (response status) untuk mendeteksi halaman yang rusak, dan tautan antara domain yang berbeda untuk mengumpulkan tautan balik.
- Alat pemantau harga crawl e-commerce websites untuk menemukan halaman produk dan mengekstrak metadata, terutama harganya. Halaman produk kemudian ditinjau kembali secara berkala.
- Common Crawl memelihara repositori terbuka dari data perayapan web . Misalnya, arsip dari Oktober 2020 berisi 2,71 miliar halaman web.

Selanjutnya, kita akan membandingkan tiga strategi berbeda untuk membangun web crawler dengan Python. Pertama, hanya menggunakan library standar, lalu library pihak ketiga untuk membuat permintaan HTTP dan mem-parsing HTML, dan terakhir, kerangka kerja perayapan web

## Membangun web crawler sederhana dengan Python dari awal

Untuk membangun web crawler sederhana dengan Python, kita memerlukan setidaknya satu library untuk mengunduh HTML dari URL dan library parsing HTML untuk mengekstrak links. Python menyediakan urllib library standar untuk membuat permintaan HTTP dan html.parser untuk mem -parsing HTML. Contoh crawler Python yang dibuat hanya dengan library standar dapat ditemukan di Github.

Library Python standar untuk request dan patsing HTML tidak terlalu ramah pengembang. Libary populer lainnya seperti request , dicap sebagai HTTP untuk manusia, dan Beautiful Soup memberikan pengalaman pengembang yang lebih baik. Kita dapat menginstal dua library secara lokal.

Disini kita akan melakukan crawling berita pada website CNN Indonesia


```python
pip install requests bs4
```

Crawler dasar dapat dibangun mengikuti diagram arsitektur sebelumnya.

## Library yang dibutuhkan


```python
import requests
from bs4 import BeautifulSoup
import numpy as np
import pandas as pd
import re
import time
```

## Function get news 

Untuk mendapatkan data berita dari website yang akan kita crawling, buat sebuah function untuk melakukan web crawler.


```python
def get_news_cnn():
    # url definition
    url = "https://www.cnnindonesia.com/teknologi/indeks/8"
    # Request
    r1 = requests.get(url)
    r1.status_code
    # We'll save in coverpage the cover page content
    coverpage = r1.content
    # Soup creation
    soup1 = BeautifulSoup(coverpage, 'html5lib')
    # News identification
    coverpage_news = soup1.find_all('div', class_='list media_rows middle')
    for arti_i in coverpage_news:
        arti = arti_i.find_all('article')
        #print(len(arti))
    number_of_articles = 3
    
    # Empty lists for content, links and titles
    news_contents = []
    list_links = []
    list_titles = []
    for n in np.arange(0, number_of_articles):
        # Getting the link of the article
        link = arti[n].find('a')['href']
        list_links.append(link)
        
        # Getting the title
        title = arti[n].find('h2').get_text()
        list_titles.append(title)
        
        # Reading the content (it is divided in paragraphs)
        article = requests.get(link)
        article_content = article.content
        soup_article = BeautifulSoup(article_content, 'html5lib')
        body = soup_article.find_all('div', class_='detail_text')
        
        # Unifying the paragraphs
        list_paragraphs = []
        final_article = []
        for pg in body:
            get_paragraph = pg.find_all('p')
            #print(get_paragraph)
            #print(len(get_paragraph))
            
        for p in np.arange(0, len(get_paragraph)):
            paragraph = get_paragraph[p].get_text()
            clean_paragraph = clean(paragraph)
            list_paragraphs.append(clean_paragraph)
            final_article = " ".join(list_paragraphs)
        
        # Removing special characters
        final_article = re.sub("\\xa0", "", final_article)
        news_contents.append(final_article)
```

Kode di atas mendefinisikan kelas Crawler dengan hepler method untuk mendownload_url menggunakan library request, get_linked_urls menggunakan library Beautiful Soup. Kita dapat menjalankan crawler di terminal.

Salah satu metode HTTP yang paling umum adalah GET. The GETmetode menunjukkan bahwa Anda sedang berusaha untuk mendapatkan atau mengambil data dari sumber tertentu. Untuk membuat GETpermintaan, panggil requests.get().

Untuk mengujinya, kita dapat membuat GETrequest ke alamat website CNN dengan memanggil get() URL berikut:


```python
url = "https://www.cnnindonesia.com/teknologi/indeks/8"
r1 = requests.get(url)
```

Informasi pertama yang dapat kita kumpulkan adalah status code. Kode status memberi tahu kita tentang status permintaan.

Misalnya, 200 OK status berarti permintaan Anda berhasil, sedangkan 404 NOT FOUNDstatus berarti sumber daya yang kita cari tidak ditemukan.
Dengan mengakses .status_code, kita dapat melihat kode status yang dikembalikan server:


```python
r1.status_code
```

Untuk mendapatkan konten dari website kita perlu menyimpan body html yang kita crawling dengan .content :


```python
coverpage = r1.content
```

Hal yang sangat menyenangkan tentang library BeautifulSoup adalah ia dibangun di atas library parsing HTML seperti html5lib, lxml, html.parser, dll. Jadi objek BeautifulSoup dan menentukan library parser dapat dibuat pada saat yang bersamaan.


```python
soup1 = BeautifulSoup(coverpage, 'html5lib')
```

kita membuat objek Beautiful Soup dengan melewatkan dua argumen

1. page.content (coverpage): Ini adalah konten html asli
2. html5lib: Menentukan parser html yang ingin kita gunakan

Untuk mencari seluruh tag < div > dengan class list media_rows middle dimana didalamnya terdapat beberapa kumpulan berita yang terdapat pada tag < article > dengan batasan untuk 3 berita saja, num_of_articles = 3


```python
coverpage_news = soup1.find_all('div', class_='list media_rows middle')
    for arti_i in coverpage_news:
        arti = arti_i.find_all('article')
        #print(len(arti))
    number_of_articles = 3
```

Untuk mendapatkan seluruh paragraf atau kalimat yang terdapat pada berita maka digunakan code dibawah ini


```python
news_contents = []
list_links = []
list_titles = []
for n in np.arange(0, number_of_articles):
    link = arti[n].find('a')['href']
    list_links.append(link)
        
    title = arti[n].find('h2').get_text()
    list_titles.append(title)
        
    article = requests.get(link)
    article_content = article.content
    soup_article = BeautifulSoup(article_content, 'html5lib')
    body = soup_article.find_all('div', class_='detail_text')
        
    list_paragraphs = []
    final_article = []
    for pg in body:
        get_paragraph = pg.find_all('p')
```

Dimana yang akan diambil adalah link, title dan isi berita, link diambil dari tag < a > dan title diambil dari tag < h2 > dan untuk isi berita diambil dari tag < p > 

## Function Clean

Code diatas menghasilkan text dengan tulisan \n dan \t yang dika diartikan adalah tab dan enter dimana hal tersebut dihilangkan dengan code berikut :


```python
def clean(string):
        string = string.replace("\n", " ")
        string = string.replace("\t", " ")
        string = string.strip()
        #string =  re.sub(r"[^a-zA-Z0-9]+", ' ', string)
        string = string.strip()
        arrString = string.split(" ")
        return string.strip()
```

Dan digunakan atau dipanggil pada code dibawah ini untuk menghilangkan tab dan enter pada setiap paragraf dari berita


```python
for p in np.arange(0, len(get_paragraph)):
    paragraph = get_paragraph[p].get_text()
    clean_paragraph = clean(paragraph)
    list_paragraphs.append(clean_paragraph)
    final_article = " ".join(list_paragraphs)
```

Terkadang text yang diambil dari hasil crawler tidak begitu bagus, masih terdapat banyak sekali simbol atau special character yang tidak dibutuhkan sehingga disini perlu adanya proses cleansing untuk menghilangkan special character yang mengganggu text.


```python
final_article = re.sub("\\xa0", "", final_article)
news_contents.append(final_article)
```