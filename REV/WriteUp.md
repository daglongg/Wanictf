![image](https://github.com/daglongg/Wanictf/assets/138242812/d8286ed8-92df-44d4-b4b0-71b8cebd3998)## lambda

![image](https://github.com/daglongg/Wanictf/assets/138242812/7634899f-9e4d-48cc-b2df-20d580a9118d)

Ngay từ đề bài ta có thể thấy tác giả đã cho chúng ta biết hint là file python. Mở ra và tôi thấy 

![image](https://github.com/daglongg/Wanictf/assets/138242812/3d697d20-f81e-4306-9ba8-aef74dcd4eef)

Đọc qua thì thấy đoạn mã này thực hiện các bước sau để kiểm tra flag:

* Lấy đầu vào từ người dùng.
* Thực hiện một chuỗi các phép biến đổi phức tạp trên chuỗi bí ẩn "16_10_13_x_6t_4_1o_9_1j_7_9_1j_1o_3_6_c_1o_6r":
* Chuyển đổi từ hệ cơ số 36.
* Phép XOR với 123.
* Điều chỉnh giá trị ASCII của các ký tự.
* So sánh kết quả giải mã với chuỗi đầu vào của người dùng.
* Thông báo kết quả kiểm tra.

Tôi sẽ viết lại 1 script để bypass nó.

```
def decode(encoded_string):
    # Split the encoded string and decode from base 36 and add 10
    parts = encoded_string.split('_')
    decoded_chars = [chr(int(part, 36) + 10) for part in parts]
    decoded_string = ''.join(decoded_chars)

    # Perform the XOR with 123
    step1 = ''.join(chr(123 ^ ord(c)) for c in decoded_string)
    
    # Subtract 12 from ASCII value
    step2 = ''.join(chr(ord(c) - 12) for c in step1)
    
    # Add 3 to ASCII value
    flag = ''.join(chr(ord(c) + 3) for c in step2)
    
    return flag

encoded_string = '16_10_13_x_6t_4_1o_9_1j_7_9_1j_1o_3_6_c_1o_6r'
flag = decode(encoded_string)
print("Decoded flag:", flag)
```
Và tôi có flag của bài này là `FLAG{l4_1a_14mbd4}`

# home

![image](https://github.com/daglongg/Wanictf/assets/138242812/66e3a6d1-4d0a-4453-9b03-eda0b5897446)

Kiểm tra file 

![image](https://github.com/daglongg/Wanictf/assets/138242812/1c31df6a-6b9d-41b7-b5d7-8ec740eb1a43)

Ở Hàm strstr kiểm tra xem buf có chứa chuỗi "Service" hay không. Nếu có, đoạn mã trong khối if sẽ được thực thi. 
![image](https://github.com/daglongg/Wanictf/assets/138242812/1c8fe92b-abcd-463e-ac03-7ff6b65b019a)

Tập trung vào hàm `constructFlag()` tôi thấy hàm này đang tính toán là sẽ trả ra flag trên men. vậy tôi sẽ debug để tìm ra flag

![image](https://github.com/daglongg/Wanictf/assets/138242812/cd44a108-8566-49ec-8479-9cb3a97dab61)

và flag: `FLAG{How_did_you_get_here_4VKzTLibQmPaBZY4}`

# Thread

![image](https://github.com/daglongg/Wanictf/assets/138242812/e4f5826b-226f-439f-a337-f2df90a9a619)

Kiểm tra file 

![image](https://github.com/daglongg/Wanictf/assets/138242812/c3c1762d-e07b-4acc-9282-97a8e35a6f11)

Bài này ta sẽ thấy rằng chương trình yêu cầu ta nhập flag. Nếu đúng sẽ trả ra `Correct` còn nếu sai thì ngược lại `Incorrect`.

![image](https://github.com/daglongg/Wanictf/assets/138242812/399625d9-2606-4101-a48d-9928792e55c3)

Tập trung vào đoạn code này

```
for ( j = 0; j <= 44; ++j )
      {
        v8[j] = j;
        pthread_create(&th[j], 0LL, start_routine, &v8[j]);
      }
```
và hàm này `start_routin`
```
void *__fastcall start_routine(int *a1)
{
  int v2; // [rsp+14h] [rbp-Ch]
  int v3; // [rsp+18h] [rbp-8h]
  int v4; // [rsp+1Ch] [rbp-4h]

  v3 = *a1;
  v2 = 0;
  while ( v2 <= 2 )
  {
    pthread_mutex_lock(&mutex);
    v4 = (dword_4200[v3] + v3) % 3;
    if ( !v4 )
      dword_4140[v3] *= 3;
    if ( v4 == 1 )
      dword_4140[v3] += 5;
    if ( v4 == 2 )
      dword_4140[v3] ^= 0x7Fu;
    v2 = ++dword_4200[v3];
    pthread_mutex_unlock(&mutex);
  }
  return 0LL;
}
```
Bản chất của đoạn code này là sẽ 2 vòn for lồng nhau. giá trị của v4 sẽ chạy luân phiên từ 0 tới 2. Sau đó sẽ nhân, cộng và xor. Ở mỗi giá trị sẽ làm điều đó 3 lần như vậy. Ở đây tôi sẽ viết 1 đoạn scrip cơ bản để bypass chỗ này 
```
from threading import Thread
input_values = [
    0xA8, 0x8A, 0xBF, 0x0A5, 0x2FD, 0x59, 0xDE, 0x24,
    0x65, 0x10F, 0xDE, 0x23, 0x15D, 0x42, 0x2C, 0xDE,
    0x09, 0x65, 0xDE, 0x51, 0xEF, 0x13F, 0x24, 0x53,
    0x15D, 0x48, 0x53, 0xDE, 0x09, 0x53, 0x14B, 0x24,
    0x65, 0xDE, 0x36, 0x53, 0x15D, 0x12, 0x4A, 0x124,
    0x3F, 0x5F, 0x14E, 0xD5, 0x0B
]

# Mảng để theo dõi số lần thực hiện phép toán
operation_counts = [3] * 45

def perform_operations(index):
    for _ in range(3):
        operation_counts[index] -= 1
        operation_type = (operation_counts[index] + index) % 3
        if operation_type == 0:
            input_values[index] //= 3
        elif operation_type == 1:
            input_values[index] -= 5
        else:
            input_values[index] ^= 0x7F

# Tạo và khởi động các thread
threads = [Thread(target=perform_operations, args=(i,)) for i in range(45)]
for thread in threads:
    thread.start()
for thread in threads:
    thread.join()

# Chuyển đổi các giá trị thành chuỗi và in ra kết quả
decoded_flag = "".join(chr(item) for item in input_values)
print(decoded_flag)
```
Flag: `FLAG{c4n_y0u_dr4w_4_1ine_be4ween_4he_thread3}`






