# Virtual host

## 1. Virtual host?

가상 호스트 서버를 이용하여 제한된 공간 안에서 여러 개의 홈페이지를 제공해주는 서비스가 (웹, 메일 등) 호스팅 서비스 입니다.

## 2. Virtual host 설정

/etc/httpd/conf/httpd.conf 에서 
virtual host 태그를 넣어 정의 합니다.

    <VirtualHost *:80>
        ServerName      donvhostest.com
        ServerAdmin     igd@malgnsoft.com
        DocumentRoot    "/var/www/html/test"
        <Directory "/var/www/html/test">
            AllowOverride All
        </Directory>
    </VirtualHost>

## 2-1.

AWS 에서 80 port에 대한 인바운드 보안 규칙을 설정합니다.
![이미지1](/img/그림2.png)


Windows/System32/Drivers/etc/hosts 에 aws의 public ipv4 ip 주소와 donvhostest.com를 적어줍니다.

    54.180.145.184 donvhostest.com

donvhostest.com 디렉터리에 간단한 html 화면을 만들어 줍니다.

그 다음 접속 했을 때 화면은 아래의 그림 과 같습니다.

![이미지3](/img/그림3.png)

왼쪽은 아이피 주소로 접속 했고, 오른쪽은 도메인 주소로 접속 했습니다.


    

