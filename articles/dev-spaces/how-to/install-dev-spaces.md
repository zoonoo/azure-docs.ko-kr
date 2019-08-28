---
title: AKS 및 클라이언트 쪽 도구에 Azure Dev Spaces 설치
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/24/2019
ms.topic: conceptual
description: AKS 클러스터에 Azure Dev Spaces를 설치 하 고 클라이언트 쪽 도구를 설치 하는 방법에 대해 알아봅니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
ms.openlocfilehash: 642d4406840f1748c3b6cbb4441227fff0c6d721
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494841"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>AKS 및 클라이언트 쪽 도구에 Azure Dev Spaces 설치

이 문서에서는 AKS 클러스터에 Azure Dev Spaces를 설치 하 고 클라이언트 쪽 도구를 설치 하는 여러 가지 방법을 보여 줍니다.

## <a name="install-azure-dev-spaces-using-the-cli"></a>CLI를 사용 하 여 Azure Dev Spaces 설치

CLI를 사용 하 여 Dev Spaces를 설치 하려면 다음이 필요 합니다.
* Azure 구독. Azure 구독이 없는 경우 [체험 계정][az-portal-create-account]을 만들 수 있습니다.
* [Azure CLI 설치][install-cli]되었습니다.
* [지원 되는 지역의][supported-regions] [AKS 클러스터][create-aks-cli] .

`use-dev-spaces` 명령을 사용하여 AKS 클러스터에서 Dev Spaces를 사용하도록 설정하고 프롬프트의 지시를 따릅니다.

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

위의 명령은 *Myresourcegroup* 그룹의 *MyAKSCluster* 클러스터에서 개발 공간을 사용 하도록 설정 하 고 *기본* 개발 공간을 만듭니다.

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

이 `use-dev-spaces` 명령은 Azure Dev Spaces CLI도 설치 합니다.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Dev Spaces 설치

Azure Portal를 사용 하 여 Dev Spaces를 설치 하려면 다음이 필요 합니다.
* Azure 구독. Azure 구독이 없는 경우 [체험 계정][az-portal-create-account]을 만들 수 있습니다.
* [지원 되는 지역의][supported-regions] [AKS 클러스터][create-aks-portal] .

Azure Portal를 사용 하 여 Azure Dev Spaces를 설치 하려면:
1. [Azure Portal][az-portal]에 로그인합니다.
1. AKS 클러스터로 이동 합니다.
1. *Dev Spaces*를 클릭 합니다.
1. *Dev Spaces 사용*을 *예*로 변경하고 *저장*을 클릭합니다.

![Azure Portal에서 Dev Spaces를 사용하도록 설정](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Azure Portal를 사용 하 여 Azure Dev Spaces를 설치 해도 Azure Dev Spaces에 대 한 클라이언트 쪽 도구는 설치 **되지** 않습니다.

## <a name="install-the-client-side-tooling"></a>클라이언트 쪽 도구 설치

Azure Dev Spaces 클라이언트 쪽 도구를 사용 하 여 로컬 컴퓨터에서 AKS 클러스터의 Dev 공백과 상호 작용할 수 있습니다. 클라이언트 쪽 도구를 설치 하는 방법에는 여러 가지가 있습니다.

* [Visual Studio Code][vscode]에서 [Azure Dev Spaces 확장][vscode-extension]을 설치 합니다.
* [Visual Studio 2019][visual-studio]에서 Azure 개발 워크 로드를 설치 합니다.
* Visual Studio 2017에서 웹 개발 워크 로드를 설치 하 고 [Visual Studio Tools for Kubernetes][visual-studio-k8s-tools]합니다.
* [Windows][cli-win], [Mac][cli-mac]또는 [Linux][cli-linux] CLI를 다운로드 하 여 설치 합니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces를 통해 여러 컨테이너에서 더 복잡한 애플리케이션을 개발할 수 있는 방법 및 사용자가 다양한 환경에서 다양한 코드 버전이나 분기로 작업하여 공동 개발을 간소화하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces에서 팀 개발][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
