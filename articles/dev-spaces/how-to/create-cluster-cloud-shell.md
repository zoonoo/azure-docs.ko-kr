---
title: Azure Cloud Shell을 사용 하 여 Azure 개발 공백을 사용 하도록 설정 하는 Kubernetes 클러스터를 만드는 방법
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: 아무것도 설치하지 않고 브라우저에서 바로, Azure Dev Spaces에 사용할 수 있는 Kubernetes 클러스터를 빠르게 만드는 방법을 알아보세요.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
ms.openlocfilehash: cd0c8c3c26feefe3448ada1cf1575706cd17e525
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808695"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Azure Cloud Shell을 사용하여 Kubernetes 클러스터 만들기

사용할 수 있습니다 [Azure Cloud Shell](/azure/cloud-shell) 사용 하 여 Azure Kubernetes Service 클러스터를 만들려면 합니다 **시도** 이 페이지에서 단추입니다. 로그인하지 않은 경우 프롬프트에 따라 Azure 계정으로 로그인한 다음, Azure Cloud Shell 프롬프트가 표시되면 명령을 입력합니다.

## <a name="create-the-cluster"></a>클러스터 만들기

먼저 리소스 그룹을 만듭니다는 [Azure 개발 공간을 지 원하는 지역을][supported-regions]합니다.

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

다음 명령을 사용하여 Kubernetes 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

클러스터를 만드는 데 몇 분이 걸립니다.  완료되면 출력이 JSON 형식으로 표시됩니다. `provisioningState`를 찾아 `Succeeded`인지 확인합니다.

## <a name="next-steps"></a>다음 단계

전체 자습서에 대한 링크는 [Azure Dev Spaces](/azure/dev-spaces/)를 참조하세요.

> [!IMPORTANT]
> Azure 개발 공간 CLI를 사용 하 여 다른 Azure 개발자 공간 퀵 스타트 및 자습서의 대부분 작업을 수행 하 합니다. Azure Cloud Shell에서 Azure 개발 공간 CLI를 설치할 수 없습니다.


[supported-regions]: ../about.md#supported-regions-and-configurations