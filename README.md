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
1. Import Libraries ที่ต้องใช้ในการวิเคราะห์ข้อมูล อาทิ pandas, numpy, matplotlib และ seaborn

![image](https://user-images.githubusercontent.com/126036942/226324088-f25a27a1-7e42-4453-b7db-fbb0910b23fd.png)

2. Import Data ซึ่งใช้ในงานครั้งนี้ทั้งหมด 3 ไฟล์ด้วยกัน

- ข้อมูลคันจิในชีวิตประจำวัน + ข้อมูลคันจิ JLPT แบบเก่าและใหม่ : 2,136 rows / 14 columns
![image](https://user-images.githubusercontent.com/126036942/226325608-8f7ee7d6-db68-4c20-ad83-b5e8a2ccd2e4.png)

- คันจิในชีวิตประจำวัน + ข้อมูลความถี่ในการปรากฎในหนังสือพิมพ์ไมนิจิ (毎日新聞) ช่วงปี 2000-2010 : 2,136 rows / 68 columns
![image](https://user-images.githubusercontent.com/126036942/226326051-db93f314-b70f-46cd-a2e3-98e0779f3d73.png)

- ข้อมูลความถี่ของคันจิผสม 2 ตัวที่ปรากฎในหนังสือพิมพ์ไมนิจิ (毎日新聞) ช่วงปี 2000-2010 : 27,950 rows / 6 columns
![image](https://user-images.githubusercontent.com/126036942/226325786-ce59c434-33bc-463e-936e-4f6c09c6ebaf.png)

## Cleanse & Reshape Data
1. ตรวจสอบและแก้ไขไฟล์ที่ 1: พบว่ามี Null ทั้งหมด 8 columns แต่ข้อมูลที่คาดว่าจะใช้จริงมีเพียง 2 columns คือ jlpt_old และ jlpt_new

- ตรวจสอบแล้วพบว่าเป็นคันจิในชีวิตประจำวันที่อยู่นอกข้อสอบ JLPT จึงทำการแก้ไขให้ Null เป็น 0

- ตัด column ของ freq หรือจัดลำดับความถี่ในการใช้งานคันจิซึ่งไม่ได้ใช้ และตัด column ข้อมูลของ wk หรือ WakiKani ซึ่งเป็นระดับการศึกษาภาษาญี่ปุ่นสำหรับต่างชาติที่ไม่ได้เป็นที่นิยมในไทย

![image](https://user-images.githubusercontent.com/126036942/226327710-e651bd54-dae6-4256-a4a9-c471bfde4303.png)

2. ตรวจสอบและแก้ไขไฟล์ที่ 2: พบว่ามี Null ค่อนข้างหลาย columns ด้วยกัน แต่ที่ใช้คาดว่าจะใช้งานมี 3 columns ด้วยกัน คือ 
## Univariate Analysis

## Bivariate Analysis

# Data Visualization

# Conclusion & Next Steps
