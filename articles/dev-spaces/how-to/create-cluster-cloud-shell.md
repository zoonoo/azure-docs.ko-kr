---
title: Azure Cloud Shell을 사용하여 Azure Dev Spaces에 사용할 수 있는 Kubernetes 클러스터를 만드는 방법 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: article
description: 아무것도 설치하지 않고 브라우저에서 바로, Azure Dev Spaces에 사용할 수 있는 Kubernetes 클러스터를 빠르게 만드는 방법을 알아보세요.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 61a466efdacb56889a7e437d84d3c9de43927e65
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705755"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Azure Cloud Shell을 사용하여 Kubernetes 클러스터 만들기

이 페이지의 **사용해 보세요** 단추를 사용하면 [Azure Cloud Shell](/azure/cloud-shell)을 통해 Azure Dev Spaces에 대한 클러스터를 만들 수 있습니다. 로그인하지 않은 경우 프롬프트에 따라 Azure 계정으로 로그인한 다음, Azure Cloud Shell 프롬프트가 표시되면 명령을 입력합니다.

## <a name="create-the-cluster"></a>클러스터 만들기

먼저 리소스 그룹을 만듭니다. 현재 지원되는 지역(미국 동부, 미국 동부 2, 미국 중부, 미국 서부 2, 서유럽, 동남 아시아, 캐나다 중부 또는 캐나다 동부) 중 하나를 사용합니다.

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

다음 명령을 사용하여 Kubernetes 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.3 --enable-addons http_application_routing
```

클러스터를 만드는 데 몇 분이 걸립니다.  완료되면 출력이 JSON 형식으로 표시됩니다. `provisioningState`를 찾아 `Succeeded`인지 확인합니다.

## <a name="next-steps"></a>다음 단계

전체 자습서에 대한 링크는 [Azure Dev Spaces](/azure/dev-spaces/)를 참조하세요.
