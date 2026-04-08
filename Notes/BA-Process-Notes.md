Cấu trúc output
Với các sản phâm đem đi triển khai:
 Contract -> SOW (channel + feature) -> List ra những chức năng base và cần customize lại (human) -> BRD SOW (nốt những phần change/not change requirement) 

Nắm danh sách overview, objective, stakeholder (để AI gen ra kế hoạch để xem blocking issue contact ai)

Overrview.md 
Document info
, project summary
, objective, main channel (back office, MB, IB ) ,danh sách use case, 
Diagram cho các luồng chính (onboarding / giao dịch ) business architecture ()
UI UX (logo như nào,theme màu gì,...) 
Use case details (mô tả từng use case 1)


Skill  
- Tách ra thành các skill cho từng nhóm BRD (Vd: trong BRD có phần nation id thì sẽ xử lý như nào)
- Ví dụ từng thị trường  - VN : phone, OCR, OTP, biometric,... tạo tài khoản số đẹp. 
- Validate :: check trùng phone, blocking 
- Pre scanning -> AML, BL, CIC ,... (check risk) - đo thang risk - đủ hết thông tin này mới gọi API này 
- Duyệt approval từ agent NFC , compliance , operation,...
Datafiled - field nào màn nào,...
First time login - 


Review
Đối với onboarding - mô tả chi tiết từng action, ví dụ sau khi nhập OTP sẽ link device với phone, sau khi nhập PIn xong sẽ lưu lại authen và update user status sang pending
Ncl các phần user status, working state, link device ,authen, pocket
BRD - onboarding đang thiếu luồng của compliance
