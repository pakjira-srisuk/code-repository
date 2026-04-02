## ความเครียดจากความร้อนในพื้นที่เมือง (Urban Heat Stress) 

พื้นที่ศึกษา คือ จังหวัดชลบุรี

## สร้างและรัน Spatial Model

### Data Preprocessing

กำหนดพื้นที่ศึกษา : จังหวัดชลบุรี
กำหนดช่วงเวลา : ปี 2023
ใช้ข้อมูลจากหลายแหล่ง ได้แก่: MODIS (LST, NDVI) , Landsat 8 (NDBI) , JRC Global Surface Water (Distance to water)
มีการใช้ median composite เพื่อลด noise เช่น เมฆ ค่าผิดปกติ 

### การ Normalize แต่ละ Factor

X<sub>norm</sub> = (X - X<sub>min</sub>) / (X<sub>max</sub> - X<sub>min</sub>)

เหตุผล:
- ทำให้ทุกปัจจัยอยู่ในช่วงเดียวกัน (0–1)
- ลดผลกระทบจากหน่วยที่ต่างกัน เช่น LST (°C)  NDVI (0–1)  Distance (meters)
- ทำให้สามารถนำมารวมกันได้ในสมการเดียว

(1) LST
- Normalize แบบตรง
- ค่า LST สูง → เสี่ยงสูง
- 
(2) NDVI
- Normalize ก่อน แล้วใช้ (1 - NDVI)
เพราะ NDVI สูง = พื้นที่เย็น จึงต้องกลับค่าให้ “ค่าสูง = เสี่ยงสูง”

(3) NDBI
- Normalize แบบตรง
- ค่า NDBI สูง → เมืองมาก → เสี่ยงสูง
- 
(4) Distance from Water
- Normalize ก่อน แล้วกลับค่า (1 - distance)

เพราะ ใกล้น้ำ = เย็น , ไกลน้ำ = เสี่ยง

### การกำหนด Weight และ Combine
###
