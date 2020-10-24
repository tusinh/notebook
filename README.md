# notebook
SSH?
SSH (Secure Shell) là một giao thức mạng dùng để thiết lập kết nối mạng một cách bảo mật.
Khi làm việc với git, ssh sẽ giúp ta trong 2 việc:

Bảo mật các kết nối của mình với server.
Không phải nhập mật khẩu mỗi lần push code.
Đây là bài viết trong loạt bài viết về git, những bài khác các bạn có thể xem tại:
Git cho người mới bắt đầu
Tạo ssh key và sử dụng ssh key trong git
Git ignore và làm việc với .gitignore
Xoá file đã tồn tại trong git và cập nhật gitignore giữa chừng dự án
git branch và mô hình pull request (Phần 1): git branch
git branch và mô hình pull request (Phần 2): pull request
git conflict và cách giải quyết
git tagging – gắn thẻ để đánh dấu commit
Git remote và việc push source code lên nhiều nơi cùng lúc
Sử dụng ssh với 2 tài khoản github cùng 1 lúc
Cơ chế làm việc
Bạn sẽ có 2 key: public key và private key. Bạn sẽ gửi public key của mình cho git server của bạn (gitlab hay github chẳng hạn).

Xong, ssh-agent sẽ làm tất cả những việc còn lại cho bạn. Mỗi lần bạn push, ssh-agent sẽ tự gửi kèm các thông tin chứng thực đi, github sẽ nhận diện ra bạn, và bạn không cần phải nhập mật khẩu nữa.

Mình giải thích một cách đơn giản nhất có thể, còn nếu bạn muốn tìm hiểu sâu hơn về cơ chế bên trong, vui lòng đọc trang wiki mà mình đưa ở đầu bài

Sinh SSH key
Bước 1: Kiểm tra xem máy bạn có ssh key nào chưa
Mở cửa sổ dòng lệnh (terminal) và chạy lệnh:

$ ls -al ~/.ssh
1
$ ls -al ~/.ssh
Lệnh trên sẽ kiểm tra trong thư mục .ssh (nằm ở thư mục gốc của user bạn đang đăng nhập vào máy, vd trên Mac: /Users/binh/.ssh) có ssh key nào chưa, mặc định, các ssh key thường sẽ có dạng:

id_rsa
id_rsa.pub
id_dsa.pub
id_ecdsa.pub
id_ed25519.pub
1
2
3
4
5
id_rsa
id_rsa.pub
id_dsa.pub
id_ecdsa.pub
id_ed25519.pub
public key sẽ có đuôi .pub (id_rsa.pub), private key thì không có đuôi (id_rsa)
Nếu có một cặp ssh key nào trong thư mục này (giả sử là id_rsa và id_rsa.pub), bạn có thể bỏ qua Bước 2 và chuyển thẳng sang Bước 3.

Bước 2: Sinh một SSH key mới
Chạy lệnh sau trên terminal
ssh-keygen -t rsa -b 4096 -C "email_cua_ban@example.com"
1
ssh-keygen -t rsa -b 4096 -C "email_cua_ban@example.com"
Để tránh phiền phức sau này, mình khuyên bạn nên để các cài đặt ở mặc định, như lần này, ssh-agent hỏi bạn muốn lưu key của mình ở đâu thì bạn cứ thế mà Enter thôi:
Enter file in which to save the key (/Users/binhcq/.ssh/id_rsa): [Press enter]
1
Enter file in which to save the key (/Users/binhcq/.ssh/id_rsa): [Press enter]
Tiếp đến thì nhập mật khẩu cho key của bạn
Enter passphrase (empty for no passphrase): [Type a passphrase]
# Enter same passphrase again: [Type passphrase again]
1
2
Enter passphrase (empty for no passphrase): [Type a passphrase]
# Enter same passphrase again: [Type passphrase again]
Lưu ý mật khẩu khi bạn gõ vào nó sẽ không hiển thị mấy dấu *** như bình thường, nhưng bạn cứ gõ xong rồi Enter thôi. Thêm nữa, bạn nên chọn một mật khẩu ĐỦ MẠNH cho mình, bạn có thể xem thêm lý do tại đây

