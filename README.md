# Optical Turbulence Climatology tai Viet Nam
## Danh gia va De Xuat Vi Tri Optical Ground Station (OGS)

---

## Gioi thieu

Nghien cuu nay danh gia climatology cua optical turbulence tai 6 dia diem dai dien o Viet Nam
nham xac dinh vi tri toi uu cho Optical Ground Station (OGS) phuc vu cac lien ket quang khong gian tu do
(Free-Space Optical - FSO) va thong tin ve tinh laser.

Pipeline su dung du lieu tai phan tich ERA5 (ECMWF) giai doan 2016-2025 ket hop voi
Daocheng/Tatarskii parameterization de tinh Cn2(h) theo do cao, tu do suy ra cac tham so FSO:
Fried parameter (r0), Scintillation index (sigma_I2) va Isoplanatic angle (theta0).

---

## Cau hoi nghien cuu

| # | Cau hoi |
|---|---------|
| RQ1 | Dac diem phan bo Cn2 theo khong gian tai 6 dia diem la nhu the nao? |
| RQ2 | Optical turbulence bien doi theo mua, thang va do cao ra sao? |
| RQ3 | Dia diem nao co r0 lon nhat va sigma_I2 nho nhat -- tot nhat cho OGS? |

---

## Dia diem nghien cuu

| Thanh pho | Vung | Vi do | Kinh do | Do cao (m ASL) |
|-----------|------|--------|---------|----------------|
| Sa Pa | Tay Bac | 22.34N | 103.84E | 1,570 |
| Ha Noi | Dong bang Bac Bo | 21.02N | 105.85E | 10 |
| Nha Trang | Duyen hai Nam Trung Bo | 12.24N | 109.19E | 5 |
| Da Lat | Tay Nguyen | 11.94N | 108.45E | 1,500 |
| TP.HCM | Dong Nam Bo | 10.82N | 106.66E | 10 |
| Can Tho | DBSCL | 10.03N | 105.78E | 2 |

---

## Cau truc thu muc

`
Cn2 Vietnam/
|
|-- optical_turbulence_vietnam.ipynb   <- Notebook chinh (toan bo pipeline)
|-- README.md                          <- File nay
|
|-- data/
|   |-- Sapa_2016-2025/        (sapa_2016.nc ... sapa_2025.nc)
|   |-- Hanoi_2016-2025/       (hn_2016.nc ... hn_2025.nc)
|   |-- Nhatrang_2016-2025/    (nhatrang_2016.nc ... nhatrang_2025.nc)
|   |-- Dalat_2016-2025/       (dalat_2016.nc ... dalat_2025.nc)
|   |-- TPHCM_2016-2025/       (hcm_2016.nc ... hcm_2025.nc)
|   -- CanTho_2016-2025/      (cantho_2016.nc ... cantho_2025.nc)
|
-- figures_v2/
    |-- fig00_dashboard.png           <- Master summary dashboard
    |-- fig01_monthly_climatology.png <- Climatology theo thang
    |-- fig02_seasonal.png            <- So sanh theo mua
    |-- fig03_timeseries.png          <- Chuoi thoi gian 2016-2025
    |-- fig04_vertical_profiles.png   <- Profile Cn2(h) full column
    |-- fig05_heatmap.png             <- Heatmap Cn2 (do cao x thang)
    |-- fig06_boxplot_violin.png      <- Phan phoi r0 va sigma_I2
    |-- fig07_cdf.png                 <- CDF tich luy
    |-- fig08_annual.png              <- Xu huong nam 2016-2025
    |-- fig09_correlation.png         <- Tuong quan va scatter plots
    |-- fig10_ogs_ranking.png         <- Xep hang OGS
    |-- table1_summary_stats.csv      <- Thong ke mo ta day du
    -- table2_ogs_ranking.csv        <- Bang xep hang OGS
`

---

## Pipeline

