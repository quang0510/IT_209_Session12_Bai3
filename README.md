# Bài 3: Tách biệt luồng CI/CD đa môi trường (Staging & Production) với GitHub Environments

## 1. Giới thiệu & Cấu trúc

Dự án cấu hình kịch bản GitHub Actions tại [.github/workflows/cd-multi-env.yml](file:///D:/Desktop/IT209/ss12/Bai3/.github/workflows/cd-multi-env.yml) đáp ứng yêu cầu:
- Tách biệt 2 môi trường: **Staging** và **Production**.
- **Job `deploy-staging`**: Chạy tự động khi code được push vào nhánh `develop`.
- **Job `deploy-prod`**: Chạy khi code được push vào nhánh `main`. Yêu cầu phê duyệt qua tính năng **Environment Protection Rules** của GitHub.

---

## 2. Hướng dẫn cấu hình GitHub Environments trên Repository

### Bước 1: Tạo môi trường Staging
1. Mở GitHub Repository của bạn trên trình duyệt.
2. Điều hướng tới tab **Settings** -> chọn mục **Environments** (ở thanh menu bên trái).
3. Nhấp vào nút **New environment**.
4. Nhập tên môi trường là `staging` rồi nhấn **Configure environment**.
5. Môi trường `staging` không cần bật protection rules đặc biệt (có thể deploy tự động).

### Bước 2: Tạo môi trường Production & Bật Reviewer
1. Tiếp tục tại mục **Settings** -> **Environments**, nhấn **New environment**.
2. Nhập tên môi trường là `production` rồi nhấn **Configure environment**.
3. Tại phần **Deployment protection rules**:
   - Tích chọn ô **Required reviewers**.
   - Tìm kiếm và chọn tài khoản GitHub của bạn (hoặc nhóm reviewers).
   - *(Tùy chọn)* Nếu bạn không muốn người kích hoạt workflow tự duyệt, có thể bỏ chọn "Allow administrators to bypass configured rules".
4. Nhấn **Save protection rules**.

---

## 3. Các bước đẩy code và kiểm thử

### Bước 3: Khởi tạo Git và đẩy lên GitHub
Chạy các lệnh sau trong thư mục bài tập (nếu chưa liên kết Git):

```bash
git init
git add .
git commit -m "feat: add multi-env CI/CD workflow"
git branch -M main
git remote add origin <URL_GITHUB_REPOSITORY_CỦA_BẠN>
git push -u origin main
```

### Bước 4: Tạo nhánh `develop` để test deploy staging
```bash
git checkout -b develop
git push -u origin develop
```
- Khi push vào `develop`, kiểm tra tab **Actions**: Job `deploy-staging` sẽ chạy tự động và hoàn thành thành công.

### Bước 5: Kiểm tra phê duyệt trên môi trường Production
```bash
git checkout main
# Thực hiện một thay đổi nhỏ hoặc commit rỗng để kích hoạt push
git commit --allow-empty -m "test: trigger production deployment"
git push origin main
```
1. Truy cập tab **Actions** trên GitHub, click vào workflow vừa chạy.
2. Bạn sẽ thấy Job `deploy-prod` dừng lại ở trạng thái **Waiting for review** (hoặc thông báo màu vàng: `Pending approval`).
3. Click vào nút **Review deployments**, tích chọn `production` và chọn **Approve and deploy**.
4. Chụp lại ảnh màn hình cửa sổ/thông báo phê duyệt này để làm minh chứng nộp bài.
5. Sau khi bấm phê duyệt, Job sẽ chuyển sang trạng thái xanh (Passed/Success).

---

## 4. Sản phẩm nộp bài
1. File workflow: `.github/workflows/cd-multi-env.yml`
2. Ảnh chụp màn hình: Cửa sổ phê duyệt deployment môi trường Production (`Waiting for review` / `Approve and deploy`).
