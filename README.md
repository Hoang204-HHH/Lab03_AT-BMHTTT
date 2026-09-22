# LAB 3 – Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin

*Identifying and Responding to Information Security Threats* · Bộ môn An toàn Thông tin · Năm học 2026–2027

| Thông tin | Nội dung |
|---|---|
| Họ và tên | Huỳnh Hữu Hoàng |
| MSSV | 1150080095 |
| Lớp | 11CPNM2 |
| Giảng viên | Phạm Trọng Huynh |
| Ngày thực hiện | 22/09/2026 (14:40 – 17:13) |
| Báo cáo | [11CPNM2-LAB3_1150080095-HuynhHuuHoang.docx](11CPNM2-LAB3_1150080095-HuynhHuuHoang.docx) · [bản PDF](11CPNM2-LAB3_1150080095-HuynhHuuHoang.pdf) |
| Video thực hành | https://youtu.be/yohzqzY2r4s |

## Kết quả

| Tình huống | Nội dung | Kết quả chính | Ảnh | Kết quả |
|---|---|---|---|---|
| TH1 | Risk register, phân loại 5 nguồn đe dọa | 6 dòng Asset → Vulnerability → Threat → Risk → Control; 5 tình huống được phân loại | – | **PASS** |
| TH2 | Mã độc – EICAR | Defender quarantine `Virus:DOS/EICAR_Test_File` lúc 15:19:30, ActionSuccess = True | [H4](Screenshots/H4_ProtectionHistory_EICAR.png) | **PASS** |
| TH3 | Tấn công mật khẩu, keylogging | 4648 + 4624 (đúng), 4625 × 2 (sai); sau khi đổi mật khẩu, mật khẩu cũ → 4625, mật khẩu mới → 4624 | [H5](Screenshots/H5_Event4625.png) | **PASS** |
| TH4 | Backdoor – persistence và listener | Autoruns + Sysmon phát hiện `LAB3_Run_Demo` và `LAB3_Persistence_Demo`; cổng `127.0.0.1:8080` → PID 2480 `python.exe` (Verified PSF) | [H6](Screenshots/H6_Sysmon_Event1.png), [H7](Screenshots/H7_Autoruns_LAB3_Run_Demo.png), [H8](Screenshots/H8_ProcessExplorer_Python.png) | **PASS** |
| TH5 | Sniffing – HTTP so với HTTPS | HTTP lộ `lab_code=TRAINING_ONLY` (1 gói); HTTPS chỉ lộ metadata, SNI `example.com` (0 gói) | [H9](Screenshots/H9_HTTP_Plaintext.png), [H10](Screenshots/H10_TLS_443.png) | **PASS** |
| TH6 | DoS, DDoS, Mail bombing | Tải thử 50/50 request tới 127.0.0.1:8080 trong 0,877 s; 120 sự kiện từ 20 IP; `bulk-sender` 60 thư (≈ 92% dung lượng) | [H10](Screenshots/H10_Load_and_Log_Analysis.png) | **PASS** |
| TH7 | Social Engineering, Phishing | 5 chỉ dấu phishing; phân loại 6 case: Phishing, Spear phishing, Pretexting, Baiting, Quid pro quo, Watering hole | [H10](Screenshots/H10_Phishing_Offline.png) | **PASS** |
| B.8 | Cleanup, kiểm tra lại, hash | Không còn artefact LAB3, Defender vẫn bật, Autoruns trước/sau không khác biệt; SHA-256 khớp 32/32 | [H11](Screenshots/H11_Recovery_Verification.png) | **PASS** |

## Môi trường thực hành

| Thành phần | Yêu cầu | Thực tế |
|---|---|---|
| Ảo hóa | VMware Workstation Pro 26H1 | VMware Workstation 26.0.1 (build 25688693) |
| Máy ảo | Windows 11 25H2, build 26200.9445 | **Windows Server 2025 Standard Evaluation**, 24H2, build 26100.1742 – 2 vCPU, 3,7 GB RAM, 60 GB, Host-only |
| Endpoint protection | Defender, Real-time + Tamper Protection | Defender: Real-time = True; Tamper Protection = False (mặc định trên Windows Server) |
| Shell | Windows PowerShell 5.1 | Windows PowerShell 5.1 |
| Sysmon / Autoruns / Process Explorer | 15.22 / 14.3 / 17.14 | 15.22 / 14.3 / 17.14 |
| Wireshark | 4.6.8 + Npcap | 4.6.8 + Npcap (có loopback capture) |
| Python | 3.14.7 | 3.14.7 |