`
ERA5 Pressure-Level Data (2016-2025)
         |
         v
  Load & Preprocess  (xr.concat, spatial mean lat/lon)
         |
         v
  +-------------------------------------------------+
  | Daocheng / Tatarskii Parameterization           |  Upper atm (~5.8-48 km)
  |   M  = -79e-6 * P/T^2 * (dT/dh + Gamma_d)     |
  |   L0 = 0.1 * h^0.5 * exp(-h/8000)             |
  |   Cn2= 2.8 * M^2 * L0^(4/3)                   |
  +-------------------------------------------------+
         |
         v
  +-------------------------------------------------+
  | Hufnagel-Valley PBL Model                       |  PBL (0-5.8 km)
  |   C0 = C0_ref * exp(-h_elev / H_scale)         |  Elevation correction
  |   HV(h) = HV_jet + HV_tropo + C0*exp(-h/100)  |
  +-------------------------------------------------+
         |
         v
  Full Column Cn2(h) = PBL + Upper Atmosphere
         |
         v
  FSO Parameters  (integrate from h_terrain upward)
    r0  = [0.423 * k^2 * integral(Cn2 dh)]^(-3/5)   [cm]
    si2 = 2.25 * k^(7/6) * integral(Cn2*h^(5/6) dh)
    th0 = 0.314 * r0 / h_bar                          [arcsec]
         |
         v
  Climatology Analysis (monthly / seasonal / annual / vertical)
         |
         v
  OGS Site Ranking
  Score = (1/3) * [norm_high(r0) + norm_low(si2) + norm_high(th0)] * 100%
`

---

## Du lieu

**Nguon:** ERA5 Pressure-Level Reanalysis - ECMWF Copernicus Climate Data Store
**Tan suat:** Daily means
**Giai doan:** 2016-01-01 den 2025-12-31  (2370 ngay / thanh pho)

### Bien so ERA5

| Ten bien | Ky hieu | Don vi |
|----------|---------|--------|
| Geopotential | z | m2/s2 |
| Temperature | t | K |
| U-component of wind | u | m/s |
| V-component of wind | v | m/s |

**Pressure levels:** 500, 400, 300, 200, 100, 50, 30, 10, 5, 1 hPa  (~5.8-48 km ASL)

> **Luu y:** ERA5 pressure-level khong bao phu troposphere thap (0-5.8 km).
> PBL duoc bo sung bang mo hinh Hufnagel-Valley voi elevation correction.

---

## Ket qua

### Xep hang OGS (2016-2025, 2370 ngay)

| Rank | Thanh pho | r0 (cm) | sigma_I2 | theta0 (arcsec) | Score |
|------|-----------|---------|----------|-----------------|-------|
| 1 (Gold)   | Da Lat    | 16.41   | 0.258    | 1.072           | 100%  |
| 2 (Silver) | Sa Pa     | 16.19   | 0.270    | 1.055           | 68.2% |
| 3 (Bronze) | Can Tho   | 5.10    | 0.332    | 1.064           | 24.4% |
| 4           | TP.HCM   | 5.31    | 0.330    | 1.063           | 23.2% |
| 5           | Nha Trang| 5.18    | 0.331    | 1.063           | 22.6% |
| 6           | Ha Noi   | 5.29    | 0.339    | 1.050           | 0.6%  |

**=> Da Lat duoc de xuat la vi tri toi uu cho OGS tai Viet Nam.**

### Cac phat hien chinh

1. **Phan nhom ro ret:** High-altitude (Da Lat, Sa Pa >= 1500 m) vs Lowland (<= 10 m)
   -- r0 chenh nhau ~3x (16 cm vs 5 cm)
2. **Mua tot nhat cho OGS:** Mua dong (Thang 12-2) tai tat ca dia diem
   -- Da Lat dat r0 = 16.59 cm (cao nhat trong toan bo dataset)
3. **Dac trung Cn2(h):** Manh nhat o tang bien (0-2 km) va tropopause (12-16 km)
4. **Loi the dia hinh:** Do cao >= 1500 m ASL giup OGS vuot qua phan lon PBL turbulence

---

## Yeu cau moi truong

### Cai dat

`ash
pip install numpy pandas xarray netCDF4 matplotlib seaborn scipy jupyter
`

Hoac voi conda:

`ash
conda install numpy pandas xarray netCDF4 matplotlib seaborn scipy jupyter
`

**Python >= 3.9** duoc khuyen nghi.

### Chay notebook

