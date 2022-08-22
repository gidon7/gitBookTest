# httpd.conf

    ServerRoot "/etc/httpd"

서버루트는 최상위 디렉토리로 서버 설정, 에러 정보 및 로그 파일이 저장되는 곳이다.

	Listen 80

아파치 웹 서버의 포트를 설정한다.
아파치 1.3버전의 Port 지시자를 대체하여 Listen을 사용한다.

	Include conf.modules.d/*.conf

ServerRoot 에 상대적으로 파일의 경로를 지정하여 포함시키는 것이 가능하다.

	User apache
	Group apache

실행되는 httpd 데몬의 사용자 및 그룹 권한을 나타낸다.

	ServerAdmin root@localhost

httpd 오류 관련 메일을 받을 수 있는 주소 설정을 한다.

	<Directory />
		AllowOverride none
		Require all denied
	</Directory>

전체 디렉터리에 대한 기본 옵션이나 권한을 나타낸다.

	DocumentRoot "/var/www/html"

httpd가 요청된 파일을 검색할 위치 지정

	<Directory "/var/www">
		AllowOverride None
		# Allow open access:
		Require all granted
	</Directory>

이 디렉터리에 대한 엑세스 허용

	<Directory "/var/www/html">
		Options Indexes FollowSymLinks
		AllowOverride None
		Require all granted
	</Directory>

/var/www/html 에 대하여 Directoryindex를 가지고 있지 않은 디렉토리에 접근하려고 하는 경우 디렉토리의 내용을 보여준다. 아파치가 심볼릭 링크를 사용하도록 한다. 성능은 향상되나 보안상 문제가 있다.

	<IfModule dir_module>
		DirectoryIndex index.html
	</IfModule>

디렉터리 인덱스 파일(처음 열리는 파일)로 사용할 파일 이름을 적는다.
	
	<Files ".ht*">
		Require all denied
	</Files>

.ht로 시작하는 파일에 대한 접근을 거부하는 설정
아파치 사용자 인증 파일인 .htaccess에 대한 권한 설정

	ErrorLog "logs/error_log"

웹 서버에  에러가 발생했을 경우, 에러에 관한 기록 파일을 설정한다.
<VirtualHost> 지시자에서 ErrorLog 설정을  핮지 않았다면, 여기에서 언급한 설정을 따른다.
로그 파일의  경우는 계속해서 사용량이 증가하므로 수시로 용량을 줄여야 한다.

	LogLevel warn

로그 메시지의 크기를 제어하는 부분 (옵션 : debug, info, notice, warn, error, crit, alert, emerg)

	<IfModule log_config_module>
		LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
		LogFormat "%h %l %u %t \"%r\" %>s %b" common

		<IfModule logio_module>
			
		</IfModule>
		CustomLog "logs/access_log" combined
	</IfModule>

로그 관련 형식 및 관련 파일에 대한 설정을 하는 곳

### LogFormat

| 형식 | 설명 |
| --- | --- |
|%h|호스트명|
|%l|리모트 로그 이름|
|%u|사용자 인증에 사용된 유저명|
|%t|시간|
|%r|요청한 내용의 첫 번째 줄|
|%s|서버 상태|
|$b|전송량(헤더 포함) - 바이트수|
|%{헤더}|요구된 헤더 내용|
|$b|HTTP 헤더를 제외한 전송 바이트 수|

	<IfModule alias_module>
		ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
	</IfModule>

Alias 와 같은 기능을 하는 지시자
CGI 스크립트 파일이 있는 경로를 지정한다.

	<Directory "/var/www/cgi-bin">
		AllowOverride None
		Options None
		Require all granted
	</Directory>

/var/www/cgi-bin 디렉토리에 대하여 옵션을 제거하고 모든 접근을 허용한다.

	<IfModule mime_module>
		TypesConfig /etc/mime.types
		AddType application/x-compress .Z
		AddType application/x-gzip .gz .tgz
		AddType text/html .shtml
		AddOutputFilter INCLUDES .shtml
	</IfModule>

TypesConfig : mime.types 파일 또는 이에 해당하는 파일을 찾을 위치를 결정한다.
MIME : 클라이언트 브라우저에게 어떠한 유형의 자료를 전송할 것인지를 조절해주는 기능이다.
AddType : 일부 브라우저에게 전송을 받으면서 정보를 압축 해제할 수 있돌록 허용할 때 사용하는 것이다.(모든 브라우저에서 이 기능을 지원하지는 않는다.)

	AddDefaultCharset UTF-8

default charactrset 인코딩을 UTF-8로 설정하나.

	<IfModule mime_magic_module>
		MIMEMagicFile conf/magic
	</IfModule>

mod_mime_magic 모듈을 사용할 경우, 해당 파일의 내용으로 파일 타입 힌트를 얻는 역할을 한다.
MIMEMagicFile : 암시를 정의하는 모듈이 어디에 위치하고 위치를 알려준다.  

	EnableSendfile on

sendfile 지원을 받을 지 여부 설정한다.

	<IfModule mod_http2.c>
		Protocols h2 h2c http/1.1
	</IfModule>

http/2에 대한 지원을 제공
http/1.1은 http와 https h2는 https h2c는 http 연결을 가능하게 한다.

	IncludeOptional conf.d/*.conf
	
Include와 유사하지만 지정한 파일이 없어도 오류가 발생하지 않는다.
