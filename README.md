### Doker token 등록 ###
 현재 README.md 파일이 있는 위치 env 설치 그 안에 dokerc_token
 env/docker_token 파일 생성
 docker hub 사이트에서 발급 받은 token 등록
  
### Docker storage ###
 docker image를 저장하는 저장소

 * Volume *
  컨테이너와 호스트간에 데이터를 공유하고 보존
  아예 호스트에 저장이 되기 때문에 컨테이너를 삭제해도, 영구저장이 가능함!

### Docker storage 연결방식
 1. bind : host 파일 시스템에 특정 디렉토리 위치, 컨테이너 특정 디렉토리랑 바인딩
  1) mount 구문은 각 옵션을 개별적으로 지정
  2) 사용 가능한 특정 디렉토리 구조를 가진 호스트 시스템의 파일 시스템에 의존
    -v or --mount 옵션 둘중에 하나 쓰면 됨

    (1) --v 사용 예시
    $ docker run -d -it --name devtest -v /storage/source/ct03-bind01:/storage:ro nginx
                                                           host 절대경로     container 경로 : read only

    (2) --mount 사용 예시
    $ docker run -d -it --name devtest --mount type=bind, source=/storage/source/ct03-bind01, target=/storage, readonly nginx                                                       

 2. volume : docker에서 생성한 volume /bar/lib/docker/volume (리눅스 경로)
    bind는 직접 관리, docker에서 제공한것 안정성 있게 관리 가능
  

 3. tmpfs : 메모리에 저장 임시저장, 읽기 쓰기에 대한 접근 속도가 빠름
     컨테이너 재시작 후에 재설정 가능

 
 docker volume prune : 연결이 안된 volume에 대해서만 삭제함


 ### 명령어 ###
 docker volume create [OPTIONS] [VOLUME] volume 생성
 docker volume ls [OPTIONS] [VOLUME] 생성된 목록 확인
 docker inspect [VOLUME] 생성된 볼륨의 상세 정보 (json형식으로 나옴)

 ### label, create ###
  vagrant@mz-server:~$ docker volume create --label ct=mzc-ubuntu-ct01 vol2
  vagrant@mz-server:~$ docker volume create --label ct=mzc-ubuntu-ct01 --label creater=wikiko.roy  vol3

  vagrant@mz-server:~$ docker volume ls --format "{{.Name}}:\t {{.Labels}}"
vol1:    
vol2:    ct=mzc-ubuntu-ct01
vol3:    creater=wikiko.roy,ct=mzc-ubuntu-ct01

format \t 붙이면 떨어져서 나옴

vagrant@mz-server:~$ docker volume ls --format "{{.Name}}:\t {{.Labels}}" --filter label=ct=mzc-ubuntu-ct01
vol2:    ct=mzc-ubuntu-ct01
vol3:    creater=wikiko.roy,ct=mzc-ubuntu-ct01
(+) filter 옵션씀

vagrant@mz-server:~$ docker volume rm $(docker volume ls -q --filter label=ct=mzc-ubuntu-ct01)
 > 지정한 필터에 해당되는 볼륨 지우기

vol2
vol3
 > 지워진 결과값 출력해줌

 docker run --volume [OPTIONS] IMAGE [COMMAND] [ARG…]
  -v, --volume : 출력 형석을 지정할 수 있습니다.

docker run -it -d -v vol1:/mnt --name volume_bind1 alpine
vol을 alpine에 /mnt --name volume_bind1에 연결
vagrant@mz-server:~$ docker run -it -d -v vol1:/mnt --name volume_bind1 alpine
vagrant@mz-server:~$ docker exec -it volume_bind1 /bin/sh

vagrant@mz-server:~$ docker run -it -d -v vol1:/mnt --name volume_bind2  alpine
5351e063a8e9117fa204674c271fed1e8c72344549b8e0e5d6ef716c3bcb738c
vagrant@mz-server:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS             PORTS     NAMES
5351e063a8e9   alpine    "/bin/sh"                12 seconds ago      Up 11 seconds                volume_bind2
41738c0c30db   alpine    "/bin/sh"                3 minutes ago       Up 3 minutes                 volume_bind1
a9afb78e5cec   nginx     "/docker-entrypoint.…"   About an hour ago   Up About an hour   80/tcp    test1
vagrant@mz-server:~$ docker exec -it volume_bind2 /bin/sh
/ # ls -la /mnt
total 16
drwxr-xr-x    3 root     root          4096 Jul  6 02:07 .
drwxr-xr-x    1 root     root          4096 Jul  6 02:09 ..
-rw-r--r--    1 root     root            26 Jul  6 02:08 hello
drwxr-xr-x    2 root     root          4096 Jul  6 02:07 test