`ash
# Mo notebook tuong tac
jupyter notebook optical_turbulence_vietnam.ipynb

# Thuc thi toan bo tu terminal
jupyter nbconvert --to notebook --execute --inplace optical_turbulence_vietnam.ipynb
`

---

## Phuong phap

### 1. Daocheng / Tatarskii Parameterization  (Upper Atmosphere)

`
M  = -79e-6 * P / T^2 * (dT/dh + g/cp)    [refractivity gradient]
L0 = 0.1 * h^0.5 * exp(-h / 8000)          [outer scale, m]
Cn2= 2.8 * M^2 * L0^(4/3)                  [structure constant, m^(-2/3)]
`

Gamma_d = g/cp = 9.8e-3 K/m  (dry adiabatic lapse rate)

### 2. Hufnagel-Valley PBL Model  (Lower Atmosphere, 0-5.8 km)

`
HV(h) = 0.00594*(vw/27)^2*(1e-5*h)^10*exp(-h/1000)  [jet stream term]
      + 2.7e-16*exp(-h/1500)                          [background term]
      + C0*exp(-h/100)                                [surface/ground layer]

C0 = 1.7e-14 * exp(-h_elev / 1000)  [elevation correction; m^(-2/3)]
`

### 3. FSO Parameters  (Kolmogorov-Tatarskii theory)

`
r0  = [0.423 * k^2 * integral(Cn2 dh)]^(-3/5)          [Fried coherence length, cm]
si2 = 2.25 * k^(7/6) * integral(Cn2 * h^(5/6) dh)     [Rytov scintillation index]
th0 = 0.314 * r0 / h_bar                                 [isoplanatic angle, arcsec]

k = 2*pi / lambda   (lambda = 550 nm)
Integration range: from terrain elevation h_s to top of atmosphere.
`

### 4. Composite OGS Score

`
Score = (1/3) * [norm_high(r0) + norm_low(si2) + norm_high(th0)] * 100%

norm_high(x) = (x - x_min) / (x_max - x_min)
norm_low(x)  = 1 - norm_high(x)
`

---

## Gioi han va Huong phat trien

### Gioi han hien tai
- ERA5 pressure-level khong bao phu troposphere thap (0-5.8 km); PBL duoc mo hinh hoa thay vi do truc tiep
- Do phan giai khong gian ERA5: 0.25 deg (~28 km) -- khong phan giai dia hinh vi mo
- Temporal resolution: daily -- khong capture bien doi trong ngay (diurnal cycle)
- Cloud cover chua duoc tich hop vao composite score

### Huong phat trien
- [ ] Tich hop ERA5 model-level data (137 levels tu be mat) thay the HV PBL model
- [ ] Phan tich diurnal variation (ngay/dem) tu ERA5 hourly data
- [ ] Tich hop cloud fraction tu ERA5 hoac MODIS vao scoring
- [ ] Validate voi du lieu DIMM/SCIDAR do tai cho
- [ ] Mo rong sang them dia diem (Da Nang, Buon Ma Thuot, Phu Quoc)
- [ ] Phan tich extreme events -- ngay co r0 > percentile 90%

---

## Tai lieu tham khao

1. Tatarskii, V.I. (1961). Wave Propagation in a Turbulent Medium. McGraw-Hill.
2. Fried, D.L. (1966). Optical resolution through a randomly inhomogeneous medium. JOSA, 56(10), 1372-1379.
3. Hufnagel, R.E. & Stanley, N.R. (1964). Modulation transfer function. JOSA, 54, 52.
4. Valley, G.C. (1980). Isoplanatic degradation. Appl. Opt., 19(4), 574-577.
5. Coulman, C.E. & Vernin, J. (1991). Significance of anisotropy. Appl. Opt., 30(1), 118-126.
6. Hersbach, H. et al. (2020). The ERA5 global reanalysis. QJRMS, 146(730), 1999-2049.
7. Andrews, L.C. & Phillips, R.L. (2005). Laser Beam Propagation through Random Media, 2nd ed. SPIE Press.

---

*Phan tich thuc hien bang Python 3.11 | ERA5 via CDS API | lambda = 550 nm | zenith = 0 deg*
