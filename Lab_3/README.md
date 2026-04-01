## พื้นที่ศึกษา คือ จังหวัดอ่างทอง

## 1. ออกแบบ Training Strategy

### 1.1 การกำหนดประเภทการใช้ที่ดิน

ในการศึกษานี้กำหนด 5 classes ได้แก่

- Water – แหล่งน้ำ เช่น แม่น้ำ คลอง บึง

- Wetland Vegetation – พืชในพื้นที่ชุ่มน้ำหรือพื้นที่น้ำขัง

- Agriculture – พื้นที่เกษตรกรรม เช่น นาข้าว

- Forest – พื้นที่ป่าไม้หรือพืชพรรณหนาแน่น

- Urban – พื้นที่เมืองและสิ่งปลูกสร้าง

การกำหนด class ทำโดยการ remap จาก Dynamic World เพื่อให้เหมาะสมกับบริบทพื้นที่จริง

### 1.2 การสร้าง Training Samples

เนื่องจากไม่มีข้อมูลภาคสนาม จึงใช้ Dynamic World เป็น reference dataset และสุ่มตัวอย่างข้อมูลจำนวน 6,000 จุดภายในพื้นที่ศึกษา

วิธีนี้ช่วยลดต้นทุนและสามารถนำไปใช้ได้อย่างรวดเร็วในพื้นที่ขนาดใหญ่ อย่างไรก็ตาม ข้อมูล label อาจมีความคลาดเคลื่อน (noise) ซึ่งส่งผลต่อความแม่นยำของโมเดล

### 1.3 การแบ่ง Train / Validation

ใช้วิธีสุ่มแบ่งข้อมูล : 80% สำหรับ training และ 20% สำหรับ testing

แม้ว่าวิธีนี้จะสะดวกและนิยมใช้ แต่มีข้อจำกัดคือ spatial autocorrelation ซึ่งอาจทำให้ค่าความแม่นยำสูงกว่าความเป็นจริง เนื่องจากข้อมูล train และ test อยู่ใกล้กันในเชิงพื้นที่

### 1.4 Feature ที่ใช้

Spectral Bands

- B2 (Blue)
- B3 (Green)
- B4 (Red)
- B8 (Near Infrared)
  
Spectral Indices

- NDVI – ใช้แยกพื้นที่พืชพรรณ
- NDWI – ใช้ตรวจจับแหล่งน้ำ
- NDBI – ใช้แยกพื้นที่เมือง

การรวม spectral bands และ indices ช่วยเพิ่มความสามารถของโมเดลในการแยกประเภทพื้นผิวที่มีลักษณะคล้ายกัน

## 2. เปรียบเทียบอัลกอริทึม

### โมเดลที่ใช้ในการทดลอง

- Random Forest (50 trees)
- Random Forest (100 trees)
- Gradient Tree Boost

### ผลการทดลอง

Random Forest (50 Trees)
- Overall Accuracy: 0.7290
- Kappa: 0.5135
  
Random Forest (100 Trees)
- Overall Accuracy: 0.7344
- Kappa: 0.5272
  
Gradient Tree Boost
- Overall Accuracy: 0.7190
- Kappa: 0.4957

### การวิเคราะห์ผล

จากผลการทดลองพบว่า Random Forest ที่ใช้จำนวนต้นไม้ 100 trees ให้ผลลัพธ์ดีที่สุด โดยมีค่าความแม่นยำและค่า Kappa สูงสุด การเพิ่มจำนวนต้นไม้ช่วยลดความแปรปรวนของโมเดลและทำให้ผลลัพธ์มีเสถียรภาพมากขึ้น

ในขณะที่ Gradient Tree Boost ให้ผลลัพธ์ใกล้เคียง แต่มีประสิทธิภาพต่ำกว่าเล็กน้อย ซึ่งอาจเกิดจากข้อจำกัดของจำนวน training samples หรือความซับซ้อนของข้อมูล

### การวิเคราะห์ราย Class (F1-score)

- Forest มีค่าความแม่นยำสูงที่สุด
- Agriculture มีค่าความแม่นยำต่ำที่สุด

แสดงให้เห็นว่าโมเดลสามารถจำแนกพื้นที่ป่าได้ดี แต่มีความยากในการแยกพื้นที่เกษตรกรรมออกจาก class อื่น

## 3. วิเคราะห์ Feature Importance

### ผลการวิเคราะห์

จาก Random Forest พบว่า feature ที่มีความสำคัญสูง ได้แก่

- NDVI
- B8 (Near Infrared)
- NDWI
  
### การตีความเชิงกายภาพ

NDVI มีบทบาทสำคัญในการแยกพื้นที่พืชพรรณ เนื่องจากพืชสะท้อนแสงในช่วง near-infrared สูง ซึ่งสอดคล้องกับหลักการทาง remote sensing

NDWI ช่วยแยกแหล่งน้ำออกจากพื้นดิน ขณะที่ NDBI มีบทบาทในการแยกพื้นที่เมือง

### การทดลองตัด Feature (Ablation Test)

เมื่อทำการตัด NDVI ออก พบว่า Accuracy เปลี่ยนจาก 0.7290 → 0.7299

ผลลัพธ์แสดงให้เห็นว่า NDVI มีความสำคัญ แต่ไม่ใช่ปัจจัยเดียวที่กำหนดประสิทธิภาพของโมเดล เนื่องจากข้อมูลจาก B8 สามารถชดเชยได้บางส่วน

## 4. ประเมินความไม่แน่นอน

