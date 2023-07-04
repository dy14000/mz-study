# verbose-adventure

```` bash

#### docker 다운로드
 sudo apt-get update
 sudo apt-get install ca-certificates curl gnupg

#### Add Docker’s official GPG key
 sudo install -m 0755 -d /etc/apt/keyrings
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
 sudo chmod a+r /etc/apt/keyrings/docker.gpg


 #### 저장소 등록
 Use the following command to set up the repository
 echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

#### 등록한 패키지 저장소 적용
``` bash
sudo apt-get update
```

#### docker 설치 (containerd도 설치)
``` bash


#### 설치후 확인
``` bash
docker -v
# 