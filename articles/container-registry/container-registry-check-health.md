---
title: Azure Container Registry의 레지스트리 상태 확인
description: 로컬 Docker 구성 및 레지스트리에 대 한 연결을 포함 하 여 Azure container registry를 사용 하는 경우 일반적인 문제를 식별 하는 빠른 진단 명령을 실행 하는 방법 알아보기
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555099"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Azure container registry의 상태를 확인 합니다.

Azure container registry를 사용 하는 경우에 따라 문제를 발생할 수 있습니다. 예를 들어 로컬 환경에서 Docker 사용 하 여 문제로 인해 컨테이너 이미지를 끌어올 수 없습니다. 또는 네트워크 문제가 있습니다 레지스트리에 연결 하지 못할 수 있습니다. 

첫 번째 진단 단계를 실행 합니다 [상태 확인 az acr][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli]합니다.

## <a name="run-az-acr-check-health"></a>Az acr 확인 상태를 실행 합니다.

다음 예제를 실행 하는 다양 한 방법 표시는 `az acr check-health` 명령입니다.

> [!NOTE]
> Azure Cloud Shell의 명령은 실행 하는 경우 로컬 환경 확인 하지 않습니다. 그러나 대상 레지스트리에 대 한 액세스를 확인할 수 있습니다.

### <a name="check-the-environment-only"></a>환경에만 확인 합니다.

로컬 Docker를 확인 하려면 데몬, CLI 버전 및 Helm 클라이언트 구성에 추가 매개 변수 없이 명령을 실행 합니다.

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>환경 및 대상 레지스트리를 확인 합니다.

로컬 환경 검사를 수행할 뿐만 아니라는 레지스트리에 대 한 액세스를 확인, 대상 레지스트리의 이름으로 전달 합니다. 예를 들면 다음과 같습니다.

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>오류 보고

이 명령은 표준 출력에 정보를 기록합니다. 문제가 발견 되는 오류 코드 및 설명을 제공 합니다. 코드 및 가능한 해결 방법에 대 한 자세한 내용은 참조는 [오류 참조](container-registry-health-error-reference.md)합니다.

기본적으로 명령은 오류를 발견할 때마다 중지 합니다. 또한 명령을 실행할 수 있습니다는 모든 상태 검사에 대 한 출력 제공 되도록 오류가 발견 되는 경우에 합니다. 추가 된 `--ignore-errors` 다음 예와에서 같이 매개 변수:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

샘플 출력:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>다음 단계

반환 된 오류 코드에 대 한 자세한를 [상태 확인 az acr][az-acr-check-health] 명령을 참조 하세요를 [상태 확인 오류 참조](container-registry-health-error-reference.md)합니다.

참조 된 [FAQ](container-registry-faq.md) 질문과 대답 및 Azure Container Registry에 대 한 알려진된 기타 문제에 대 한 합니다.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
