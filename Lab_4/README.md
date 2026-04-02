## สร้างและรัน Spatial Model

Multi-Criteria Analysis (MCA) ของ Urban Heat Stress ประกอบด้วยขั้นตอนหลักดังนี้:

1. Normalize ทุก Factor
วิธีทำ: ใช้ Min-Max normalization
​
เหตุผล:
แต่ละ factor มีหน่วยและช่วงค่าต่างกัน เช่น LST (°C), NDVI (0–1), NDBI (-0.5 ถึง 0.5) การ normalize จะปรับให้ทุก factor อยู่ในช่วงเดียวกัน (0–1) เพื่อให้สามารถรวมเป็น Weighted Sum ได้อย่างสมเหตุสมผล

2. กำหนด Weight และ Combine
น้ำหนักที่กำหนด (ตัวอย่างสำหรับ 4 factor):
LST = 0.35 (สำคัญที่สุด)
NDVI_inv = 0.25 (พื้นที่เขียวช่วยลดความร้อน)
NDBI = 0.25 (พื้นที่สิ่งปลูกสร้างเพิ่มความร้อน)
Distance from Water = 0.15 (ใกล้แหล่งน้ำ → ลดความร้อน)
สมการ Weighted Sum:
Heat Stress
=
0.35
⋅
LST
𝑛
+
0.25
⋅
NDVI
𝑛
−
1
+
0.25
⋅
NDBI
𝑛
+
0.15
⋅
Distance
𝑛
Heat Stress=0.35⋅LST
n
	​

+0.25⋅NDVI
n
−1
	​

+0.25⋅NDBI
n
	​

+0.15⋅Distance
n
	​

NDVI inverse: ใช้ค่า NDVI_inv = 1 - NDVI_n เพราะพื้นที่เขียวสูง → เสี่ยงต่ำ

3. แบ่งระดับความเสี่ยง (Classification / Thresholds)
   
วิธีทำ: ใช้ช่วงค่า Heat Stress Index หลัง normalize (0–1)
Threshold ที่ใช้:
Low: <0.2

Medium: 0.2–0.4

High: 0.4–0.6

Very High: >0.6

เหตุผล:
กำหนดจาก ความเหมาะสมในการตีความผลลัพธ์ และช่วงค่าที่ normalized ให้เข้าใจง่ายสำหรับผู้ใช้ / ผู้อ่านแผนที่

5. แผนที่ผลลัพธ์
ใช้ palette สีชัดเจน:
Green → Low Risk, Yellow → Medium, Orange → High, Red → Very High
แสดงเฉพาะ กรุงเทพมหานคร โดย clip geometry
สามารถตีความได้ง่าย: พื้นที่สีแดง = เสี่ยงสูงต่อความร้อน
🔹 ภารกิจ 3: Sensitivity Analysis

เป้าหมาย: ตรวจสอบว่าโมเดลไวต่อการเปลี่ยนน้ำหนัก factor แค่ไหน

1. วิธีทำ
เลือก factor สำคัญที่สุด: LST
เพิ่มน้ำหนัก ±20%:
LST +20% → weight = 0.42
LST -20% → weight = 0.28
รัน Weighted Sum ใหม่ → สร้าง Heat Stress Map ใหม่
เปรียบเทียบ map กับค่าเดิม
2. วิเคราะห์ผล
พื้นที่ CBD / เมืองแออัด: class เปลี่ยนมาก → sensitive
พื้นที่ ชานเมือง / ใกล้สวนสาธารณะ / ใกล้น้ำ: class แทบไม่เปลี่ยน → robust
3. ข้อสรุป
โมเดล Sensitive ต่อ LST → LST เป็น factor สำคัญที่สุด
โมเดล Robust ต่อ NDVI, NDBI, Distance
ผล Sensitivity Analysis เพิ่มความเชื่อมั่น: เรารู้ว่า factor ไหนควรให้ความสำคัญ และ factor ไหนโมเดลไม่ไว
🔹 ภารกิจ 4: Validation กับข้อมูลจริง
1. วิธี Validation
ไม่มี Ground Truth Heat Stress ในกรุงเทพโดยตรง
ใช้ LST MODIS เป็น proxy เพื่อตรวจสอบความสมเหตุสมผล
ตรวจสอบว่าพื้นที่ที่ LST สูงตรงกับพื้นที่ Heat Stress สูงหรือไม่
2. การตีความ
พบว่า Heat Stress สูงตรงพื้นที่ CBD / เขตแออัด → สอดคล้องกับ LST MODIS
พื้นที่ชานเมือง / ใกล้สวน / ใกล้น้ำ → Heat Stress ต่ำ → สอดคล้องกับข้อมูลจริง
3. ข้อจำกัด
โมเดล ไม่รวม microclimate เช่น อาคารสูง / ซอยแคบ / การระบายความร้อนจากอาคาร
การ validate เป็น ระดับ proxy แต่ยังช่วยให้เห็น pattern พื้นที่เสี่ยง
4. วิธี Validation ในชีวิตจริง
สามารถใช้ sensor วัดอุณหภูมิจริง ตามจุดในเมือง
หรือใช้ รายงาน Heatwave / complaints ของประชาชน
เปรียบเทียบกับ Heat Stress Map เพื่อปรับปรุงโมเดลให้แม่นยำขึ้น
