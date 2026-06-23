# Bài 3: Đọc hiểu & Dò lỗi qua Prompt (Logic Bug + Optimization)

---

## 1. Vì sao prompt thô “Mã này bị lỗi gì?” dễ khiến AI bỏ sót lỗi logic

Prompt dạng:
> “Mã này bị lỗi gì?”

### ❌ Vấn đề chính:

- **Không chỉ định loại lỗi cần tìm**
    - AI có xu hướng ưu tiên lỗi cú pháp (compile-time errors)
    - Trong khi bài này là **logic error (runtime logic bug)**

- **Không có ngữ cảnh hành vi mong đợi**
    - Không nói rõ: hàm phải tìm “phần tử trùng đầu tiên”
    - AI không biết “đầu ra đúng” là gì để so sánh

- **Không có test case phản ví dụ**
    - Ví dụ mảng `{1, 2, 3, 4}` nhưng lại trả về `1`
    - Nếu không có test case, AI khó phát hiện sai hành vi

- **Code không lỗi cú pháp**
    - Compiler không báo lỗi → AI dễ kết luận “code đúng”

➡️ Kết quả: AI thường bỏ qua bug logic do thiếu “hợp đồng hành vi (expected behavior)”.

---

## 2. Prompt tối ưu (Code Auditor + test case + yêu cầu tối ưu)

### Prompt:

Bạn hãy đóng vai trò là một **Code Auditor (chuyên gia kiểm thử logic và tối ưu thuật toán)**.

Tôi cung cấp đoạn code Java dưới đây, nhiệm vụ của bạn là:

---

## Mã nguồn cần kiểm tra:
```java
public class DuplicateFinder {

    // Lỗi logic: so sánh sai khiến kết quả luôn đúng sai lệch

    public static Integer findDuplicate(int[] arr) {

        for (int i = 0; i < arr.length; i++) {

            for (int j = i; j < arr.length; j++) {

                if (arr[i] == arr[j]) {
                    return arr[i];
                }

            }

        }

        return null;
    }
}
```

---

## Test case bắt buộc kiểm tra:

Input:
```
{1, 2, 3, 4}
```

Expected output:
```
null (không có phần tử trùng lặp)
```

Actual output hiện tại:
```
1  (SAI - logic lỗi)
```

---

## Yêu cầu:

1. Phân tích vì sao code hiện tại luôn sai logic dù không lỗi compile.
2. Chỉ ra lỗi nằm ở điều kiện vòng lặp lồng nhau.
3. Giải thích vì sao `j = i` gây ra self-comparison sai logic.
4. Viết lại thuật toán bằng **HashSet**.
5. Tối ưu độ phức tạp từ **O(N²) → O(N)**.
6. Đảm bảo:
    - Trả về phần tử trùng đầu tiên
    - Nếu không có trùng → trả về null

---

## 3. Code Java đã sửa (HashSet – tối ưu O(N))

```java
import java.util.HashSet;
import java.util.Set;

public class DuplicateFinder {

    public static Integer findDuplicate(int[] arr) {

        Set<Integer> seen = new HashSet<>();

        for (int num : arr) {

            if (seen.contains(num)) {
                return num;
            }

            seen.add(num);
        }

        return null;
    }
}
```