---
title: 헬름 3(미리 보기)를 사용하도록 Azure 개발자 공간 클러스터 구성
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Helm 3을 사용하도록 개발자 공간 클러스터를 구성하는 방법에 대해 알아보십시오.
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454298"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>헬름 3(미리 보기)를 사용하도록 Azure 개발자 공간 클러스터 구성

Azure 개발자 공간은 기본적으로 Helm 2를 사용하여 AKS 클러스터의 개발 공간에 사용자 서비스를 설치합니다. 개발 공간에 사용자 서비스를 설치하는 Helm 2 대신 Azure 개발자 공간을 사용하도록 설정할 수 있습니다. 사용자 서비스를 설치하는 데 사용하는 Helm Azure 개발자 공간의 버전에 관계없이 Helm 2 또는 3 클라이언트를 계속 사용하여 동일한 클러스터에서 자체 릴리스를 관리할 수 있습니다.

Helm 3을 사용하도록 설정하면 다음과 같은 방법으로 개발자 공간에 사용자 서비스를 설치할 때 Azure 개발자 공간이 다르게 수행됩니다.

* 틸러는 더 이상 azds 네임스페이스의 클러스터에 *배포되지 않습니다.*
* Helm은 azds 네임스페이스 대신 서비스가 설치된 네임스페이스에 릴리스 정보를 *저장합니다.*
* Helm 3 릴리스 정보는 컨트롤러를 삭제한 후 서비스가 설치된 네임스페이스에 남아 있습니다.
* Helm 3 클라이언트를 사용하여 클러스터의 Azure 개발자 공간에서 관리하는 모든 릴리스와 직접 상호 작용할 수 있습니다.

이 가이드에서는 Azure 개발자 공간에 대해 Helm 3을 사용하여 개발 공간에 사용자 서비스를 설치하는 방법을 알아봅니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치][azure-cli]

### <a name="register-the-helm3preview-preview-feature"></a>Helm3미리보기 미리 보기 기능 등록

Azure 개발자 공간에서 개발 공간에 사용자 서비스를 설치하기 위해 Helm 3을 사용하도록 설정하려면 먼저 *az 기능 레지스터* 명령을 사용하여 구독에서 *Helm3Preview* 기능 플래그를 사용하도록 설정합니다.

> [!WARNING]
> *Helm3Preview* 기능 플래그를 사용하여 Azure 개발자 공간을 사용하도록 설정하는 모든 AKS 클러스터는 이 미리 보기 환경을 사용합니다. AKS 클러스터에서 완전히 지원되는 Azure 개발자 공간을 계속 사용하려면 프로덕션 구독에서 미리 보기 기능을 사용하도록 설정하지 마십시오. 미리 보기 기능을 테스트하기 위해 별도의 테스트 또는 개발 Azure 구독을 사용합니다.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

등록을 완료하는 데 몇 분 정도 걸립니다. *az 기능 표시* 명령을 사용하여 등록 상태를 확인합니다.

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

*상태가* *등록되면* *az 공급자 레지스터를*사용하여 *Microsoft.DevSpaces* 등록을 새로 고칩니다.

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>제한 사항

이 기능이 미리 보기 에 있는 동안 에는 다음과 같은 제한 사항이 적용됩니다.

* 기존 워크로드가 있는 AKS 클러스터에서는 이 기능을 사용할 수 없습니다. 새 AKS 클러스터를 만들어야 합니다.

## <a name="create-your-cluster"></a>클러스터 만들기

이 미리 보기 기능이 있는 리전에서 새 AKS 클러스터를 만듭니다. 아래 명령은 *MyResourceGroup이라는* 리소스 그룹과 *MyAKS라는*새 AKS 클러스터를 만듭니다.

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Azure 개발자 공간 사용

*사용-개발 공간* 명령을 사용하여 AKS 클러스터에서 개발자 공간을 활성화하고 프롬프트를 따릅니다. 아래 명령은 *MyResourceGroup* 그룹의 *MyAKS* 클러스터에서 Dev Spaces를 사용하도록 설정하고 기본 개발 공간을 만듭니다.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>개발 공백이 투구 3를 실행하고 있는지 확인

*azds* 네임스페이스에 배포를 나열하여 틸러가 실행되고 있는지 확인합니다.

```cmd
kubectl get deployment -n azds
```

azds 네임스페이스에서 *틸러 배포가* 실행되고 있지 않은지 확인합니다. 예를 들어:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces를 통해 여러 컨테이너에서 더 복잡한 애플리케이션을 개발할 수 있는 방법 및 사용자가 다양한 환경에서 다양한 코드 버전이나 분기로 작업하여 공동 개발을 간소화하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces에서 팀 개발][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md