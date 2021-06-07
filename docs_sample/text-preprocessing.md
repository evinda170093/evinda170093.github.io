![preprocessing.png](https://miro.medium.com/max/800/1*KljmJybQDj1mJAL-oS8VWQ.png)

## Apa itu Preprocessing?

Preprocessing atau Text Preprocessing merupakan tahap lanjutan dari crawling data. Text preprocessing merupakan tahap untuk melakukan seleksi data sebelum dilakukan pemrosesan data dan diubah menjadi data yang lebih tertsruktur.

Terdapat beberapa langkah dalam Text Preprocessing :
1. Tokenizing
2. Remove Stop Word
3. Stemming
4. Matrix TF-IDF

## Library yang dibutuhkan


```python
import numpy as np
import sys
from IPython.display import Image
import matplotlib.pyplot as plt
%matplotlib inline
import networkx as nx
from nltk.tokenize.punkt import PunktSentenceTokenizer
from sklearn.feature_extraction.text import TfidfTransformer, CountVectorizer
import string 
from Sastrawi.Stemmer.StemmerFactory import StemmerFactory# create stemmer
import re # impor modul regular expressionkalimat = "Berikut ini adalah 5 negara dengan pendidikan terbaik di dunia adalah Korea Selatan, Jepang, Singapura, Hong Kong, dan Finlandia."
from Sastrawi.StopWordRemover.StopWordRemoverFactory import StopWordRemoverFactory
from nltk.tokenize import word_tokenize
import pandas as pd
from sklearn.feature_extraction.text import TfidfTransformer
```

## Tokenizing

Proses tokenizing atau pemisahan string merupakan proses untuk mengubah teks menjadi daftar token. Kita dapat menganggap token sebagai bagian seperti kata adalah tanda dalam kalimat dan kalimat adalah tanda dalam paragraf. Atau lebih mudah dipahami bahwa tokenizing dalah memisahkan kalimat dalam paragraf.

Dalam contohnya tokenizing penggunakan function PunktSentenceTokenizer() dari module nltk.tokenize.punkt untuk menandai akhir dan awal kalimat, apakah terdapat karakter dan tanda baca. re merupakan module regular expressions untuk menghapus substring pada string. (r"\d+", "",i) berarti merubah "\d+" menjadi string kosong. Kemudian pada perulangan kedua menghapus enter dan menggantinya dengan string kosong untuk menggabungkan setiap kalimat yang ada.




```python
# TOKENIZING
doc_tokenizer = PunktSentenceTokenizer()
sentences_list = doc_tokenizer.tokenize(document)
dokumenre=[]
for i in sentences_list:
    hasil = re.sub(r"\d+", "", i)
    dokumenre.append(hasil) 
dokumen=[]
for i in dokumenre:
    hasil =  i.replace('\n','') 
    dokumen.append(hasil)
print(dokumen)
```

## Remove Stop Words

Remove stop words merupakah salah satu langkah yang paling penting dalam text preprocessing. Dalam remove stop words terdapat proses untuk menyaring dan menghilangkan data atau kata yang tidak berguna. Dan dalam Natural Language Processing (NLP) kata-kata atau data yang tidak berguna tersebut dikenal dengan stopwords.

Dalam stopwords terdiri dari kata-kata seperti kata penghubung (dan, atau, dari, di, dsb).

Untuk proses remove stopwords menggunakan function StopWordRemoverFactory() dari module Sastrawi.StopWordRemover.StopWordRemoverFactory. Pertama kita membuat perulangan untuk menghapus kata-kata yang tidak penting pada setiap kalimat. Setalah dilakukan proses remove stopwords kita perlu memastikan bahwa setiap kata tidak berisikan tanda baca dengan menggunakan translate(str.maketrans("","",string.punctuation))


```python
factory = StopWordRemoverFactory()
stopword = factory.create_stop_word_remover()
a=len(dokumen)
dokumenstop=[]
for i in range(0, a):
    sentence = stopword.remove(dokumen[i])
    dokumenstop.append(sentence)
print(dokumenstop)
dokumenstopall=[]
for i in dokumenstop:
    output = i.translate(str.maketrans("","",string.punctuation))
    dokumenstopall.append(output)
print("Final ", dokumenstopall)
```

## Stemming

Proses stemming adalah proses untuk menghasilkan kata dasar dari sebuah kalimat yang terdapat imbuhan. Contohnya : mengambil menjadi ambil, diproses menjadi proses dan sebagainya.

Proses stemming menggunakan function StemmerFactory() dari module Sastrawi.Stemmer.StemmerFactory


```python
# STEMMING
factory = StemmerFactory()
stemmer = factory.create_stemmer()
dokumenstem=[]
for i in dokumenstopall:
    output = stemmer.stem(i)
    dokumenstem.append(output)
print(dokumenstem)
```

## Get TF-IDF

TF-IDF (Term Frequency-Inverse Document Frequency) adalah ukuran statistik yang mengvaluasi seberapa relevan sebuah kata dengan dokumen dalam kumpulan dokumen. TF-IDF diciptakan untuk pencarian dokumen dan temu kembali informasi.

TF-IDF dihitung dengan mengalikan dua metrik yang berbeda :
1. Term Frequency (TF) : adalah frekuensi sebuah kata dalam sebuah dokumen. Cara paling sederhana adalah dengan menghitung jumlah awal kata yang muncul dalam dokumen.
2. Inverse Document Frequency (IDF) : adalah frekuensi kata di satu set dokumen. Yang berarti seberapa umum atau jarang sebuah kata di seluruh kumpulan dokumen. Semakin dekat ke 0, semakin umum sebuah kata. Metrik ini dapat dihitung dengan mengambil jumlah total dokumen, membaginya dengan jumlah dokumen yang berisi kata dan menghitung logaritma.

Jika kata tersebut sangat umum dan muncul di banyak dokumen, maka hasil akan mendekati 0, jika tidak maka akan mendekati 1. Dan berikut adalah code untuk TF-IDF :


```python
count = CountVectorizer()
bag = count.fit_transform(dokumenstem)
matrik_vsm=bag.toarray()
a=count.get_feature_names()
dfb =pd.DataFrame(data=matrik_vsm,index=list(range(1, len(matrik_vsm[:,1])+1, )),columns=[a])
dfb

tfidf = TfidfTransformer(use_idf=True,norm='l2',smooth_idf=True)
tf=tfidf.fit_transform(count.fit_transform(dokumenstem)).toarray()
print(tf.shape)
get_tfidf = pd.DataFrame(data=tf,index=list(range(1, len(tf[:,1])+1, )),columns=[a])
get_tfidf
```