Sau khi nhập mật khẩu, bạn sẽ nhận được thông báo về việc mật khẩu đã lưu vào địa chỉ lúc nãy bạn chỉ định:
Your identification has been saved in /Users/you/.ssh/id_rsa.
# Your public key has been saved in /Users/you/.ssh/id_rsa.pub.
# The key fingerprint is:
# 01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db email_cua_ban@example.com
1
2
3
4
Your identification has been saved in /Users/you/.ssh/id_rsa.
# Your public key has been saved in /Users/you/.ssh/id_rsa.pub.
# The key fingerprint is:
# 01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db email_cua_ban@example.com
Bước 3: Thêm key của bạn vào ssh-agent
ssh-agent là trình quản lý ssh key của bạn, công việc của nó thì nãy mình có nói qua ở trên rồi đó.

Đảm bảo rằng ssh-agent đã được kích hoạt bằng lệnh:
eval "$(ssh-agent -s)"
# Agent pid 59566
1
2
eval "$(ssh-agent -s)"
# Agent pid 59566
Add ssh key của bạn vào ssh-agent
ssh-add ~/.ssh/id_rsa
1
ssh-add ~/.ssh/id_rsa
Lưu ý id_rsa chính là private key của bạn, nếu ở bước 2, bạn có key khác thì thay tên key tương ứng vào.

Bước 4: Thêm ssh public key vào tài khoản trên server của bạn (github, gitlab...)
Copy ssh key vào clipboard:

pbcopy < ~/.ssh/id_rsa.pub
1
pbcopy < ~/.ssh/id_rsa.pub
Sau đó lên tài khoản của bạn, vào mục setting, tìm tới mục Add ssh key và dán nội dung đã copy lúc nãy vào:


Bước 5: Kiếm tra lại xem mọi thứ đã OK chưa:
ssh -T git@gitlab.com
1
ssh -T git@gitlab.com
Với github thì là ssh -T git@github.com
Có thể bạn sẽ nhận được thông báo về việc thêm host gitlab vào danh sách tin cậy:

The authenticity of host gitlab.com (207.97.227.239) can't be established.
# RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
# Are you sure you want to continue connecting (yes/no)?
1
2
3
The authenticity of host gitlab.com (207.97.227.239) can't be established.
# RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
# Are you sure you want to continue connecting (yes/no)?
Bạn chỉ việc gõ yes vào terminal rồi Enter là được.
Và bạn sẽ nhận được dòng thông báo thành công:

Welcome to GitLab, @binh.cao!
1
Welcome to GitLab, @binh.cao!
Hoặc github:

Hi BinhCQ! You've successfully authenticated, but GitHub does not
# provide shell access.
1
2
Hi BinhCQ! You've successfully authenticated, but GitHub does not
# provide shell access.
OK, đến đây là bạn đã có thể sử dụng link ssh rồi.

Thêm chút nữa:
Vậy nếu cùng lúc tôi muốn dùng ssh với hai tài khoản của cùng một server (github chẳng hạn) có được không?
Được, nhưng bạn phải sinh 2 ssh key, để biết thêm chi tiết, vui lòng đọc bài viết Sử dụng ssh với 2 tài khoản github cùng 1 lúc của mình.
Còn nếu tôi muốn cùng lúc đưa code của mình lên hai server khác nhau?
Hoàn toàn được, bạn vui lòng đọc bài viết Git remote và việc push source code lên nhiều nơi cùng lúc của mình.
Vậy là với ssh, từ nay bạn sẽ giảm được một phần thao tác rườm ra khi làm việc với git, và tập trung vào chuyên môn của mình hơn 😛

Happy coding!

- source : https://nhatky.dev/2019/06/08/tao-ssh-key-va-su-dung-ssh-key-trong-git/
