# Sổ Tay Đồ Ăn — triển khai riêng

Mã nguồn này dùng một project Supabase riêng tên `so-tay-do-an`. Không chạy SQL hoặc đổi Auth của project app mèo `sxeslpgevuzabvetfijl`.

## Trạng thái (17/11/2026)

- Đã tạo project Supabase `so-tay-do-an` (ref `kuekxxtxtenhqpiooivz`, vùng Asia-Pacific, org `baobao`).
- Đã chạy `supabase-setup.sql`: có bảng `cats`, `cat_editors`, `cat_submissions` và bucket `cat-images`.
- Auth: đăng ký email bật, xác nhận email bật; Site URL và Redirect URL là địa chỉ GitHub Pages bên dưới.
- `index.html` đã điền URL và publishable key của project mới.
- Đã cấp quyền admin cho tài khoản quản trị; GitHub Pages đã xuất bản từ nhánh `main`.

## 1. Tạo Supabase và chạy SQL

Tạo project mới tên `so-tay-do-an`, ghi lại project ref, rồi chạy toàn bộ `supabase-setup.sql` trong SQL Editor của chính project mới đó.

Schema giữ các tên nội bộ `cats`, `cat_editors`, `cat_submissions`, bucket `cat-images` và các RPC `report_cat_status`/`is_cat_admin` để khớp với ứng dụng. Chúng thuộc database mới và không dùng chung dữ liệu với app mèo. Không đổi riêng lẻ các tên này.

SQL cấu hình quyền truy cập theo tài khoản: mọi người được xem, người đăng nhập được thêm món và sửa/xóa món của mình; admin được quản lý toàn bộ. Ảnh nằm trong bucket công khai, chỉ tài khoản đăng nhập mới được tải ảnh lên thư mục của mình.

## 2. Bật đăng ký và cấu hình Auth

Bật cho phép người dùng mới đăng ký bằng email/mật khẩu. Có thể giữ xác nhận email.

Đặt cả **Site URL** và **Redirect URLs** của project mới thành:

`https://phamduyen6611-ops.github.io/so-tay-do-an/`

Đăng ký, xác nhận email và khôi phục mật khẩu phải quay về địa chỉ đồ ăn này.

## 3. Điền cấu hình ứng dụng

Trong `index.html`, điền `supabaseUrl` và `supabaseAnonKey` từ chính project `so-tay-do-an`. Dùng publishable key hoặc anon key; không đưa service-role key hay secret key vào HTML/repo.

Kiểm tra project ref trong URL khác `sxeslpgevuzabvetfijl` trước khi mở ứng dụng có cấu hình cloud.

## 4. GitHub Pages

Tạo repo `phamduyen6611-ops/so-tay-do-an`, đẩy `index.html`, `supabase-setup.sql` và tài liệu này lên nhánh `main`.

Bật Pages từ nhánh `main`, thư mục gốc `/`. URL dự kiến:

`https://phamduyen6611-ops.github.io/so-tay-do-an/`

Chỉ coi là triển khai xong khi Pages đã xuất bản và URL trả về đúng ứng dụng đồ ăn.

## Admin ban đầu

Project mới chưa có admin. Sau khi tài khoản quản trị đăng ký và xác nhận email trong app đồ ăn, lấy UUID của tài khoản đó từ Supabase Authentication → Users và chạy:

```sql
insert into public.cat_editors (user_id, is_admin)
values ('<ADMIN_USER_UUID>', true)
on conflict (user_id) do update set is_admin = true;
```

Không dùng UUID của người dùng app mèo nếu tài khoản đó chưa tồn tại trong project mới.

## Kiểm tra sau triển khai

Kiểm tra đăng ký, email xác nhận, đăng nhập, khôi phục mật khẩu, thêm món và tải ảnh. Dùng hai tài khoản để xác nhận tài khoản thứ hai không sửa/xóa được món của tài khoản thứ nhất. Kiểm tra dấu mới xuất hiện trên một thiết bị khác và app mèo vẫn dùng project cũ.

Dữ liệu trình duyệt dùng namespace `foodMapMarkers`, `foodMapDatabase`, `foodMapLocationNamesV2` và `foodMapMigratedToCloud_…`, tách khỏi app mèo.
