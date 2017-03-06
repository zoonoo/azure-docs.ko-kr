---
title: "Azure 컨테이너 레지스트리의 Docker 이미지 | Microsoft Docs"
description: "Docker CLI를 사용하여 Azure Container Registry로 Docker 이미지 밀어넣기 및 끌어오기"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: 1e70f41dd15b0243fb8edd137710ac555821434e
ms.lasthandoff: 02/22/2017

---
# <a name="push-your-first-image-to-a-container-registry-using-the-docker-cli"></a>Docker CLI를 사용하여 컨테이너 레지스트리로 이미지 밀어넣기 및 끌어오기
Azure Container Registry는 [Docker Hub](https://hub.docker.com/)에서 공개 Docker 이미지를 저장하는 것과 유사한 방식으로 개인 [Docker](http://hub.docker.com) 컨테이너 이미지를 저장하고 관리합니다. [Docker 명령줄 인터페이스](https://docs.docker.com/engine/reference/commandline/cli/)(Docker CLI)를 사용하여 컨테이너 레지스트리에서 [로그인](https://docs.docker.com/engine/reference/commandline/login/), [밀어넣기](https://docs.docker.com/engine/reference/commandline/push/), [끌어오기](https://docs.docker.com/engine/reference/commandline/pull/) 및 기타 작업을 수행합니다. 

백그라운드 및 개념에 대한 자세한 내용은 [Azure Container Registry란?](container-registry-intro.md)을 참조하세요.


> [!NOTE]
> Container Registry는 현재 미리 보기 상태입니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
* **Azure Container Registry** - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI 2.0](container-registry-get-started-azure-cli.md)을 사용합니다.
* **Docker CLI** - 로컬 컴퓨터를 Docker 호스트로 설정하고 Docker CLI 명령에 액세스하려면 [Docker 엔진](https://docs.docker.com/engine/installation/)을 설치합니다.

## <a name="log-in-to-a-registry"></a>레지스트리에 로그인
[레지스트리 자격 증명](container-registry-authentication.md)을 사용하여 컨테이너 레지스트리에 로그인하려면 `docker login`을 실행합니다.

다음 예제는 Azure Active Directory [서비스 주체](../active-directory/active-directory-application-objects.md)의 ID와 암호를 전달합니다. 예를 들어 자동화 시나리오를 위해 레지스트리에 서비스 주체를 할당할 수 있습니다. 

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> 정규화된 레지스트리 이름(모두 소문자)을 지정해야 합니다. 이 예제에서는 `myregistry-contoso.azurecr.io`입니다.

## <a name="steps-to-pull-and-push-an-image"></a>이미지 끌어오기 및 밀어넣기 단계
다음 예제는 공개 Docker Hub 레지스트리에서 Nginx 이미지를 다운로드하고 개인 Azure Container Registry용으로 태그를 지정하고, 레지스트리에 밀어넣은 다음 다시 끌어옵니다.

**1. Nginx에 대한 Docker 공식 이미지 끌어오기**

먼저 공개 Nginx 이미지를 로컬 컴퓨터로 끌어옵니다.

```
docker pull nginx
```
**2. Nginx 컨테이너 시작**

다음 명령은 로컬 Nginx 컨테이너를 대화형으로 시작하고(따라서, Nginx의 출력을 볼 수 있음) 8080 포트를 수신 대기합니다. 일단 중지되면 실행 중인 컨테이너를 제거합니다.

```
docker run -it --rm -p 8080:80 nginx
```

[http://localhost:8080](http://localhost:8080)으로 이동하여 실행 중인 컨테이너를 봅니다. 다음과 비슷한 화면이 표시됩니다.

![로컬 컴퓨터의 Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

실행 중인 컨테이너를 중지하려면 [CTRL]+[C] 키를 누릅니다.

**3. 레지스트리에 있는 이미지의 별칭 만들기**

다음 명령은 레지스트리에 대한 정규화된 경로를 사용하여 이미지의 별칭을 만듭니다. 이 예제는 레지스트리의 루트에서 혼잡을 방지하기 위해 `samples` 네임스페이스를 지정합니다.

```
docker tag nginx myregistry-exp.azurecr.io/samples/nginx
```  

**4. 이미지를 레지스트리로 밀어넣기**

```
docker push myregistry-contoso.azurecr.io/samples/nginx
``` 

**5. 이미지를 레지스트리에서 끌어오기**

```
docker pull myregistry-contoso.azurecr.io/samples/nginx
``` 

**6. 레지스트리에서 Nginx 컨테이너 시작**

```
docker run -it --rm -p 8080:80 myregistry-exp.azurecr.io/samples/nginx
```

[http://localhost:8080](http://localhost:8080)으로 이동하여 실행 중인 컨테이너를 봅니다.

실행 중인 컨테이너를 중지하려면 [CTRL]+[C] 키를 누릅니다.

**6. 이미지 제거**

```
docker rmi myregistry-contoso.azurecr.io/samples/nginx
```



## <a name="next-steps"></a>다음 단계
이제 기본 사항을 배웠으니 레지스트리 사용을 시작할 준비가 되었습니다! 예를 들어, 컨테이너 이미지를 [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) 클러스터에 배포하기 시작하세요.




