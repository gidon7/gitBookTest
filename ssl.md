# SSL 모듈 설치 및 환경설정, Letsencrypt 인증서 설치

## 1. SSL 모듈 설치 및 환경설정

	$ sudo systemctl start httpd && sudo systemctl enable httpd

Apache를 시작한 다음 시스템 부팅시 마다 시작하도록 설정합니다.

	$ sudo yum update -y
	$ sudo yum install -y mod_ssl

모든 소프트웨어 패키지가 최신 상태로 업데이트 합니다. 그리고 Apache module (mod_ssl)을 설치하여 TLS지원을 추가합니다.

/etc/httpd/conf.d/ssl.conf : mod_ssl의 구성 파일, Apache에 암호화 키 및 인증서의 위치, 허용하는 TLS 프로토콜 버전, 허용하는 암호화 암호를 알려주는 명령이 포함되어 있다.

	$ cd /etc/pki/tls/certs
	$ sudo ./make-dummy-cert localhost.crt

자체 서명된 인증서와 키를 생성합니다. localhost.crt 파일이 /etc/pki/tls/certs 에 생성 됩니다. 
자체 서명된 인증서에도 키가 포함 되어 있어서 /etc/httpd/conf.d/ssl.conf 파일을 열어서 SSLCertificateKeyFile 부분을 주석처리 합니다.
주석처리를 하지 않으면 Apache 서비스가 시작되지 않습니다.

	$ sudo apachectl restart

아파치 서버를 재시작하고 https:// 주소로 접속이 가능하면 성공입니다. 하지만 신뢰할 수 없는 자체 서명된 호스트 인증서를 사용하여 사이트에 연결하기 때문에 브라우저에 보안 경고가 연속으로 표시 될 수 있습니다.

## 2. letsencrypt

	$ sudo yum-config-manager --enable epel*
	$ sudo yum repolist all

epel package를 사용 가능한 상태로 만들고 epel이 활성화 되었는지 확인 합니다.

	<VirtualHost *:80>
		DocumentRoot "/var/www/html"
		ServerName "malgnigd.ze.to"
		ServerAlias "www.malgnigd.ze.to"
	</VirtualHost>

httpd.conf 에 추가합니다.

	$ sudo apachectl restart

아파치를 재시작 해줍니다.

#### Certbot 설치 및 인증서 발급 받기

	$ sudo amazon-linux-extras install epel -y

epel용 Amazon Extras 저장소를 설치합니다.

	$ sudo yum install -y certbot python2-certbot-apache

Certbot 패키지 및 종속성을 설치합니다.

	$ sudo certbot --webroot --installer apache -w /var/www/html/ -d malgnigd.ze.to

webroot 방식으로 진행 했을 경우에 도메인 인증을 위해 외부에서 접근 가능한 경로를 제공하고, let's Encrypt 에서 해당 경로로 접속해 인증을 하는 방식입니다.
standalone방식과 다르게 서비스 종료는 하지 않아도 됩니다.(서버를 내려야 하는 문제를 해결하기 위해 Webroot 방식으로 진행한다.)
		
	<VirtualHost *:80>
		...
		RewriteEngine on
		RewriteCond %{SERVER_NAME} =malgnigd.ze.to [OR]
		RewriteCond %{SERVER_NAME} =www.malgnigd.ze.to
		RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
	</VirtualHost>


http://로 접속시 https://로 리다이렉트 해주는 내용이 추가 됩니다.


	<IfModule mod_ssl.c>
	<VirtualHost *:443>
		ServerName     " malgnigd.ze.to"
		DocumentRoot    "/var/www/html"
		ServerAlias "www.malgnigd.ze.to"
	RewriteEngine on

	SSLCertificateFile /etc/letsencrypt/live/malgnigd.ze.to/fullchain.pem
	SSLCertificateKeyFile /etc/letsencrypt/live/malgnigd.ze.to/privkey.pem
	Include /etc/letsencrypt/options-ssl-apache.conf
	</VirtualHost>
	</IfModule>

그리고 /etc/httpd/conf/httpd-le-ssl.conf 를 생성하고 httpd.conf에서 Include 하게 됩니다.
 443에 대한 가상호스트를 설정하고, 인증서 파일 경로를 지정해준다.

#### 발급

그러면 아래의 화면 처럼 인증서 발급이 정상적으로 된 것을 확인 할수 있다.
![이미지1](/img/그림5.png)

## conf.d/ssl.conf


	SSLPassPhraseDialog exec:/usr/libexec/httpd-ssl-pass-dialog

암호화된 개인 키에 대한 암호 구문 대화 상자 타입

	SSLSessionCache         shmcb:/run/httpd/sslcache(512000)
서버 프로세스의 로컬 OpenSSL 메모리 캐시를 동기화하기 위해 RAM의 공유 메모리 세그먼트 내부의 고성능 순환 버퍼를 사용합니다.

	SSLSessionCacheTimeout  300

SSL Session Cache와 SSLeay internal memory cache에 저장된 정보들에 대한 timeout을 초단위로 지정한다. 설정 값은 최저 15초이며 실제로 실행될 서버에서는 보통 300초 이상을 지정합니다.

	SSLRandomSeed startup file:/dev/urandom  256
	SSLRandomSeed connect builtin

서버가 시동할 때(context가 startup인 경우) 혹은 새로운 SSL 연결이 이루어질 때 SSLeay의 Pseudo Random Number Generator(PRNG)의 seeding을 위한 하나 혹은 그 이상의 seed source를 설정합니다.

	SSLCryptoDevice builtin

ssl 처리 오버헤드의 일부를 줄일 수 있다.

	ErrorLog logs/ssl_error_log
	TransferLog logs/ssl_access_log
	LogLevel warn
	
	SSLEngine on

SSL/TLS 프로토콜 엔진의 사용을 토글합니다.

	SSLProtocol all -SSLv3

Netscape Corporation의 SSL(Secure Sockets Layer) 프로토콜 버전 3.0입니다.

	SSLCipherSuite HIGH:MEDIUM:!aNULL:!MD5:!SEED:!IDEA

OpenSSL 암호 사양으로 구성된 콜론으로 구분된 암호 사양 문자열을 사용하여 클라이언트가 SSL 핸드셰이크 단계에서 협상할 수 있는 암호 제품군을 구성합니다.

	SSLCertificateFile /etc/pki/tls/certs/localhost.crt
PEM 형식의 인증서 데이터 또는 구성된 암호화 토큰을 통한 인증서 식별자가 있는 파일을 가리킵니다.


	<Files ~ "\.(cgi|shtml|phtml|php3?)$">
		SSLOptions +StdEnvVars
	</Files>
디렉토리별로 다양한 런타임 옵션을 제어하는 ​​데 사용할 수 있습니다. 

	<Directory "/var/www/cgi-bin">
		SSLOptions +StdEnvVars
	</Directory>

/var/www/cgi-bin 에 SSLOptions 활성시킨다. 


	BrowserMatch "MSIE [2-5]" \
		nokeepalive ssl-unclean-shutdown \
		downgrade-1.0 force-response-1.0

구형 IE에서 발생하는 오류 처리하기 위한 설정, IE 2-5 브라우저에 대해서 http 프로토콜을 이요한 지속적인 연결을 무시한다.

	CustomLog logs/ssl_request_log \
	"%t %h %{SSL_PROTOCOL}x %{SSL_CIPHER}x \"%r\" %b"

사용자 정의 ssl 로그 파일의 홈, 가상 호스트 기반의 오류 없는 ssl 로그 파일을 압축한다.

                                