## Cách dựng môi trường

1. Tạo **linked clone** từ snapshot sạch của VM Windows Server 2025 (2 vCPU, 3,7 GB RAM). Chỉ bật NAT tạm thời khi cần tải công cụ, sau đó chuyển về **Host-only** và tạo snapshot `LAB3_CLEAN_20260914`.
2. Tạo `C:\LAB3\{Evidence, Tools, Downloads, Assets, Screenshots}`, chép `lab3_assets` vào `C:\LAB3\lab3_assets` và tính SHA-256 cho 8 tệp của gói.
3. Tải Python 3.14.7 và Wireshark 4.6.8 (kèm Npcap) từ trang chính thức; tải Sysmon, Autoruns, Process Explorer từ `download.sysinternals.com`.
4. Ghi baseline (OS, Defender, firewall, mạng, tiến trình) trước khi tạo tình huống, sau đó thực hiện lần lượt TH1 → TH7 và B.8 theo tài liệu.

## Lỗi gặp phải và cách khắc phục

| Lỗi / hiện tượng | Cách khắc phục |
|---|---|
| Dùng VM Windows Server 2025 có sẵn thay cho Windows 11; Windows Update KB5122871 tải quá lâu | Ghi rõ build thực tế 26100.1742 và các điểm lệch trong báo cáo |
| Không có file ZIP gốc để đối chiếu SHA-256 `96236f95…` | Tính SHA-256 cho từng tệp trong `lab3_assets` và đối chiếu với bản gốc (khớp 8/8) |
| `runas`: *Unable to acquire user password* | Dùng `Start-Process cmd.exe -Credential (Get-Credential)`, vẫn sinh 4648/4624/4625 |
| Scheduled Task bị ngắt với mã `0xC000013A` | Chuyển principal sang `LogonType S4U` (chạy nền) → `LastTaskResult = 0` |
| Autoruns không hiện `LAB3_Run_Demo` | Bỏ tick *Options › Hide Windows Entries* (entry trỏ tới `notepad.exe` có chữ ký Microsoft) |
| Log Sysmon tăng khoảng 16 sự kiện/giây, sẽ ghi đè sau khoảng 45 phút | Tăng kích thước log lên 1 GB bằng `wevtutil sl … /ms:1073741824` |
| `tshark -Y 'frame contains "…"'` lỗi trong PowerShell 5.1 | Escape dấu ngoặc kép thành `\"TRAINING_ONLY\"` |
| auditpol hiểu `{GUID}` là script block; lệnh copy từ PDF bị ngắt dòng | Đặt GUID trong ngoặc kép; ghép lệnh thành một dòng trước khi chạy |

Chi tiết từng tình huống, lệnh đã chạy, 26 hình và phần trả lời 20 câu hỏi nằm trong báo cáo.

## Cấu trúc repository

```
README.md
11CPNM2-LAB3_1150080095-HuynhHuuHoang.docx   Báo cáo (Word)
11CPNM2-LAB3_1150080095-HuynhHuuHoang.pdf    Báo cáo (PDF)
Evidence/        33 tệp: baseline, log Defender/Security/Sysmon, Autoruns, pcapng,
                 kết quả TH6, cleanup_verify.txt và evidence_sha256.csv
Screenshots/     27 ảnh H1–H12 chụp trực tiếp từ VM
```

Kiểm tra lại tính toàn vẹn của bằng chứng (PowerShell):

```powershell
Import-Csv Evidence\evidence_sha256.csv | ForEach-Object {
  $f = Join-Path Evidence (Split-Path $_.Path -Leaf)
  [pscustomobject]@{ File = Split-Path $f -Leaf; OK = ((Get-FileHash $f -Algorithm SHA256).Hash -eq $_.Hash) }
}
```

## Ghi chú an toàn

- Chỉ dùng tài khoản thử nghiệm `lab3user`, đã xóa sau bài. Repo không chứa mật khẩu, token hay dữ liệu cá nhân thật.
- Tệp EICAR đã bị Defender cách ly và không có trong repo. Repo cũng không chứa installer hay executable của Sysinternals, Wireshark, Python.
- Tải thử chỉ nhắm `127.0.0.1:8080` bằng script gốc (không sửa). Dữ liệu DDoS và mail là dataset offline (TEST-NET, `.invalid`). Không thực hiện MITM, spoofing hay gửi thư thật.
