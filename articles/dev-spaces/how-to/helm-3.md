---
title: 투구 3을 사용 하도록 Azure Dev Spaces 클러스터 구성 (미리 보기)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: 투구 3을 사용 하도록 Dev Spaces 클러스터를 구성 하는 방법을 알아봅니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 9e3f3ff90d36215c386bf1d8b8ec1edd54ebfb6a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202261"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>투구 3을 사용 하도록 Azure Dev Spaces 클러스터 구성 (미리 보기)

Azure Dev Spaces는 기본적으로 투구 2를 사용 하 여 AKS 클러스터의 Dev 공간에 사용자 서비스를 설치 합니다. 개발 공간에 사용자 서비스를 설치 하는 것이 아니라 투구 3을 사용 하도록 Azure Dev Spaces 수 있습니다. 에서 사용자 서비스를 설치 하는 데 사용 하는 Azure Dev Spaces 투구 버전에 관계 없이, 동일한 클러스터에서 투구 2 또는 3 클라이언트를 사용 하 여 사용자 고유의 릴리스를 관리할 수 있습니다.

투구 3을 사용 하도록 설정 하는 경우 다음과 같은 방법으로 개발 공간에 사용자 서비스를 설치할 때 Azure Dev Spaces 다르게 동작 합니다.

* Tiller는 더 이상 *azds* 네임 스페이스의 클러스터에 배포 되지 않습니다.
* 투구는 *azds* 네임 스페이스 대신 서비스가 설치 되는 네임 스페이스에 릴리스 정보를 저장 합니다.
* 투구 3 릴리스 정보는 컨트롤러를 삭제 한 후 서비스가 설치 되는 네임 스페이스에 유지 됩니다.
* 투구 3 클라이언트를 사용 하 여 클러스터에서 Azure Dev Spaces 하 여 관리 되는 릴리스와 직접 상호 작용할 수 있습니다.

이 가이드에서는 Azure Dev Spaces에 대해 투구 3을 사용 하도록 설정 하 여 개발자 공간에 사용자 서비스를 설치 하는 방법에 대해 설명 합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

### <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치][azure-cli]

### <a name="register-the-helm3preview-preview-feature"></a>Helm3Preview preview 기능 등록

개발자 공간에 사용자 서비스를 설치 하기 위해 투구 3을 사용 하도록 Azure Dev Spaces 하려면 먼저 *az feature register* 명령을 사용 하 여 구독에서 *Helm3Preview* 기능 플래그를 사용 하도록 설정 합니다.

> [!WARNING]
> *Helm3Preview* 기능 플래그를 사용 하 여 Azure Dev Spaces 사용 하도록 설정 하는 모든 AKS 클러스터는이 미리 보기 환경을 사용 합니다. AKS 클러스터에서 완벽 하 게 지원 되는 Azure Dev Spaces을 계속 사용 하도록 설정 하려면 프로덕션 구독에서 미리 보기 기능을 사용 하도록 설정 하지 마세요. 별도의 테스트 또는 개발 Azure 구독을 사용 하 여 미리 보기 기능을 테스트 합니다.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

등록을 완료 하는 데 몇 분 정도 걸립니다. *Az feature show* 명령을 사용 하 여 등록 상태를 확인 합니다.

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

*상태* 를 *등록*한 경우 *az provider Register*를 사용 하 여 *Microsoft devspaces* 등록을 새로 고칩니다.

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>제한 사항

이 기능이 미리 보기 상태인 동안에는 다음과 같은 제한 사항이 적용 됩니다.

* 기존 워크 로드를 사용 하는 AKS 클러스터에서는이 기능을 사용할 수 없습니다. 새 AKS 클러스터를 만들어야 합니다.

## <a name="create-your-cluster"></a>클러스터 만들기

이 미리 보기 기능이 있는 지역에서 새 AKS 클러스터를 만듭니다. 아래 명령은 *Myresourcegroup* 이라는 리소스 그룹과 *미국* *MyAKS* 라는 새 AKS 클러스터를 만듭니다.

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Azure Dev Spaces 사용

AKS 클러스터에서 개발 공간을 사용 하도록 설정 하 고 메시지의 지시에 따라 *사용-개발-공간* 명령을 사용 합니다. 아래 명령을 사용 하 여 *Myresourcegroup* 그룹의 *MyAKS* 클러스터에서 개발 공간을 사용 하도록 설정 하 고 기본 개발 공간을 만듭니다.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Dev 공간에서 투구 3이 실행 되 고 있는지 확인

Tiller가 실행 되 고 있지 않은지 확인 합니다. *azds* 네임 스페이스에 배포를 나열 합니다.

```cmd
kubectl get deployment -n azds
```

Azds 네임 스페이스에서 *tiller* 가 실행 되 고 있지 않은지 확인 합니다. 예를 들면 다음과 같습니다.

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