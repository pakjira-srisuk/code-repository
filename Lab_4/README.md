## สร้างและรัน Spatial Model

Multi-Criteria Analysis (MCA) ของ Urban Heat Stress ประกอบด้วยขั้นตอนหลักดังนี้:

1. Normalize ทุก Factor
วิธีทำ: ใช้ Min-Max normalization
𝑋
norm
=
𝑋
−
𝑋
min
⁡
𝑋
max
⁡
−
𝑋
min
⁡
X
norm
	​

=
X
max
	​

−X
min
	​

X−X
min
	​

	​

เหตุผล:
แต่ละ factor มีหน่วยและช่วงค่าต่างกัน เช่น LST (°C), NDVI (0–1), NDBI (-0.5 ถึง 0.5) การ normalize จะปรับให้ทุก factor อยู่ในช่วงเดียวกัน (0–1) เพื่อให้สามารถรวมเป็น Weighted Sum ได้อย่างสมเหตุสมผล

