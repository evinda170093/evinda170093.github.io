![modelling.png](https://media.springernature.com/original/springer-static/image/chp%3A10.1007%2F978-3-319-95663-3_6/MediaObjects/418651_1_En_6_Fig3_HTML.png)

## Apa itu Modelling?

Terdapat beberapa topik dalam modelling, diantaranya adalah LSA (Latent Semantic Analysis) dan LDA (Latent Dirichlet Allocation). LSA merupakah sebuah teknik dasar dalam pemodelan text untuk menganalisis hubungan antara sekumpulan dokumen dan istilah atau term yang dikandungnya atau dapat didefinisikan sebagai model untuk mengekstraksi dan mewakili makna penggunaan kontekstual dari kata-kata dan untuk menghitung kesamaan antar kata, kalimat atau seluruh dokumen.

LSA juga memanfaatkan konteks di sekitar kata untuk menangkap konsep tersembunyi yang juga dikenal sebagai topik menggunakan Singular Value Decomposition (SVD)

Berikut adalah code mengenai LSA dan penjelasannya :

## Library yang dibutuhkan


```python
from scipy.linalg import svd
from numpy import dot
```

![SVD.png](https://github.com/evinda170093/evinda170093.github.io/blob/main/images/SVD.png?raw=true)

SDV (Singular Value Decomposition) adalah salah satu teknik dalam matrix factorization atau faktorisasi matriks yang banyak digunakan untuk menguraikan matriks menjadi beberapa matriks komponen.

Dan berikut adalah code untuk SVD dengan menggunakan function svd yang terdapat pada module scipy.linalg :


```python
matrix_A = np.transpose(tf)
print("Ukuran Matrix A = ",matrix_A.shape)
# SVD
U, D, VT = svd(matrix_A)
print("Matrix U = ", U.shape)
print("Matrix S = ", D.shape)
print("Matrix Vt = ", VT.shape)
# create m x n Sigma matrix
Sigma = np.zeros((matrix_A.shape[0], matrix_A.shape[1]))
# populate Sigma with n x n diagonal matrix
Sigma[:matrix_A.shape[1], :matrix_A.shape[1]] = np.diag(D)
print("Matrix Sigma = ", Sigma.shape)
# reconstruct matrix
B = U.dot(Sigma.dot(VT))
print("Matrix A reconstruct = ", B.shape)
```