### การวิเคราะห์ Confusion Matrix

พบว่า class ที่มีความสับสนสูง ได้แก่

- Agriculture vs Forest
- Agriculture vs Wetland Vegetation

### สาเหตุของความไม่แน่นอน
- พื้นที่เกษตรกรรมมีลักษณะคล้าย vegetation
- พื้นที่ชุ่มน้ำมีทั้งน้ำและพืช ทำให้มี spectral overlap
- ลักษณะ seasonal ของพืชส่งผลต่อค่าการสะท้อนแสง
  
### การประเมินความน่าเชื่อถือ

- โมเดลมีความแม่นยำสูงใน Water และ Forest
- มีความไม่แน่นอนใน Agriculture และ Wetland Vegetation

ดังนั้น แผนที่นี้เหมาะสำหรับการวิเคราะห์ในระดับภาพรวม (regional scale) แต่ไม่เหมาะสำหรับการใช้งานในระดับรายละเอียดสูงโดยไม่มีข้อมูลภาคสนามเพิ่มเติม

## คำถามสำหรับใช้คิด
### 1. ถ้าเพิ่ม Training Samples อีก 2 เท่า ความแม่นยำจะเพิ่มขึ้นเท่าไหร่? 

### ทดสอบ

Random Forest (50 trees) → Accuracy = 0.7290

Random Forest (100 trees) → Accuracy = 0.7344

จะเห็นว่าแม้มีการเพิ่ม complexity ของโมเดล (จำนวน trees) ความแม่นยำเพิ่มขึ้นเพียงเล็กน้อย (~0.5%)

ดังนั้น หากเพิ่มจำนวน training samples เป็น 2 เท่า ความแม่นยำมีแนวโน้มเพิ่มขึ้น เล็กน้อย (ประมาณ 1–3%) ไม่เพิ่มแบบ linear เนื่องจากเกิด diminishing returns

### เหตุผล
- ข้อมูลเดิมครอบคลุม pattern หลักแล้ว
training samples 6000 จุดถือว่าเพียงพอในระดับหนึ่ง
- คุณภาพของ label สำคัญกว่าปริมาณ
เนื่องจากใช้ Dynamic World ซึ่งมี noise → เพิ่ม sample = เพิ่ม noise ด้วย
- ปัญหาหลักคือ class overlap ไม่ใช่ data scarcity
เช่น Agriculture vs Forest ที่มี F1-score ต่ำ (~0.19)

สรุปแล้ว การเพิ่ม training samples จะช่วยได้แต่ไม่ใช่ปัจจัยหลักในการเพิ่ม accuracy ในกรณีนี้ควรเน้นที่ feature engineering และ label quality

### 2. Spatial Autocorrelation ในข้อมูล Training มีผลต่อความแม่นยำที่รายงานอย่างไร?

Spatial autocorrelation ทำให้โมเดลดูเหมือนแม่นยำกว่าความเป็นจริง

### จากผลลัพธ์

Accuracy ~0.73 ถือว่าปานกลาง แต่ F1-score ของบาง class (Agriculture ≈ 0.19) ต่ำมาก

แสดงว่า model perform ดีเฉพาะพื้นที่ที่ “คล้าย training data” แต่ fail ในพื้นที่ที่แตกต่าง

### อธิบายเชิงพื้นที่ 

- pixel ที่อยู่ใกล้กันมี spectral signature คล้ายกัน model จึง “จำ” pattern ได้ง่าย

- ผลกระทบ คือ Accuracy ที่รายงาน → overestimate ไม่สะท้อน performance จริงในพื้นที่ใหม่

สรุปแล้ว Spatial autocorrelation ทำให้ค่า Accuracy และ Kappa สูงเกินจริงและความสามารถ generalization ของโมเดลต่ำกว่าที่ประเมิน

### 3. Class ใดที่โมเดลทำได้แย่ที่สุด แก้ได้ด้วยวิธีใดบ้าง?

Agriculture แย่ที่สุด

F1-score ≈ 0.19 (RF)

F1-score ≈ 0.17 (GTB)

### วิเคราะห์สาเหตุ
- Spectral similarity โดย Agriculture มีลักษณะคล้าย Forest และ Wetland , NDVI สูงเหมือน vegetation
- Seasonal variability นาข้าวมีหลาย stage , น้ำ → เหมือน water , เขียว → เหมือน forest
- Label noise จาก Dynamic World บาง pixel อาจถูก label ผิด

### แนวทางแก้ไข
- เพิ่ม Features
  
NDMI → ความชื้น

EVI → vegetation sensitivity

Texture → spatial pattern

- ใช้ Temporal Data

ใช้หลายช่วงเวลา : planting , harvesting ซึ่งจะช่วยแยก agriculture ได้ดีขึ้น

- ปรับ Training Strategy โดยใช้ stratified sampling และเพิ่ม sample ของ agriculture โดยเฉพาะ

- ใช้ Model ที่ซับซ้อนขึ้น เช่น Deep Learning (CNN, U-Net)

### 4. ถ้าต้องทำซ้ำ Lab นี้สำหรับพื้นที่อื่น อะไรคือสิ่งที่ต้องเปลี่ยน และอะไรที่ใช้ซ้ำได้?


### สิ่งที่ต้องเปลี่ยน 
- ขอบเขตพื้นที่ศึกษา 
- Training data
- Class Definition

### สิ่งที่สามารถใช้ซ้ำได้
- Workflow
- Model
- Feature Engineering
