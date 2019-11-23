---
title: Create a Kubernetes cluster with Azure Dev Spaces enabled - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: 아무것도 설치하지 않고 브라우저에서 바로, Azure Dev Spaces에 사용할 수 있는 Kubernetes 클러스터를 빠르게 만드는 방법을 알아보세요.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: ac825caa7aacbc6ac21dd1f9fe7acbb9cf12e900
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325763"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Create a Kubernetes cluster with Azure Dev Spaces enabled with Azure Cloud Shell

You can use [Azure Cloud Shell](/azure/cloud-shell) to create an Azure Kubernetes Service cluster by using the **Try It** button from this page. 로그인하지 않은 경우 프롬프트에 따라 Azure 계정으로 로그인한 다음, Azure Cloud Shell 프롬프트가 표시되면 명령을 입력합니다.

## <a name="create-the-cluster"></a>클러스터 만들기

First, create the resource group in a [region that supports Azure Dev Spaces][supported-regions].

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
> Many of the Azure Dev Spaces quickstarts and tutorials use the Azure Dev Spaces CLI to perform operations. Azure Cloud Shell에는 Azure Dev Spaces CLI를 설치할 수 없습니다.


[supported-regions]: ../about.md#supported-regions-and-configurations