---
title: Azure 개발자 공간을 사용하도록 설정한 Kubernetes 클러스터 만들기 - Azure 클라우드 셸
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: 아무것도 설치하지 않고 브라우저에서 바로, Azure Dev Spaces에 사용할 수 있는 Kubernetes 클러스터를 빠르게 만드는 방법을 알아보세요.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605311"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Azure 클라우드 셸에서 사용하도록 설정된 Azure 개발자 공간을 사용하여 Kubernetes 클러스터 만들기

이 페이지에서 **Try It** 단추를 사용하여 [Azure 클라우드 셸을](/azure/cloud-shell) 사용하여 Azure Kubernetes 서비스 클러스터를 만들 수 있습니다. 로그인하지 않은 경우 프롬프트에 따라 Azure 계정으로 로그인한 다음, Azure Cloud Shell 프롬프트가 표시되면 명령을 입력합니다.

## <a name="create-the-cluster"></a>클러스터 만들기

먼저 Azure 개발자 공간을 [지원하는 리전에서][supported-regions]리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

다음 명령을 사용하여 Kubernetes 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

클러스터를 만드는 데 몇 분이 걸립니다.  완료되면 출력이 JSON 형식으로 표시됩니다. `provisioningState`를 찾아 `Succeeded`인지 확인합니다.

## <a name="next-steps"></a>다음 단계

전체 자습서에 대한 링크는 [Azure Dev Spaces](/azure/dev-spaces/)를 참조하세요.

> [!IMPORTANT]
> 대부분의 Azure 개발자 공간 빠른 시작 및 자습서는 Azure 개발자 공간 CLI를 사용하여 작업을 수행합니다. Azure Cloud Shell에는 Azure Dev Spaces CLI를 설치할 수 없습니다.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service