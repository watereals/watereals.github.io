<!-- $theme: gaia -->
# input data

![150%](images/data_dicription.jpg)

all these data have 13 integer features and 26 categorical features.

------------

|Label |I1 |I2 |... |I13 |C1 |C2 |... |C26 |
|--|--:|--:|---:|----:|--------:|--------:|---:|-------:|
|1 |3  |20 |... |2741 |68fd1e64 |80e26c9b |... |4cf72387|
|0 |7  |91 |... |1157 |3516f6e6 |cfc86806 |... |796a1a2e|
|0 |12 |73 |... |1844 |05db9164 |38a947a1 |... |5d93f8ab|
|...|... |... |... |... |... |... |... |... |... |
|? |9  |62 |... |1457 |68fd1e64 |cfc86806 |... |cf59444f|

----

* train data: tr.csv
![](images/tr.csv.png)
* test data: te.csv
![](images/te.csv.png)

---
# gbdt input features
* categorical features: tr.gbdt.sparse
![](images/tr.gbdt.sparse.png)
* integer features: tr.gbdt.dense
![](images/tr.gbdt.dense.png)

---
# gbdt out features
* tr.gbdt.out
![](images/tr.gbdt.out.png)

-----

# convert features to fmm format

* tr.fmm
![](images/tr.ffm.png)
ffm model input features
-----

# ffm model predict out
* te.out
![](images/te.out.png)

---
# criteo
* te.out.cal
![](images/te.out.cal.png)
* submission
![](images/submission.png)
