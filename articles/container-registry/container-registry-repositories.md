---
title: "Azure 컨테이너 레지스트리 리포지토리 | Microsoft Docs"
description: "Docker 이미지에 Azure 컨테이너 레지스트리 리포지토리를 사용하는 방법"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: cristyg
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 06b809c31cecef1714f60d04657eb74c611be8cb
ms.lasthandoff: 03/28/2017


---
# <a name="azure-container-registry-repositories"></a>Azure 컨테이너 레지스트리 리포지토리

Azure 컨테이너 레지스트리를 통해 리포지토리에 컨테이너 이미지를 저장할 수 있습니다. 리포지토리에 이미지를 저장하면 격리된 환경에 이미지 그룹(또는 이미지 버전)을 포함할 수 있습니다. 이미지를 레지스트리에 푸시할 때 이러한 리포지토리를 지정할 수 있습니다.


## <a name="prerequisites"></a>필수 조건
* **Azure Container Registry** - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI 2.0](container-registry-get-started-azure-cli.md)을 사용합니다.
* **Docker CLI** - 로컬 컴퓨터를 Docker 호스트로 설정하고 Docker CLI 명령에 액세스하려면 [Docker 엔진](https://docs.docker.com/engine/installation/)을 설치합니다.
* **이미지 끌어오기** - 공개 Docker Hub 레지스트리에서 이미지를 끌어온 후 태그를 지정하고 레지스트리에 밀어넣습니다. 이미지를 밀어넣고 끌어오는 방법은 [Azure 개인 레지스트리에 Docker 이미지 밀어넣기](container-registry-get-started-docker-cli.md)를 참조하세요.


## <a name="viewing-repositories-in-the-portal"></a>포털에서 리포지토리 보기

이미지를 컨테이너 레지스트리에 밀어넣었으면 Azure Portal에서 이미지를 호스팅하는 리포지토리 목록을 볼 수 있습니다.

[Azure 개인 레지스트리에 Docker 이미지 밀어넣기](container-registry-get-started-docker-cli.md) 문서의 단계를 수행하면 컨테이너 레지스트리에 Nginx 이미지가 포함됩니다. 지침의 일부로 이미지에 대한 네임스페이스를 지정해야 합니다. 아래 예제의 명령은 NGinx 이미지를 "samples" 리포지토리에 밀어넣습니다.

```
docker push myregistry.azurecr.io/samples/nginx
```
 Azure Container Registry는 다단계 리포지토리 네임스페이스를 지원합니다. 이 기능을 통해 특정 앱과 관련된 이미지 컬렉션 또는 특정 배포 또는 작업 팀에 대한 앱 컬렉션을 그룹화할 수 있습니다. 컨테이너 레지스트리의 리포지토리에 대한 자세한 내용은 [Azure의 개인 Docker 컨테이너 레지스트리](container-registry-intro.md)를 참조하세요.

Azure 컨테이너 레지스트리 리포지토리를 보려면

1. Azure 포털에 로그인
2. **Azure 컨테이너 레지스트리** 블레이드에서 검사할 레지스트리를 선택합니다.
3. 레지스트리 블레이드에서 **리포지토리**를 클릭하면 모든 리포지토리와 해당 이미지 목록이 표시됩니다.
4. (선택 사항) 태그를 볼 특정 이미지 선택

![포털에서 리포지토리](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>다음 단계
이제 기본 사항을 배웠으니 레지스트리 사용을 시작할 준비가 되었습니다! 예를 들어, 컨테이너 이미지를 [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) 클러스터에 배포하기 시작하세요.

