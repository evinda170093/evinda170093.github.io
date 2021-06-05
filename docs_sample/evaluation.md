## Apa itu Evaluation?

Terdapat beberapa metrik evaluasi yang bisa digunakan untuk mengevaluasi hasil dari kerja sebuah sistem. Diantaranya adalah Recall, Precision, dan F1-Measure.

Recall adalah rasio pengamatan positif yang diprediksi benar positif dengan seluruh data.
Precision adalah rasio pengamatan positif yang diprediksi benar dengan total pengamatan positif yang diprediksi.
F1-Measure adalah rata-rata tertimbang dari Recall dan Precision.

Berikut adalah rumusnya :<br>
Presisi = TP/TP+FP<br>
Recall = TP/TP+FN<br>
F1-Measure = 2*(Recall * Precision) / (Recall + Precision)

Dan berikut adalah code dari metode evaluasi Recall, Precision, dan F1-Measure :

## Library yang dibutuhkan


```python
from sklearn.metrics import classification_report, confusion_matrix, accuracy_score
```


```python
print(confusion_matrix(array_label,y_pred))
print(classification_report(array_label,y_pred))
print(accuracy_score(array_label, y_pred))
```