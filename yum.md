# yum 사용법

## 1. yum(Yellodog Update Modified)
- yum이란 Yellodog Update Modified의 약자로 레드햇 계열의 리눅스 배포판에서 사용하는 프로그램(패키지) 설치 관리 도구 입니다. 명령어 이름으로만 봤을때는 전혀 기능과 관련이 없어보이기도 합니다. 그러나 yum은 rpm 명령어가 해결하지 못했던 패키지 의존성 문제를 해결한 똑똑한 패키지 관리 툴입니다. yum 명령어를 사용하면 패키지 의존성 문제를 자동으로 처리하면서 설치, 업데이트, 삭제를 진행할 수 있습니다.

## 2. yum의 기본 명령어
| 명령어 | 설명 |
| --- | --- |
|  yum check-update | 현재 인스톨된 프로그램 중 업데이트 된 것을 체크 |
| yum clean all | 캐시 되어 있는 것을 모두 지움 |
| yum deplist | yum 패키지에 대한 의존성 테스트 |
| yum downgrade 패키지 | yum을 통한 패키지 다운그레이드 |
| yum erase 패키지 | yum을 통한 시스템에서 삭제 |
| yum groupinfo 그룹 | 그룹패키지의 정보를 보여줌 |
| yum groupinstall 그룹 | 그룹패키지 설치 |
| yum grouplist 그룹 | 그룹리스트에 관한 정보 확인 |
| yum groupremove 그룹 | 그룹리스트에 관해 삭제 |
| yum help | yum 도움말 확인 |
| yum info 그룹 또는 패키지 | 패키지 또는 그룹의 패키지를 자세하게 확인 |
| yum install 패키지 | 시스템으로 패키지의 Install을 실시 |
| yum list | 서버에 있는 그룹 및 패키지의 리스트를 보여줌 |
| yum localinstall 패키지 | 로컬에 설치 |
| yum makecache | 캐쉬 다시 올림 |

## 3. yum 동작 흐름

1. yum install 로 패키지 설치 명령을 실행합니다.
2. /etc/yum.repos.d/ 디렉터리의 여러 설정파일들 및 CentOS-Base.repo 파일을 참고하여 인터넷상의 패키지 저장소 서버 주소를 얻습니다.
3. 패키지 목록을 저장소 서버로부터 얻습니다.(실제 패키지는 받지 않고 목록만 가져옴)
4. 가져온 패키지 목록을 터미널에 출력하고 설치할 것인지 여부(Y/N)를 묻습니다.
5. Y를 입력하면 패키지를 다운로드하여 자동으로 설치합니다. 만약 yum install -y 옵션을 사용했다면 여부를 묻지 않고 모든 과정을 자동으로 진행합니다.


## 4. Repository

- Package를 모아놓은 저장소입니다. Yum을 통해 Package 설치 시 활성화 된 Yum Repository에서 package를 다운로드하여 설치하기 때문에 Package가 Repository에 없을 경우 설치 할 수 없습니다.

### 4-1. yum repository 리스트 확인
	
	yum repolist

### 4-2. yum repository 설정 위치

	/etc/yum.repos.d

### 4-3. yum repository 추가
	
	vi /etc/yum.repos.d/레포지토리_name.repo  

	<필드설명>
	name=저장소 표시 이름
	baseurl=저장소 주소
	enabled=활성화 여부 (0 or 1)
	gpgcheck=gpg 서명키 사용 여부 (0 or 1)
	gpgkey=서명키 사용 시 서명키 주소	

### 4-4. yum repository 삭제

	[불필요한 repository 파일을 삭제]
	rm -rf /etc/yum.repos.d/{REPO_NAME}.repo

	[cache 파일 삭제]
	rm -rf /var/cache/yum/x86_64

	[Yum headers, packages, metadata 삭제]
	yum clean headers
	yum clean packages
	yum clean metadata


