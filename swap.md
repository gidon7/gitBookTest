# AWS SWAP 설정

## 1. dd 명령을 사용하여 루트 파일 시스템에 스왑 파일을 생성합니다.
### dd 명령에서 스왑 파일은 4GB(128MB x 32)입니다.
    $ sudo dd if=/dev/zero of=/swapfile bs=128M count=32 

## 2. 스왑 파일의 읽기 및 쓰기 권한을 업데이트 합니다.
    $ sudo chmod 600 /swapfile

## 3. Linux 스왑 영역을 설정합니다.
    $ sudo mkswap /swapfile

## 4. 스왑 공간에 스왑 파일을 추가하여 스왑 파일을 즉시 사용할 수 있도록 합니다.
    $ sudo swapon /swapfile

## 5. 프로시저가 성공적인지 확인합니다.
    $ sudo swapon -s

## 6. /etc/fstab 파일을 편집하여 부팅 시 스왑 파일을 시작합니다.
    $ sudo vi /etc/fstab

## 파일 끝에 다음 줄을 새로 추가하고 파일을 저장한 다음 종료합니다.
    /swapfile swap swap defaults 0 0

## 7. 메모리 확인을 합니다.
    $ free -h
