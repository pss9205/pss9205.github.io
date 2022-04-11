---
layout: post
title: "Docker 기본 커맨드"
date: 2022-04-10 23:14:00 +0900
categories: [Docker]
tags: [docker]
---

## 컨테이너

프로세스 수준의 가상화. 프로그램 코드와 라이브러리와 같은 종속된 항목들을 묶은 패키지

### 컨테이너 vs 가상머신

가상 머신은 자체 커널을 포함하는 OS를 실행하지만 컨테이너는 Host OS위에서 커널을 공유하며 실행됨.
때문에 컨테이너가 훨씬 가벼움

```
[      Application     ]    [      Application     ]
[       Lib, Deps      ]    [       Lib, Deps      ]
[       Conatiner      ]    [           OS         ]
[        Docker        ]    [    Virtual Machine   ]
[          OS          ]    [      HyperVisor      ]
[          H/W         ]    [          H/W         ]
```

가상 머신 위에 Docker Host를 돌린다면?
=> 가상화의 장점, 컨테이너의 장점을 모두 챙길 수 있다. Easy to provision, decommission, Quickly scale

## 도커란?

> 컨테이너 기반의 가상화 플랫폼

프로그램이 컨테이너 위에서 실행되기 때문에 프로그램 간 요구하는 라이브러리 버전, OS등이 달라도 이에 구애받지 않고 각 프로그램만의 환경 구축 가능  
프로그램 실행에 필요한 모든 설정을 가지고 있기 때문에 Dev서버, Prod서버와 같이 같은 환경을 재설정 하는 경우에도 쉽게 할 수 있음

### Command

- run : docker 컨테이너 실행을 위한 커맨드. 이미지가 없다면 다운 받은 후 실행

  > docker run -d -p 80:80 docker/getting-started

  ```tip
  container는 안에 실행중인 프로세스가 있을때만 alive상태를 유지한다.
  ```

  옵션

  1. -d : 백그라운드에서 실행
  2. -I stdin / -t stdout : terminal과 docker를 연결하여 interaction 가능하게 해줌
  3. -p : docker host와 container간의 포트를 매핑
     > -p 80(host):5000(container)
  4. -v : docker host와 container간의 volume 매핑  
     컨테이너안의 데이터는 컨테이너가 정지, 삭제되면 모두 제거되기 때문에 호스트의 디렉토리를 마운트하여 사용해야함
     > -v hostpath:containerpath
  5. -e : 패스워드와 같이 설정이 필요한 환경 변수 설정
     > -e MYSQL_ROOT_PASSWORD=my-secret-pw
  6. -attach : 실행중인 컨테이너에 연결

- ps : 실행중인 container 프로세스 목록을 볼 수 있음

  > docker ps

  옵션

  1. -a 이미 종료된 프로세스 까지 모두 표시

- stop : container 정지
  > docker stop {containerId}
- rm : 컨테이너 데이터 삭제
  > docker rm {containerId}
- images : image목록 확인
  > docker images
- rmi : 이미지 삭제
  > docker rmi {imageId}
- pull : 이미지 다운로드
  > docker pull {imageName}:{tag}
- exec : 컨테이너에 명령어 실행
  > docker exec {containerId} {command}
- inspect : 컨테이너의 detail정보를 json 형태로 출력
  > docker inspect {containerId}
- logs : 로그를 출력함
  > docker logs {containerId}
