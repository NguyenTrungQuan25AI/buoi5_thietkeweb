# Week 5 — Prompt tinh chỉnh tốc độ và độ mượt chuyển động

Các prompt dưới đây được soạn dựa trên `quan_homework.html` và `quan_homework.css`. Trang hiện có hiệu ứng AOS khi cuộn, chuyển trạng thái menu/card/nút, cuộn mượt, hiệu ứng pulse và hiệu ứng chữ tự gõ theo nhiều nhịp.

## Prompt 1 — Rà soát và cân chỉnh chuyển động toàn trang

> Hãy đọc `quan_homework.html` và `quan_homework.css`, sau đó tinh chỉnh tốc độ và độ mượt của các chuyển động trên trang portfolio. Hiện AOS đang dùng `duration: 400` và `ease-out-cubic`; CSS có các transition nhanh, transition thường, hiệu ứng menu/card, FAB pulse và hiệu ứng chữ tự gõ kéo dài theo nhiều mốc thời gian.
>
> Mục tiêu là tạo cảm giác phản hồi nhanh nhưng không giật, không làm các hiệu ứng chồng chéo hoặc khiến trang có vẻ chậm. Hãy rà soát thời lượng, easing, delay và thứ tự xuất hiện của từng nhóm hiệu ứng; dùng easing phù hợp với mục đích của hiệu ứng thay vì thay hàng loạt bằng một giá trị chung. Đặc biệt, không làm mất nhịp gõ từng ký tự của hiệu ứng typewriter.
>
> Chỉ sửa các thuộc tính chuyển động cần thiết trong hai tệp trên. Giữ nguyên nội dung, bố cục, màu sắc và hành vi tương tác hiện tại. Không thêm thư viện mới. Giữ hỗ trợ `prefers-reduced-motion` để người dùng chọn giảm chuyển động vẫn không phải chờ các hiệu ứng.

## Prompt 2 — Tinh chỉnh thời gian và cảm giác phản hồi

> Tiếp tục kiểm tra các thời lượng sau khi cân chỉnh. Các thao tác do người dùng kích hoạt (mở menu, hover/focus, lật card) cần phản hồi sớm và dứt khoát; hiệu ứng xuất hiện khi cuộn cần đủ thời gian để nhìn thấy nhưng không tạo cảm giác trễ; hiệu ứng pulse và typewriter vẫn cần có nhịp dễ chịu.
>
> Điều chỉnh riêng duration, easing và delay theo từng nhóm. Tránh dùng `transition: all` nếu có thể chỉ định đúng thuộc tính cần chuyển động. Không rút ngắn đồng loạt các bước `steps()` của typewriter đến mức chữ khó đọc hoặc hiệu ứng không còn giống gõ chữ. Hãy bảo đảm menu đóng/mở, card lật, nút liên hệ, hiệu ứng AOS và trạng thái reduced-motion đều hoạt động như trước.

## Prompt 3 — Kiểm tra kết quả sau chỉnh sửa

> Hãy rà soát lại các thay đổi chuyển động trong `quan_homework.html` và `quan_homework.css`. Xác nhận các transition/animation có duration và easing hợp lý, các delay không làm nội dung chính xuất hiện quá muộn, hiệu ứng không gây tràn ngang hoặc làm thay đổi layout, và `prefers-reduced-motion` vẫn tắt hoặc giảm chuyển động đầy đủ.
>
> Nếu phát hiện một hiệu ứng quá nhanh, quá chậm, giật hoặc bị chồng với hiệu ứng khác, hãy chỉnh đúng hiệu ứng đó thay vì tăng/giảm thời gian toàn cục. Tóm tắt ngắn gọn những thuộc tính chuyển động đã đổi và lý do.

## Tiêu chí mong muốn

- Tương tác phản hồi nhanh, chuyển động có điểm dừng tự nhiên và không gây cảm giác giật.
- AOS, menu/card, FAB và typewriter giữ được vai trò riêng; không áp dụng cùng một tốc độ/easing cho mọi hiệu ứng.
- Không trì hoãn việc đọc nội dung vì các hiệu ứng tuần tự.
- Không thay đổi nội dung hoặc bố cục của bài tập.
- Tôn trọng thiết lập giảm chuyển động của hệ điều hành.
