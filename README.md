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

เนื่องจากไฟล์ที่ได้มามีรูปแบบข้อมูลเป็นแนวนอน จึง Transpose ให้ตารางเป็นแนวตั้งแบบเดียวกับไฟล์อื่น
```
kanji = pd.read_json('https://raw.githubusercontent.com/davidluzgouveia/kanji-data/master/kanji-jouyou.json')
kanji = kanji.T.reset_index()
kanji.head()
```
![image](https://user-images.githubusercontent.com/126036942/227118551-6f7c8cd8-f9f9-4650-8e3b-145c5d9b9a5e.png)


**- คันจิในชีวิตประจำวัน + ข้อมูลความถี่ในการปรากฎในหนังสือพิมพ์ไมนิจิ (毎日新聞) ช่วงปี 2000-2010:** 2,136 rows / 68 columns
```
mainichi = pd.read_csv('KanjiTable.csv')
mainichi.head()
```
![image](https://user-images.githubusercontent.com/126036942/227119287-01640a73-6923-4433-af94-b34f02a3861e.png)


**- ข้อมูลความถี่ของคันจิผสม 2 ตัวที่ปรากฎในหนังสือพิมพ์ไมนิจิ (毎日新聞) ช่วงปี 2000-2010:** 27,950 rows / 6 columns
```
jukugo = pd.read_csv('JukugoTable.csv',';')
jukugo.head()
```
![image](https://user-images.githubusercontent.com/126036942/227119121-54704341-8ef0-4f8f-a7e8-231116b5b9a7.png)


## Cleanse & Reshape Data
**1. ตรวจสอบและแก้ไข Dataframe ที่ 1(kanji):** พบว่ามี Null ทั้งหมด 8 columns แต่ข้อมูลที่คาดว่าจะใช้จริงมีเพียง 2 columns คือ "jlpt_old" และ "jlpt_new"
![image](https://user-images.githubusercontent.com/126036942/227119504-fee311fe-5d9a-477c-9237-f91c37d7cd39.png)

- ตรวจสอบข้อมูล jlpt_old และ jlpt_new พบว่าเป็นคันจิในชีวิตประจำวันที่อยู่นอกข้อสอบ JLPT จึงทำการแก้ไขให้ Null เป็น 0
```
kanji['jlpt_old'].fillna(0, inplace=True)
kanji['jlpt_new'].fillna(0, inplace=True)
```

- ตัด column ของ freq หรือจัดลำดับความถี่ในการใช้งานคันจิซึ่งไม่ได้ใช้ และตัด column ข้อมูลเกี่ยวกับ wk หรือ WaniKani ซึ่งเป็นระดับการศึกษาภาษาญี่ปุ่นสำหรับต่างชาติที่ไม่ได้เป็นที่นิยมในไทย
```
kanji = kanji.iloc[:,[0,1,4,5,6,7,8]]
```

**2. ตรวจสอบและแก้ไข Dataframe ที่ 2(mainichi):** พบว่ามี Null ค่อนข้างหลาย columns ด้วยกัน แต่ที่ใช้คาดว่าจะใช้งานมี 3 columns ด้วยกัน คือ "Reading beyond Joyo", "On within Joyo" และ "Translation of Kun"

![image](https://user-images.githubusercontent.com/126036942/227120452-c8d31867-4e39-41b6-809b-4dff63b72431.png)

- ตรวจสอบ column "Reading beyond Joyo" พบว่าเป็นคันจิที่ไม่มีเสียงอ่านนอกจากที่ใช้ในชีวิตประจำวันอยู่แล้ว จึงแก้ไขให้ Null เป็น "-"
```
mainichi['Reading beyond Joyo'].fillna('-', inplace=True)
```

- ตรวจสอบ column "On within Joyo" พบว่า value ที่เป็น Null นั้นมี value ที่ดั้งเดิมคือ nan (ดูจาก column "Reading within Joyo" ประกอบ) ซึ่งเมื่อ import เข้ามาโปรแกรมได้แปลงค่าเป็น Null เองโดยพลการ จึงแก้ไขให้ค่าเป็น nan อย่างที่ควรจะเป็น 

Note: ナン สะกดเป็น Romanji(ตัวสะกดภาษาอังกฤษ) ว่า nan
```
mainichi.iloc[:,[1,8,10,15,11,17]][mainichi['On within Joyo'].isnull()]
```
![image](https://user-images.githubusercontent.com/126036942/227122639-dd40fff7-2467-445f-955f-d8ff4a9f7ff4.png)
```
mainichi['On within Joyo'].fillna('nan', inplace=True)
```


## Univariate Analysis

## Bivariate Analysis

# Data Visualization

# Conclusion & Next Steps