vagrant@mz-server:~$ sudo ls -la /var/lib/docker/volumes/vol1/_data
total 16
drwxr-xr-x 3 root root 4096 Jul  6 02:07 .
drwx-----x 3 root root 4096 Jul  6 01:36 ..
-rw-r--r-- 1 root root   26 Jul  6 02:08 hello
drwxr-xr-x 2 root root 4096 Jul  6 02:07 test


vagrant@mz-server:~$ docker inspect --format="{{range .Mounts}}{{.Source}}{{.Destination}}{{end}}" volume_bind1
/var/lib/docker/volumes/vol1/_data/mnt
vagrant@mz-server:~$ docker inspect --format="{{range .Mounts}}{{.Source}}{{.Destination}}{{end}}" volume_bind2
/var/lib/docker/volumes/vol1/_data/mnt

 #### volume 2개 연결지음 ####
vagrant@mz-server:~$ docker run -it -d -v vol1:/mnt -v vol2:/storage --name volume_bind3  alpine

vagrant@mz-server:~$ docker inspect --format="{{range .Mounts}}{{.Source}}{{.Destination}}{{end}}" volume_bind3
/var/lib/docker/volumes/vol1/_data/mnt/var/lib/docker/volumes/vol2/_data/storage

vagrant@mz-server:~$ docker inspect --format="{{range .Mounts}}{{.Source}}{{.Destination}}{{println}}{{end}}" volume_bind3
/var/lib/docker/volumes/vol1/_data/mnt
/var/lib/docker/volumes/vol2/_data/storage
 > 줄바꿈 추가

nginx 바로보고 있는 root directory 어디?
include 추가 설정

windows\system32\drivers\etc\hosts

127.0.0.1 apache-web.co.kr
127.0.0.2 nginx-web.co.kr 
로 넣어두면 ip 입력 안해도, 알아서 nginx-web.co.kr로 매핑 시켜줌 dns랑 비슷

 #### Volume 옵션 ####
 docker run -it --name mzc-centos-ct01 -v ct01-vol01:/storage centos:ro > read olny
 source code는 수정되서는 안되서, 읽기 전용으로 권한 부여
 docker run -v 옵션시 사용하면됨
 docker run -d -v volume name:/어디로 연결할건지? :ro (옵션)
 한번 권한부여하면 변경안됨 > 지우고 다시 생성

 ##### volume 사용하면 binding 한 원래 폴더가 비어 있지 않았을때 원래 폴더 내용들까지 확인 가능 ####
vagrant@mz-server:~$ docker run -it -v empty_vol:/usr/local/apache2 --name t1 httpd /bin/bash
root@026732e941e1:/usr/local/apache2# ls -la
total 48
drwxr-xr-x 12 www-data www-data 4096 Jul  6 06:43 .
drwxr-xr-x  1 root     root     4096 Jul  4 13:15 ..
drwxr-xr-x  2 root     root     4096 Jul  6 06:43 bin
drwxr-xr-x  2 root     root     4096 Jul  6 06:43 build
drwxr-xr-x  2 root     root     4096 Jul  6 06:43 cgi-bin
drwxr-xr-x  4 root     root     4096 Jul  6 06:43 conf
drwxr-xr-x  3 root     root     4096 Jul  6 06:43 error
drwxr-xr-x  2 root     root     4096 Jul  6 06:43 htdocs
drwxr-xr-x  3 root     root     4096 Jul  6 06:43 icons
drwxr-xr-x  2 root     root     4096 Jul  6 06:43 include
drwxr-xr-x  2 root     root     4096 Jul  6 06:43 logs
drwxr-xr-x  2 root     root     4096 Jul  6 06:43 modules
root@026732e941e1:/usr/local/apache2# exit       


 ##### mount 사용하면 binding 한 원래 폴더가 비어 있지 않았을때 원래 폴더 내용 무시하고 volume파일 내용만 보여줌 덮어쓰기 ####
vagrant@mz-server:~$ docker run -it -v /home/vagrant/empty_bind:/usr/local/apache2 --name t2 httpd /bin/bash
root@0adf6efc0425:/usr/local/apache2# ls -la
total 8
drwxr-xr-x 2 root root 4096 Jul  6 06:46 .
drwxr-xr-x 1 root root 4096 Jul  4 13:15 ..






