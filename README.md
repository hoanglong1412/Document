# 🚀 Git Pro Command Cheat Sheet

Một bộ lệnh Git “xịn xò” dành cho dev muốn thao tác nhanh – chuẩn – không sai vào đâu được.

---

## 🧩 1. Rebase

### Rebase branch hiện tại lên `main`

```
git checkout feature-branch
git rebase main
```

### Rebase interactive (sửa, gộp, xoá commit)

```
git rebase -i HEAD~5
```

---

## 🔄 2. Pull Rebase

### Pull nhưng không tạo merge commit

```
git pull --rebase
```

### Set mặc định cho mọi repo

```
git config --global pull.rebase true
```

---

## ✏️ 3. Rename Commit

### Đổi message commit gần nhất

```
git commit --amend -m "New commit message"
```

### Đổi commit cũ hơn

```
git rebase -i HEAD~3
# đổi 'pick' -> 'reword'
```

---

## 🕵️ 4. Reflog

### Xem toàn bộ lịch sử (kể cả commit bị xoá)

```
git reflog
```

### Quay về trạng thái bất kỳ

```
git reset --hard <hash>
```

---

## 📦 5. Export Source

### Export toàn bộ source (không .git)

```
git archive --format=zip HEAD -o source.zip
```

### Export 1 folder cụ thể

```
git archive -o ui.zip HEAD ui/
```

---

## 🛠️ 6. Edit Commit

### Sửa commit gần nhất

```
git commit --amend
```

### Sửa commit cũ

```
git rebase -i HEAD~4
# đổi 'pick' -> 'edit'
```

---

## 📂 7. Checkout Path

### Lấy file từ branch khác

```
git checkout main -- src/utils.cpp
```

### Lấy file từ commit cũ

```
git checkout <hash> -- config.json
```

---

## 🧵 8. Create Patch

### Tạo patch cho 1 commit

```
git format-patch -1 <hash>
```

### Tạo patch cho nhiều commit

```
git format-patch main
```

### Apply patch

```
git apply xxx.patch
```

---

## 🎁 9. Bundle

### Tạo bundle chứa toàn repo

```
git bundle create repo.bundle --all
```

### Clone từ bundle

```
git clone repo.bundle -b main
```

### Fetch từ bundle

```
git fetch repo.bundle main
```

---

## 🧠 Tips Nhanh Cho Dân Pro

* Rebase để lịch sử sạch và thẳng.
* Commit nhỏ – dễ review – dễ revert.
* Reflog là “chiêu hồi” cứu repo khi lỡ tay xoá commit.
* Patch & Bundle cực hữu dụng khi làm việc offline.
