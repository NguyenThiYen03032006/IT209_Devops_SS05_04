# Bài 4: Mô phỏng quy trình Hotfix & Gitflow thực tế

## 1. Mục tiêu

Thực hành mô hình phân nhánh Gitflow với quy trình xử lý lỗi khẩn cấp trên hệ thống production.

Các nội dung thực hiện:

* Sử dụng nhánh `main` cho phiên bản production ổn định.
* Sử dụng nhánh `develop` cho quá trình phát triển tính năng mới.
* Tạo nhánh `hotfix/v1.0.1` trực tiếp từ `main`.
* Sửa lỗi nghiêm trọng trên nhánh hotfix.
* Merge hotfix vào `main`.
* Tạo tag `v1.0.1` cho phiên bản release.
* Merge ngược hotfix vào `develop`.
* Xóa nhánh hotfix sau khi hoàn thành.

---

## 2. Bối cảnh

Hệ thống đang chạy trên nhánh `main` với phiên bản ổn định `v1.0.0`.

Trong quá trình vận hành phát hiện một lỗi nghiêm trọng liên quan đến việc lộ dữ liệu người dùng.

Trong khi đó, nhánh `develop` đang phát triển các tính năng mới và chưa thể deploy lên production.

Do đó cần tạo một nhánh hotfix trực tiếp từ `main` để sửa lỗi khẩn cấp mà không ảnh hưởng đến các tính năng đang phát triển trên `develop`.

---

## 3. Các nhánh sử dụng

| Nhánh           | Mục đích                                          |
| --------------- | ------------------------------------------------- |
| `main`          | Chứa code production ổn định                      |
| `develop`       | Chứa code đang phát triển cho phiên bản tương lai |
| `hotfix/v1.0.1` | Chứa bản sửa lỗi khẩn cấp cho production          |

---

## 4. Quy trình thực hiện

### Bước 1: Khởi tạo repository

Repository được khởi tạo và sử dụng nhánh chính:

```bash
git init
git branch -M main
```

---

### Bước 2: Tạo phiên bản ổn định v1.0.0

Tạo commit đầu tiên trên `main`:

```bash
git add .
git commit -m "Release version 1.0.0"
```

Commit này đại diện cho phiên bản production `v1.0.0`.

---

### Bước 3: Tạo nhánh develop

Tạo nhánh phát triển từ `main`:

```bash
git checkout -b develop
```

Sau đó thực hiện một số commit mô phỏng việc phát triển tính năng mới:

```bash
git add .
git commit -m "Add new booking feature"

git add .
git commit -m "Update new booking feature"
```

Như vậy `develop` đã có lịch sử phát triển riêng và khác với `main`.

---

### Bước 4: Tạo nhánh hotfix từ main

Khi phát hiện lỗi nghiêm trọng trên production, chuyển về `main`:

```bash
git checkout main
```

Sau đó tạo nhánh hotfix trực tiếp từ `main`:

```bash
git checkout -b hotfix/v1.0.1
```

Điều này đảm bảo hotfix được xây dựng dựa trên phiên bản production hiện tại thay vì dựa trên code chưa hoàn thiện của `develop`.

---

### Bước 5: Sửa lỗi bảo mật

Thực hiện sửa lỗi liên quan đến việc lộ dữ liệu người dùng.

Commit bản sửa lỗi:

```bash
git add .
git commit -m "Fix critical user data exposure vulnerability"
```

---

### Bước 6: Merge hotfix vào main

Chuyển về `main`:

```bash
git checkout main
```

Merge hotfix:

```bash
git merge hotfix/v1.0.1
```

Sau thao tác này, bản sửa lỗi đã được đưa vào nhánh production.

---

### Bước 7: Tạo tag v1.0.1

Tạo tag cho phiên bản hotfix:

```bash
git tag -a v1.0.1 -m "Release Hotfix 1.0.1"
```

Kiểm tra:

```bash
git tag
```

Kết quả:

```text
v1.0.1
```

Tag `v1.0.1` trỏ tới commit sửa lỗi trên `main`.

---

### Bước 8: Merge hotfix ngược về develop

Sau khi release production, bản sửa lỗi phải được đồng bộ về nhánh phát triển.

Chuyển sang `develop`:

```bash
git checkout develop
```

Merge hotfix:

```bash
git merge hotfix/v1.0.1
```

Thao tác này đảm bảo các phiên bản phát triển trong tương lai cũng chứa bản sửa lỗi bảo mật.

---

### Bước 9: Xóa nhánh hotfix

Sau khi hotfix đã được merge vào cả `main` và `develop`, xóa nhánh hotfix:

```bash
git branch -d hotfix/v1.0.1
```

Việc xóa nhánh chỉ xóa tên nhánh, không xóa các commit đã được merge và cũng không xóa tag `v1.0.1`.

---

## 5. Kiểm tra branch

Sử dụng:

```bash
git branch -a
```

Kết quả thực tế:

```text
* develop
  main
```

Nhánh `hotfix/v1.0.1` đã được xóa sau khi hoàn thành quy trình.

---

## 6. Kiểm tra tag

Sử dụng:

```bash
git tag
```

Kết quả:

```text
v1.0.1
```

Tag `v1.0.1` được sử dụng để đánh dấu phiên bản production sau khi sửa lỗi.

---

## 7. Kiểm tra lịch sử commit

Sử dụng:

```bash
git log --graph --oneline --all --decorate
```

Kết quả thực tế:

```text
*   XXXXXXX (HEAD -> develop) Merge branch 'hotfix/v1.0.1' into develop
|\
| * XXXXXXX (tag: v1.0.1, main) Fix critical user data exposure vulnerability
* | XXXXXXX Update new booking feature
* | XXXXXXX Add new booking feature
|/
* XXXXXXX Release version 1.0.0
```

Kết quả cho thấy:

* `main` đã nhận commit sửa lỗi.
* Tag `v1.0.1` trỏ tới commit sửa lỗi.
* `develop` đã merge bản sửa lỗi từ hotfix.
* Nhánh `hotfix/v1.0.1` đã được hoàn thành và xóa.

---

## 8. Sơ đồ Gitflow

```text
                           develop
                              |
                              ● Update new booking feature
                              |
                              ● Add new booking feature
                             / \
                            /   \
                           /     ● Merge hotfix into develop
                          /      |
                         /       |
●-----------------------●--------●
Release v1.0.0          |
main                    |
                        ● Fix critical user data exposure
                        ↑
                        |
                  hotfix/v1.0.1
                        |
                        ●
                        |
                   (tag v1.0.1)

Quy trình:

main
  ↓
hotfix/v1.0.1
  ↓
Fix lỗi
  ↓
merge → main
  ↓
tag v1.0.1
  ↓
merge → develop
```

---

## 9. Kết luận

Quy trình Hotfix đã được thực hiện theo mô hình Gitflow:

1. `hotfix/v1.0.1` được tạo trực tiếp từ `main`.
2. Lỗi nghiêm trọng được sửa trên nhánh hotfix.
3. Hotfix được merge vào `main`.
4. Phiên bản mới được đánh dấu bằng tag `v1.0.1`.
5. Hotfix được merge ngược vào `develop`.
6. Nhánh hotfix được xóa sau khi hoàn tất.

Việc merge hotfix về `develop` giúp đảm bảo lỗi đã được sửa trên production không xuất hiện trở lại trong các phiên bản tương lai.
