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
Note: JLPT (Japanese Language Proficiency Test) คือการสอบวัดระดับความรู้ความสามารถภาษาญี่ปุ่นสำหรับชาวต่างชาติ โดยรูปแบบเก่ามี 4 ระดับ (4 ง่ายสุด 1 ยากสุด) และมีการปรับใหม่ในปี 2010 เป็น 5 ระดับ (5 ง่ายสุด 1 ยากสุด) 

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
**1. ตรวจสอบและแก้ไข Dataframe ที่ 1 (kanji):** พบว่ามี Null ทั้งหมด 8 columns แต่ข้อมูลที่คาดว่าจะใช้จริงมีเพียง 2 columns คือ "jlpt_old" และ "jlpt_new"

![image](https://user-images.githubusercontent.com/126036942/227119504-fee311fe-5d9a-477c-9237-f91c37d7cd39.png)

- ตรวจสอบข้อมูล jlpt_old และ jlpt_new พบว่าเป็นคันจิในชีวิตประจำวันที่อยู่นอกข้อสอบ JLPT จึงทำการแก้ไขให้ Null เป็น 0
```
kanji['jlpt_old'].fillna(0, inplace=True)
kanji['jlpt_new'].fillna(0, inplace=True)
```

- ตัด column ของ freq หรือจัดลำดับความถี่ในการใช้งานคันจิซึ่งไม่ได้ใช้ และตัด column ข้อมูลเกี่ยวกับ wk หรือ WaniKani ซึ่งเป็นระดับการศึกษาภาษาญี่ปุ่นสำหรับต่างชาติที่ไม่ได้เป็นที่นิยมในไทย
```
kanji = kanji.iloc[:,[0,1,4,5,6,7,8]]
kanji.info()
```
![image](https://user-images.githubusercontent.com/126036942/227129663-cb3b5f14-fe57-468c-aefb-67887fab1ee6.png)


**2. ตรวจสอบและแก้ไข Dataframe ที่ 2 (mainichi):** พบว่ามี Null ค่อนข้างหลาย columns ด้วยกัน แต่ที่ใช้คาดว่าจะใช้งานมี 3 columns ด้วยกัน คือ "Reading beyond Joyo", "On within Joyo" และ "Translation of Kun"

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

- ตรวจสอบ column "Translation of Kun" พบว่าเมื่อเทียบข้อมูลกับ column "Reading within Joyo" แล้ว หากไม่มีแต่เสียงอ่าน Kun (เขียนแทนด้วย Hiragana）"Translation of Kun" จะมี value เป็น "-" และถ้าหากมีเสียงอ่านก็จะมี value เป็นคำแปลภาษาอังกฤษใน column นั้น ซึ่งพบว่าคันจิ "双" มีเสียงอ่าน Kun จึงก็อปปี้ความหมายจาก column "Translation of On" มาใส่แทนเนื่องจากโดยปกติแล้วความหมายจะคล้ายคลึงกัน แต่คันจิ "弊" ไม่มีเสียงอ่าน Kun จึงแก้ value เป็น "-" แทนให้เหมือนกับ row อื่นๆ

Note：column "Reading within Joyo" จะแทนเสียงอ่าน Kun ด้วยตัวอักษร Hiragana และแทนเสียงอ่าน On ด้วยตัวอักษร Katakana
```
mainichi.iloc[:,[1,8,14,19]][mainichi['Translation of Kun'].isnull()]
```
![image](https://user-images.githubusercontent.com/126036942/227128050-bfff67d5-7d6c-4dcd-9a4d-b34e9bd06de8.png)
```
mainichi.fillna({'Translation of Kun': {1216: mainichi.iloc[1797,14] , 1797:'-'}},inplace=True)
```

- ตัด column ที่คาดว่าจะไม่ใช้ออก
```
mainichi = mainichi.iloc[:, 0:21]
mainichi.info()
```
![image](https://user-images.githubusercontent.com/126036942/227130028-467c22b6-347c-4ec0-a0e9-0470b50fd475.png)

**3. ตรวจสอบและแก้ไข Dataframe ที่ 3 (่jukugo):** พบว่ามีข้อมูลค่อนข้างเรียบร้อย มีแค่ column "English Translation" ที่มี Null อยู่เล็กน้อย แต่คาดว่าจะไม่ได้ใช้ข้อมูลตรงนั้นจึงแก้ไข Null เป็น "-" ทั้งหมด
```
jukugo['English Translation'].fillna('-', inplace=True)
jukugo.info()
```
![image](https://user-images.githubusercontent.com/126036942/227130629-f82f8427-7350-405f-a67e-379d3a41d00c.png)

## Merge Data
Dataframe ของ kanji และ mainichi ประกอบด้วยคันจิในชีวิตประจำวันเหมือนกัน จึงตรวจสอบตัวคันจิก่อนว่าข้อมูลเหมือนกันทุกตัวหรือไม่ และพบว่ามีคันจิ 2 ตัวที่แตกต่างกัน
```
display(kanji[~kanji['index'].isin(mainichi['Kanji'])])
display(mainichi[~mainichi['Kanji'].isin(kanji['index'])])
```

![image](https://user-images.githubusercontent.com/126036942/227134932-465dd3ff-13d7-4e6a-9382-3f87b7295608.png)

จากการตรวจสอบพบว่า 塡 และ 頰 เป็นคันจิแบบเก่าซึ่งปัจจุบันมีการอัพเดทใหม่และใช้เป็น 填 และ 頬 แทนแล้ว จึงแก้คันจิรูปแบบเก่าเป็นแบบปัจจุบันให้ข้อมูลตรงกัน
```
kanji.loc[2134,'index'] = '填'
kanji.loc[2135,'index'] = '頬'
```
หลังจากข้อมูลตรงกันแล้วจึงเรา Dataframe ทั้งสองมา Merge กันและ drop column ที่ซ้ำทิ้งและตรวจสอบความเรียบร้อยของข้อมูลอีกครั้ง
```
m_kanji = pd.merge(kanji[['index','jlpt_old','jlpt_new']], mainichi[:], left_on='index', right_on='Kanji')
m_kanji = m_kanji.drop(['id','Kanji'], axis=1)
m_kanji.info()
```
![image](https://user-images.githubusercontent.com/126036942/227136672-f340d3c6-1ff9-4c05-9c48-6a0f6ac83212.png)

# Data Analysis/Visualization
## คันจิในชีวิตประจำวันกับระดับการศึกษา
**ประเด็นที่ 1:** เปรียบเทียบจำนวนคันจิในชีวิตประจำวันที่เรียนแยกตามช่วง "ระดับการศึกษาญี่ปุ่น (Grade)" และ "ระดับ JLPT"

![image](https://user-images.githubusercontent.com/126036942/227142486-a5db674e-2634-4ab3-a8c5-3926c1d12f1c.png)
Note: Grade 1-6 = ระดับประถม 1-6 / Grade 7 = ระดับมัธยมต้นเป็นต้นไป

Note: JLPT 0 แทนค่าด้วยคันจิที่อยู่นอกเหนือจากการสอบวัดระดับภาษาญี่ปุ่น


จะเห็นได้ว่าการเรียนการสอนคันจิที่ในชีวิตประจำวันตามระดับการศึกษาและระดับ JLPT มีความสอดคล้องกันอย่างมาก ซึ่งเมื่อเปรียบเทียบเป็นคู่กันแล้ว จะพบว่าจำนวนมีความใกล้เคียงและจับคู่กันได้ ดังนี้ 
`Grade1 ≈ JLPT5 / Grade2 ≈ JLPT4 / Grade3+Grade4 ≈ JLPT3 / Grade5+Grade6 ≈ JLPT2 / Grade7 ≈ JLPT1+JLPT0` 
ซึ่งสามารถใช้ข้อมูลดังกล่าวไปหาหนังสือหรือสื่อต่างๆในระดับเดียวกัน ในการพัฒนาความรู้ความสามารถภาษาญี่ปุ่นเพิ่มเติมได้



**ประเด็นที่ 2:** เปรียบเทียบจำนวนคันจิที่ใช้ในการสอบของ JLPT แบบเก่าและ JLPT แบบใหม่

![image](https://user-images.githubusercontent.com/126036942/227148293-b931fc35-e25c-4e1f-bcc8-3b781427128c.png)

จะเห็นได้ว่าข้อมูลมีความสอดคล้องอย่างชัดเจนเกี่ยวกับการแบ่งระดับที่จำนวนคันจิที่ใช้มีความใกล้เคียงกัน
`JLPT4(เก่า) ≈ JLPT5(ใหม่) / JLPT3(เก่า) ≈ JLPT4(ใหม่) / JLPT2(เก่า) ≈ JLPT3(ใหม่)＋JLPT2(ใหม่) / JLPT1(เก่า) ≈ JLPT1(ใหม่)`
จากข้อมูลข้างต้นสามารถใช้สรุปได้ 2 กรณี ดังนี้
- ใช้อ้างอิงสมมุติฐานว่าด้วยความยากของ JLPT2(เก่า) มีความห่างจาก JLPT3(เก่า) มากเกินไป จึงมีการแบ่งออกเป็น JLPT3(ใหม่) และ JLPT2(ใหม่)
- ใช้อ้างอิงในกรณีหาเอกสารข้อสอบเก่าของ JLPT แบบเก่าเพื่อฝึกปรือความสามารถ เนื่องจากหลังจากเปลี่ยนเป็น 5 มีการเพิ่มกฎห้ามเผยแพร่ข้อมูลข้อสอบ จึงทำให้ข้อสอบเก่าของ JLPT แบบใหม่หาได้ยากมากต่างกับการหาข้อสอบเก่าของ JLPT แบบเก่าที่หาได้ค่อนข้างง่ายโดยเฉพาะทางออนไลน์



**ประเด็นที่ 3:** จำนวนเส้นของคันจิในชีวิตประจำวันกับความสอดคล้องในระดับ JLPT

![image](https://user-images.githubusercontent.com/126036942/227153382-ad9c4daa-a9d6-4b2d-8dde-1793354978f3.png)

จะเห็นได้ว่าการแจกแจงของจำนวนเส้นของคันจิจะเพิ่มขึ้นอย่างต่อเนื่องเมื่อระดับความยากของข้อสอบเพิ่มขึ้น
.
.
.
.

## คันจิในชีวิตประจำวันกับเสียงอ่าน
**ประเด็นที่ 4:** การกระจายตัวจำนวนเสียงอ่านทั้งหมด (On และ Kun) ของคันจิในชีวิตประจำวัน

Note: เสียง On คือเสียงอ่านแบบจีน / เสียง Kun คือเสียงอ่านแบบญี่ปุ่น

Note: คันจิหนึ่งตัว สามารถอ่านได้หลายเสียง

![image](https://user-images.githubusercontent.com/126036942/227156972-a31353cf-ef99-48d4-86ed-b5f2606d0114.png)

จะเห็นได้ว่าคันจิในชีวิตประจำวันส่วนใหญ่จะมีเสียงอ่านเพียง 1-2 เสียงเท่านั้น

![image](https://user-images.githubusercontent.com/126036942/227158478-609df878-8d02-469c-9de7-234c9b5f797a.png)


**ประเด็นที่ 5:** ทิศทางการกระจายตัวของจำนวนเสียง On และ Kun ของคันจิในชีวิตประจำวัน

![image](https://user-images.githubusercontent.com/126036942/227160173-0b8c4b7f-1265-41a6-99b9-f01974ddd149.png)

จะเห็นได้ว่าคันจิในชีวิตประจำวัน

## คันจิในชีวิตประจำวันกับคำผสมคันจิ 2 ตัว
**ประเด็นที่ 6:** หนังสือพิมพ์ไมนิจิช่วงปี 2000-2010 มีความถี่ในการปรากฎตัวซ้ำๆของคันจิผสม 2 ตัว




## Univariate Analysis

## Bivariate Analysis

# Data Visualization

# Conclusion & Next Steps
