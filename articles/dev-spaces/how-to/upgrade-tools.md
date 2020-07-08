---
title: Azure Dev Spaces 도구를 업그레이드하는 방법
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Azure Dev Spaces 명령줄 도구, Visual Studio Code 확장 및 Visual Studio 확장을 업그레이드하는 방법 알아보기
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
ms.openlocfilehash: 1dad455b834bbef046b295b2cba040831a74f757
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873477"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Azure Dev Spaces 도구를 업그레이드하는 방법

새 릴리스가 있고 Azure Dev Spaces를 이미 사용 중인 경우 Azure Dev Spaces 클라이언트 도구를 업그레이드해야 할 수 있습니다.

## <a name="update-the-azure-cli"></a>Azure CLI 업데이트

최신 Azure CLI를 업데이트하는 경우 최신 버전의 Dev Spaces CLI 확장도 제공됩니다.

이전 버전을 제거할 필요는 없으며 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)에서 적절한 다운로드를 찾기만 하면 됩니다.


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Dev Spaces CLI 확장 및 명령줄 도구 업데이트

다음 명령 실행:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>VS Code 확장 업데이트

확장이 일단 설치된 경우 자동으로 업데이트됩니다. 새 기능을 사용하려면 확장이 다시 로드해야 할 수 있습니다. VS Code에서 **확장** 창을 열고 **Azure Dev Spaces** 확장을 선택한 후 **다시 로드**를 선택합니다.

## <a name="update-visual-studio"></a>Visual Studio 업데이트

Azure Dev Spaces는 Azure 개발 워크로드의 일부이며, 모든 Visual Studio 업데이트에 포함됩니다.

## <a name="next-steps"></a>다음 단계

새 클러스터를 만들어 새 도구를 테스트합니다. [Azure Dev Spaces](/azure/dev-spaces)에서 빠른 시작 및 자습서를 진행합니다.
