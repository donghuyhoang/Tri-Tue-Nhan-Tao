# Ghi chú: Port code Pacman (Berkeley CS188) sang Python 3

Mình đã sửa các file `.py` bạn upload để chạy được trên Python 3 (đã test bằng
Python 3.12, cả chế độ text `-t` lẫn chế độ đồ họa Tkinter). File `search.py`
và `searchAgents.py` **không bị thay đổi phần thuật toán** — các đoạn
`"*** YOUR CODE HERE ***"` vẫn còn nguyên để bạn tự làm bài, mình chỉ đảm bảo
chúng import/chạy được trên Python 3.

## Các lỗi Python 2 -> 3 đã sửa

1. **`print "..."`** → `print("...")` (cú pháp print statement -> hàm) — ở
   `pacman.py`, `textDisplay.py`, `util.py`, `graphicsUtils.py`, `game.py`,
   `eightpuzzle.py`.
2. **`except X, e:`** → `except X as e:`
3. **`raise Exception, "msg"`** và **`raise "chuỗi"`** → `raise Exception("msg")`
   (Python 3 không cho raise một chuỗi hay dùng cú pháp `raise X, Y`).
4. **So sánh `!= None` / `== None`** → `is not None` / `is None`. Đây là lỗi
   nguy hiểm nhất: các class như `GameState` tự định nghĩa `__eq__`, nên
   `obj != None` trong Python 3 sẽ **gọi `__eq__` của chính đối tượng đó** và
   crash khi không tìm được lý do so sánh với `None`. Đây là nguyên nhân của
   lỗi `AttributeError: 'NoneType' object has no attribute 'data'` nếu bạn
   từng gặp.
5. **`Tkinter`** → `tkinter`; **`Tkinter.tkinter.dooneevent`** (API C-level cũ)
   → viết lại `keys_pressed`, `move_to`, `move_by`, `remove_from_screen` trong
   `graphicsUtils.py` để dùng `_root_window.tk.dooneevent(...)` — API nội bộ
   của `_tkinter` đã đổi giữa Python 2 và 3.
6. **`func.func_code`** → `func.__code__` (thuộc tính hàm đổi tên) trong
   `searchAgents.py`.
7. **`import cStringIO`** → `import io` (+ `io.StringIO`) trong `game.py`.
8. **`list.sort(cmp=...)`** (tham số `cmp` bị xoá khỏi Python 3) → dùng
   `key=...` trong `util.py` (`Counter.sortedKeys`). Cũng sửa
   `Counter.argMax` vì `dict.items()` ở Python 3 trả về view, không index
   được như list.
9. **`xrange`** → `range`, **`raw_input`** → `input`.
10. **`reduce()`** không còn là builtin → thêm `from functools import reduce`
    trong `layout.py`.
11. **`file(...)`** (builtin cũ) → `open(...)` trong `graphicsUtils.py`
    (`writePostscript`).

## ⚠️ Việc bạn cần tự làm thêm

**Thư mục `layouts/` bị thiếu.** Trong file bạn upload không có thư mục
`layouts/` (chứa các file `.lay` như `tinyMaze.lay`, `mediumMaze.lay`,
`bigMaze.lay`, `tinyCorners.lay`, `mediumCorners.lay`, `testSearch.lay`,
`trickySearch.lay`, `bigSearch.lay`, `mediumDottedMaze.lay`,
`mediumScaryMaze.lay`...). File `layout.py` sẽ tìm các file này trong
`layouts/<tên>.lay`. Không có thư mục này thì mọi lệnh `python pacman.py -l ...`
sẽ báo lỗi `"The layout X cannot be found"`.

→ Thư mục này thường nằm sẵn trong file `.zip` gốc mà giảng viên/khoá học
cung cấp (cùng cấp với `pacman.py`). Hãy tìm lại file zip gốc và copy thư mục
`layouts/` vào cùng thư mục với các file `.py` này. Nếu bạn không còn file
gốc, cho mình biết — mình có thể tạo lại các layout đơn giản để bạn test tạm
(nhưng để có đúng độ dài đường đi 130 bước ở `mediumMaze` như đề bài yêu cầu,
bạn cần đúng file layout gốc của khoá học).

**Cần cài `tkinter`** nếu muốn chạy chế độ đồ họa (mặc định, không dùng `-t`):
- Windows/Mac: thường có sẵn khi cài Python từ python.org.
- Ubuntu/Debian: `sudo apt install python3-tk`

## Cách test nhanh (khi đã có `layouts/`)

```bash
python3 pacman.py -l tinyMaze -p SearchAgent -a fn=tinyMazeSearch
python3 pacman.py -l mediumMaze -p SearchAgent -a fn=dfs
```

Trước khi bạn code `depthFirstSearch`/`breadthFirstSearch`/`uniformCostSearch`,
lệnh trên sẽ dừng đúng với thông báo `Method not implemented: ...` — đó là
dấu hiệu môi trường đã chạy tốt, chỉ còn thiếu thuật toán bạn phải tự viết
trong `search.py`.
