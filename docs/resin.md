# 아파치 레진 연동

### 아파치 컴파일
    yum install httpd-devel

apxs 설치 경로를 찾아준다.

    whereis apxs

그리고 apxs 경로를 configure 할때 넣습니다.

    ./configure --with-apxs=/usr/bin/apxs

위 명령어를 실행 시켜줍니다.

    make 
    make install

다음으로 

    ps aux | grep resin
레진이 작동하는지 확인 하고
레진이 작동하지 않는 다면 resin.sh 가 있는 경로를 찾아서 레진을 실행시켜줍니다.

    sh resin.sh start

### resin.xml 수정


    <host id="" root-directory=".">
      <!--
         - webapps can be overridden/extended in the resin.xml
        -->
         <!-- <web-app id="/" root-directory="webapps/ROOT"/>-->
         <web-app id="/" document-directory="/var/www/html"/>

    </host>

apache의 document 디렉터리 경로를 설정해줍니다.
다음으로 resin.sh 경로에서 resin.sh 를 시작합니다.

    sh resin.sh start

/var/www/html 아래에 dondon.jsp를 만들고 아래의 내용을 넣습니다.

    <%@ page contentType="text/html; charset=utf-8" %>
    <%out.println("대표님 저랑 민이형은 화면 나오는거 까지는 완료했습니다!");%>

malgnigd.ze.to/dondon.jsp 에서 다음과 같이 출력되면 됩니다.
![이미지1](/img/그림7.png)


