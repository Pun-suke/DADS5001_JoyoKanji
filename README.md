# DADS5001 Mini-project
## Topic: 常用漢字 (คันจิในชีวิตประจำวัน 2,136 ตัว) 
By: Pisit Kuensuwan (Student ID: 6510422022)
## Dataset
1. คันจิในชีวิตประจำวัน + ข้อมูลคันจิ JLPT แบบเก่าและใหม่
  
    Source: https://raw.githubusercontent.com/davidluzgouveia/kanji-data/master/kanji-jouyou.json

2. คันจิในชีวิตประจำวัน + ข้อมูลความถี่ในการปรากฎในหนังสือพิมพ์ไมนิจิ (毎日新聞) ช่วงปี 2000-2010
  
    Source: https://www.kanjidatabase.com/kanji_database.php

3. ข้อมูลความถี่ของคันจิผสม 2 ตัวที่ปรากฎในหนังสือพิมพ์ไมนิจิ (毎日新聞) ช่วงปี 2000-2010
  
    Source: https://www.kanjidatabase.com/jukugo_database.php

# Exploratory Data Analysis (EDA)
## Import Libraries & Data
**1. Import Libraries** ที่ต้องใช้ในการวิเคราะห์ข้อมูล อาทิ pandas, numpy, matplotlib และ seaborn
```
import sys
import pandas as pd
import numpy as np
import matplotlib as mpl
%matplotlib inline
import matplotlib.pyplot as plt
import seaborn as sns
plt.style.use('seaborn')
```

**2. Import Data** ซึ่งใช้ในงานครั้งนี้ทั้งหมด 3 ไฟล์ด้วยกัน

**- ข้อมูลคันจิในชีวิตประจำวัน + ข้อมูลคันจิ JLPT แบบเก่าและใหม่:** 2,136 rows / 14 columns
```
kanji = pd.read_json('https://raw.githubusercontent.com/davidluzgouveia/kanji-data/master/kanji-jouyou.json')
kanji = kanji.T.reset_index()
kanji
```

**- คันจิในชีวิตประจำวัน + ข้อมูลความถี่ในการปรากฎในหนังสือพิมพ์ไมนิจิ (毎日新聞) ช่วงปี 2000-2010:** 2,136 rows / 68 columns
```
mainichi = pd.read_csv('KanjiTable.csv')
mainichi
```

**- ข้อมูลความถี่ของคันจิผสม 2 ตัวที่ปรากฎในหนังสือพิมพ์ไมนิจิ (毎日新聞) ช่วงปี 2000-2010:** 27,950 rows / 6 columns
```
jukugo = pd.read_csv('JukugoTable.csv',';')
jukugo
```

## Cleanse & Reshape Data
**1. ตรวจสอบและแก้ไขตารางที่ 1(Kanji):** พบว่ามี Null ทั้งหมด 8 columns แต่ข้อมูลที่คาดว่าจะใช้จริงมีเพียง 2 columns คือ jlpt_old และ jlpt_new

- ตรวจสอบแล้วพบว่าเป็นคันจิในชีวิตประจำวันที่อยู่นอกข้อสอบ JLPT จึงทำการแก้ไขให้ Null เป็น 0
```
kanji['jlpt_old'].fillna(0, inplace=True)
kanji['jlpt_new'].fillna(0, inplace=True)
```

- ตัด column ของ freq หรือจัดลำดับความถี่ในการใช้งานคันจิซึ่งไม่ได้ใช้ และตัด column ข้อมูลของ wk หรือ WakiKani ซึ่งเป็นระดับการศึกษาภาษาญี่ปุ่นสำหรับต่างชาติที่ไม่ได้เป็นที่นิยมในไทย
```
kanji = kanji.iloc[:,[0,1,4,5,6,7,8]]
```

**2. ตรวจสอบและแก้ไขตารางที่ 2(mainichi):** พบว่ามี Null ค่อนข้างหลาย columns ด้วยกัน แต่ที่ใช้คาดว่าจะใช้งานมี 3 columns ด้วยกัน คือ Reading beyond Joyo, On within Joyo และ Translation of Kun

- ตรวจสอบ column Reading by
```
mainichi.iloc[:,[1,8,10,15,11,17]][mainichi['On within Joyo'].isnull()]
```


## Univariate Analysis

## Bivariate Analysis

# Data Visualization

# Conclusion & Next Steps
