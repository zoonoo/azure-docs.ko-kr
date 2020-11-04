---
title: 개인 컨테이너 레지스트리에서 공용 콘텐츠 관리
description: Docker 허브 및 기타 공용 콘텐츠에서 공용 이미지에 대 한 종속성을 관리 하기 위한 Azure Container Registry의 사례 및 워크플로
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 10/29/2020
ms.openlocfilehash: def1c3a9b8a1086f453c7e71d766ab0dd89b0c2d
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347525"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Azure Container Registry를 사용 하 여 공용 콘텐츠 관리

이 문서는 Docker 허브의 컨테이너 이미지와 같은 공용 콘텐츠의 복사본을 유지 관리 하기 위해 [Azure container registry](container-registry-intro.md) 와 같은 로컬 레지스트리를 사용 하는 방법 및 워크플로의 개요입니다. 


## <a name="risks-with-public-content"></a>공용 콘텐츠를 사용 하는 위험

사용자 환경에는 공용 컨테이너 이미지, [투구 차트](https://helm.sh/), opa ( [Open Policy Agent](https://www.openpolicyagent.org/) ) 정책 또는 기타 아티팩트와 같은 공용 콘텐츠에 대 한 종속성이 있을 수 있습니다. 예를 들어 서비스 라우팅에 대해 [nginx](https://hub.docker.com/_/nginx) 를 실행 하거나 `docker build FROM alpine` Docker 허브 또는 다른 공용 레지스트리에서 직접 이미지를 끌어올 수 있습니다. 

적절 한 컨트롤이 없으면 공용 레지스트리 콘텐츠에 대 한 종속성이 있으면 이미지 개발 및 배포 워크플로에 위험을 초래할 수 있습니다. 위험을 완화 하려면 가능한 경우 공용 콘텐츠의 로컬 복사본을 유지 합니다. 자세한 내용은 [컨테이너 이니셔티브 열기 블로그](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/)를 참조 하세요. 

## <a name="authenticate-with-docker-hub"></a>Docker 허브를 사용 하 여 인증

첫 번째 단계로, 현재 빌드 또는 배포 워크플로의 일부로 Docker 허브에서 공용 이미지를 끌어오는 경우 익명 끌어오기 요청을 수행 하는 대신 [Docker 허브 계정을 사용 하 여 인증](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) 하는 것이 좋습니다.

> [!NOTE]
> 2020 년 11 월 2 일부 터 [다운로드 요금 제한은](https://docs.docker.com/docker-hub/download-rate-limit) Docker 무료 계획 계정에서 docker 허브에 대 한 익명 및 인증 된 요청에 적용 되며 각각 IP 주소 및 docker ID로 적용 됩니다. 
>
> 끌어오기 요청 수를 예측 하는 경우 클라우드 공급자 서비스를 사용 하거나 회사 NAT 뒤에서 작업 하는 경우 여러 사용자가 집계의 Docker 허브에 IP 주소 하위 집합으로 표시 됩니다. Docker Hub에 대 한 요청에 Docker 유료 계정 인증을 추가 하면 요금 제한으로 인 한 잠재적인 서비스 중단이 발생 하지 않습니다.
>
> 자세한 내용은 [docker 가격 책정 및 구독](https://www.docker.com/pricing) 및 [docker 서비스 약관](https://www.docker.com/legal/docker-terms-service)을 참조 하세요.

### <a name="docker-hub-access-token"></a>Docker 허브 액세스 토큰

Docker 허브는 docker 허브에 인증할 때 Docker 암호의 대 안으로 [개인 액세스 토큰](https://docs.docker.com/docker-hub/access-tokens/) 을 지원 합니다. 토큰은 Docker 허브에서 이미지를 가져오는 자동화 된 서비스에 권장 됩니다. 다른 사용자 또는 서비스에 대해 여러 토큰을 생성 하 고 더 이상 필요 하지 않은 경우 토큰을 해지할 수 있습니다.

토큰을 사용 하 여 인증 하려면 `docker login` 명령줄에서 암호를 생략 합니다. 암호를 입력 하 라는 메시지가 표시 되 면 토큰을 대신 입력 합니다. Docker 허브 계정에 대해 2 단계 인증을 사용 하도록 설정한 경우 Docker CLI에서 로그인 할 때 개인용 액세스 토큰을 사용 해야 합니다.

### <a name="authenticate-from-azure-services"></a>Azure 서비스에서 인증

App Service 및 Azure Container Instances를 비롯 한 몇 가지 Azure 서비스는 컨테이너 배포를 위해 Docker 허브와 같은 공용 레지스트리에서 이미지를 끌어올 수 있도록 지원 합니다. Docker 허브에서 이미지를 배포 해야 하는 경우 Docker 허브 계정을 사용 하 여 인증 하는 설정을 구성 하는 것이 좋습니다. 예제:

**App Service**

* **이미지 원본** : Docker 허브
* **리포지토리 액세스** : 개인
* **로그인** : \<Docker Hub username>
* **암호** : \<Docker Hub token>

자세한 내용은 [App Service에서 Docker Hub 인증](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html)가져오기를 참조 하세요.

**Azure Container Instances**

* **이미지 원본** : Docker 허브 또는 기타 레지스트리
* **이미지 형식** : 개인
* **이미지 레지스트리 로그인 서버** : docker.io
* **이미지 레지스트리 사용자 이름** : \<Docker Hub username>
* **이미지 레지스트리 암호** : \<Docker Hub token>
* **이미지** : docker.io/ \<repo name\> :\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Azure container registry에 이미지 가져오기
 
공용 이미지의 복사본을 관리 하기 시작 하려면 아직 없는 경우 Azure container registry를 만들 수 있습니다. [Azure CLI](container-registry-get-started-azure-cli.md), [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md)또는 기타 도구를 사용 하 여 레지스트리를 만듭니다. 

권장 일회성 단계로 기본 이미지 및 기타 공용 콘텐츠를 Azure container registry로 [가져옵니다](container-registry-import-images.md) . Azure CLI의 [az acr import](/cli/azure/acr#az_acr_import) 명령은 Docker 허브, Microsoft Container Registry 및 다른 개인 컨테이너 레지스트리의 이미지 가져오기를 지원 합니다. 

`az acr import` 로컬 Docker 설치가 필요 하지 않습니다. Azure CLI의 로컬 설치를 사용 하거나 Azure Cloud Shell 직접 실행할 수 있습니다. 모든 OS 유형, 다중 아키텍처 이미지 또는 OCI 아티팩트의 이미지 (예: 투구 차트)를 지원 합니다.

예제:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

조직의 요구 사항에 따라 전용 레지스트리 또는 공유 레지스트리의 리포지토리로 가져올 수 있습니다.

## <a name="automate-application-image-updates"></a>응용 프로그램 이미지 업데이트 자동화

응용 프로그램 이미지 개발자는 코드에서 해당 컨트롤의 로컬 콘텐츠를 참조 하도록 해야 합니다. 예를 들어 `Docker FROM` Dockerfile의 문은 공용 레지스트리 대신 개인 기본 이미지 레지스트리의 이미지를 참조 해야 합니다. 

이미지 가져오기를 확장 하 고 기본 이미지가 업데이트 될 때 응용 프로그램 이미지 빌드를 자동화 하도록 [Azure Container Registry 작업](container-registry-tasks-overview.md) 을 설정 합니다. 자동화 된 빌드 작업은 [기본 이미지 업데이트](container-registry-tasks-base-images.md) 와 [소스 코드 업데이트](container-registry-tasks-overview.md#trigger-task-on-source-code-update)를 모두 추적할 수 있습니다.

자세한 예제는 [Azure Container Registry 작업을 사용 하 여 공용 콘텐츠를 사용 하 고 유지 관리 하는 방법](tasks-consume-public-content.md)을 참조 하세요. 

> [!NOTE]
> 미리 구성 된 단일 작업에서 종속 된 기본 이미지를 참조 하는 모든 응용 프로그램 이미지를 자동으로 다시 작성할 수 있습니다. 
 
## <a name="next-steps"></a>다음 단계
 
* Azure에서 컨테이너 이미지를 빌드, 실행, 푸시 및 패치 하는 [ACR 작업](container-registry-tasks-overview.md) 에 대해 자세히 알아보세요.
* 자동화 된 제어 워크플로에 대 한 [Azure Container Registry 작업으로 공용 콘텐츠를 사용 하 고 유지 관리 하는 방법](tasks-consume-public-content.md) 을 참조 하 여 기본 이미지를 환경에 업데이트 합니다. 
* 이미지 빌드 및 업데이트를 자동화 하는 더 많은 예제는 [ACR 작업 자습서](container-registry-tutorial-quick-task.md) 를 참조 하세요.
