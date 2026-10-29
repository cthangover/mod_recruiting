# Recruiting — kẻ địch bị đánh bại gia nhập đội

Recruiting cho phép bắt giữ kẻ địch vừa bị đánh bại trong chiến đấu, để sau đó chiến đấu cho phía người chơi. Sau một chiến thắng, mỗi kẻ địch bị đánh bại đều có cơ hội bị bắt; các nhân vật bị bắt được liệt kê trong bảng **Người được chiêu mộ**, và bất kỳ ai trong số họ cũng có thể thêm vào đội hoặc rút ra. Trong lúc bị giam giữ, người được chiêu mộ sống theo quy tắc riêng của mình — trong Cthangover thì nó sẽ đói.

Mod không phụ thuộc hệ thống chiến đấu nào: nó lắng nghe sự kiện “nhân vật bị đánh bại” của nền tảng, nên hoạt động với FF Battle, Chiến đấu thẻ bài hay bất cứ thứ gì báo cáo nhân vật bị đánh bại. Ai có thể bị bắt, và với điều kiện gì, do dữ liệu của trò chơi và các mod khác quyết định.

## Bảng Người được chiêu mộ

Mở bằng nút trên thanh công cụ. Đầu game Cthangover thì chưa bắt được ai:

![Bảng Người được chiêu mộ: tiêu đề “Người được chiêu mộ”, dòng “Kẻ thù bị bắt · bất kỳ ai cũng có thể gia nhập tổ đội”, “Chưa bắt được ai” và dưới cùng “Đã bắt: 0 · trong tổ đội: 0”.](assets/recruits-panel.jpg)

Mỗi người được bắt sẽ có một hàng với chân dung, tên và máu, kèm một nút: **Thêm vào tổ đội** hoặc **Rời tổ đội**. Phần chân đếm số người đã bắt và bao nhiêu đang trong đội.

## Một lần bắt diễn ra thế nào

Sau chiến thắng, với mỗi kẻ địch bị đánh bại:

1. Tệp nhân vật của địch phải có `RecruitmentChance` lớn hơn 0.
2. Mỗi loại địch chỉ một người được bắt: nếu loại đó đã có người bị bắt, bỏ qua.
3. Mọi **điều kiện** đã đăng ký đều phải cho phép. Mod này có sẵn một điều kiện: người chơi phải có kỹ năng `slave_master` (trong Cthangover, Marao có sẵn ngay từ đầu).
4. Một lần gieo từ 1 đến 100 không được vượt quá tỉ lệ.

Đạt đủ rồi thì kẻ địch vào danh sách với 10 máu. Trong Cthangover, nhân vật duy nhất có thể bị bắt sẽ xuất hiện về sau trong cốt truyện, với tỉ lệ 50%:

```json
{ "Id": "werewolf_girl_1", "RecruitmentChance": 50 }
```

## Trong tổ đội

**Thêm vào tổ đội** biến người được bắt thành một thành viên bình thường: xuất hiện trên bảng nhân vật, chiến đấu bằng hành động riêng, có thể là mục tiêu của vật phẩm, và được lưu cùng tổ đội. Máu đã mất trong trận sẽ ở lại với người đó. **Rời tổ đội** giữ lại hành động và kinh nghiệm cho lần gia nhập sau.

## Nhu cầu của người được chiêu mộ

Mỗi người được bắt chạy các **hành vi** đã đăng ký ở mỗi nhịp của đồng hồ trò chơi. Mod này có sẵn một — dinh dưỡng: người chưa ăn suốt 24 giờ trò chơi sẽ mất 1 máu mỗi nhịp. Trò chơi hiện tại vẫn chưa có gì để cho người được bắt ăn, nên kẻ vừa bị bắt sẽ bắt đầu đói sau một ngày.

Danh sách, máu và trạng thái hành vi được lưu cùng trò chơi.

## Dành cho tác giả mod

- **Điều kiện** là một lớp cài đặt `IRecruitCondition`; mod recruiting tìm thấy nó trong bất kỳ mod nào đã nạp. Trả về false để cấm việc bắt.
- **Hành vi** cài đặt `IRecruitBehaviour`: được cấu hình khi có người bị bắt, được gọi mỗi nhịp, và bị gỡ cùng với người đó. Trạng thái của nó nằm trong thuộc tính của người được bắt.
- Mã nguồn có thể phản ứng với `RecruitJoinedEvent` và `RecruitLeftEvent`.

## Nếu có trục trặc

1. **Chẳng ai bị bắt bao giờ.** `RecruitmentChance` của địch là 0, một điều kiện cấm (người chơi thiếu kỹ năng `slave_master`), hoặc lần gieo thất bại.
2. **Cùng một kẻ địch không bị bắt lần hai.** Mỗi loại địch một người, cố ý vậy.
3. **Máu của người được bắt cứ rơi.** Đó là dinh dưỡng: nó đã nhịn một ngày, mà chưa có cách nào cho nó ăn.
