# Trả lời câu hỏi bổ sung – Buổi 8

**1. Cờ ready.** Ở lần render đầu, giỏ hàng trong store còn rỗng (`[]`) còn dữ liệu cũ vẫn nằm trong AsyncStorage. Nếu bỏ điều kiện `ready`, effect trong `useAutoPersist` chạy ngay sau render đầu và ghi `[]` đè lên dữ liệu cũ, trong khi hàm `loadJSON` chưa kịp đọc xong. Đến lúc đọc xong thì dữ liệu đã bị xóa, nên giỏ luôn rỗng. Cờ `ready` chỉ bật sau khi đọc và nạp xong nên chặn được việc ghi quá sớm.

**2. Token giả.** Hai điểm không an toàn: (1) mật khẩu nằm sẵn trong `db.json` dạng văn bản thô và được so sánh ngay ở phía app, ai mở app hoặc gọi API cũng đọc được; (2) token do app tự tạo nên ai cũng có thể làm giả, server không xác thực được. Hệ thống thật thì server kiểm tra mật khẩu (đã băm) và cấp token có thời hạn (ví dụ JWT). Token nên lưu bằng SecureStore (Keychain trên iOS, Keystore trên Android) thay vì AsyncStorage dạng thô.

**3. Hai effect trong CartItemRow.** Nếu xóa effect đồng bộ `setQty(item.quantity)`, state cục bộ chỉ nhận giá trị lúc render đầu. Số trên nút sẽ lệch store khi store đổi từ nơi khác, ví dụ: nạp giỏ từ AsyncStorage sau khi mở app, bấm "Thêm vào giỏ" ở trang chi tiết (tăng số lượng cùng sản phẩm), hoặc xóa rồi thêm lại sản phẩm.

**4. Một nguồn state.** Nếu mỗi nơi dùng `useState` riêng thì hai tim độc lập: thả tim ở lưới sách, vào trang chi tiết tim vẫn trắng (và ngược lại). Khi dùng store wishlist, mọi `HeartButton` cùng đọc và ghi một danh sách `ids`, nên bấm ở đâu thì mọi nơi đều cập nhật và luôn khớp nhau.

**5. Code bổ sung (hiện/ẩn mật khẩu).** Trong `LoginScreen.tsx`:

```tsx
const [showPassword, setShowPassword] = useState(false);
// ...
<FormField label="Mật khẩu" secureTextEntry={!showPassword} ... />
<Pressable onPress={() => setShowPassword((v) => !v)} style={styles.eye}>
  <Text>👁</Text>
</Pressable>
```