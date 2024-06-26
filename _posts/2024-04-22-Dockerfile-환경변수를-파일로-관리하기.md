---
title: Dockerfile 환경변수를 파일로 관리하기
date: 2024-04-22 18:00:00 +09:00
description: >-
    Dockerfile에 필요한 환경변수를 파일로 관리해 보안을 유지하는 방법에 대해서 설명합니다.
categories: [도커, 보안]
tags: [도커]
---

## 왜 파일로 환경변수를 관리하게 되었는가?

`Dockerfile`의 장점 중 하나는 프로젝트와 함께 레포지토리에 올려 프로젝트의 구성 정보를 버전으로 관리할 수 있다는 점이다. 하지만 `Dockerfile`에는 민감한 정보가 들어갈 수 있다. 외부에 알려져서는 안되는 키값이나 ID가 그러한 것이다. 

`Dockerfile`의 `ENV`명령을 사용해서 도커 컨테이너 시작시에 환경변수를 설정할 수 있다. 하지만 ENV 명령을 사용해서 환경변수를 선언하게 되면 `Dockerfile`에 민감한 정보가 노출된다. 

파일로 환경변수를 관리하게 되면 환경변수를 담고있는 파일은 `.gitignore`로 무시하면서 외부에 노출시키지 않고, 로컬에는 파일이 존재하기 때문에 민감한 정보를 외부에 공개하지 않으면서 이미지를 빌드 할 수 있다.

## 환경변수를 파일로 관리하는 방법

`.env` 파일을 활용하면 된다. 나는 네이버 클라우드의 핀포인트 서비스를 콘솔로 사용하는데 에이전트가 컨트롤러에 데이터를 전송하기 위해서는 `라이센스ID` 가 필요하다. `Dockerfile`에서 `ENV`변수를 선언하면 `Dockerfile`에 `라이센스ID`가 노출되어 버린다. 

`.env`파일을 만들면 다음과 같이 `key=value`의 형태로 환경변수를 나열하면 됩니다.

```sh
PORT=8080
LICENCE_ID=6d*****7hd
```

### docker build
`docker build` 명령을 사용해서 이미지를 만들 때 .env 환경변수 파일을 사용하기 위해서는 `--env-file` 옵션으로 환경변수 정보가 담긴 파일을 명시해주어야 합니다.
```sh
docker run ... --env-file ./.env ...
```
참고로 `--env-file` 옵션이 없으면 .env라는 이름의 파일이 자동으로 사용됩니다.

### Dockerfile, Docker Compose
Dockerfile과 Docker Compose내에서 파일에 작성한 환경변수가 필요하다면 `${...} 내부에 정의해둔 환경변수의 key값을 작성하면 됩니다.

도커 컴포즈를 이용해서 빌드할 때도 `docker build`와 마찬가지로 --env-file 옵션을 사용하면 됩니다. 물론 이것도 해당 옵션을 작성하지 않으면 가장 `Dockerfile`, `docker-compose.yml`이 있는 상위폴더의 `.env` 파일을 사용합니다. 


## Reference
- [Docker Compose env_file 속성 문서](https://docs.docker.com/compose/environment-variables/set-environment-variables/#use-the-env_file-attribute)
- [**[Stack overflow]** Why do I need to declare env_file explicitely in docker-compose.yml?](https://stackoverflow.com/questions/58047984/why-do-i-need-to-declare-env-file-explicitely-in-docker-compose-yml)