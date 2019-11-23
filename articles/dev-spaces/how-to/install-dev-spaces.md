---
title: Install Azure Dev Spaces on AKS & the client-side tooling
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Learn how to install Azure Dev Spaces on an AKS cluster and install the client-side tooling.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 2649b36c96313d4a7d878a1c72c3b175ad0f4d30
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325774"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Install Azure Dev Spaces on AKS and the client-side tooling

This article shows you several ways to install Azure Dev Spaces on an AKS cluster as well as install the client-side tooling.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Install Azure Dev Spaces using the CLI

Before you can install Dev Spaces using the CLI, you need:
* Azure 구독. Azure 구독이 없는 경우 [체험 계정][az-portal-create-account]을 만들 수 있습니다.
* [The Azure CLI installed][install-cli].
* [An AKS cluster][create-aks-cli] in a [supported region][supported-regions].

`use-dev-spaces` 명령을 사용하여 AKS 클러스터에서 Dev Spaces를 사용하도록 설정하고 프롬프트의 지시를 따릅니다.

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

The above command enables Dev Spaces on the *myAKSCluster* cluster in the *myResourceGroup* group and creates a *default* dev space.

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

The `use-dev-spaces` command also installs the Azure Dev Spaces CLI.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Install Azure Dev Spaces using the Azure portal

Before you can install Dev Spaces using the Azure portal, you need:
* Azure 구독. Azure 구독이 없는 경우 [체험 계정][az-portal-create-account]을 만들 수 있습니다.
* [An AKS cluster][create-aks-portal] in a [supported region][supported-regions].

To install Azure Dev Spaces using the Azure portal:
1. [Azure portal][az-portal]에 로그인합니다.
1. Navigate to your AKS cluster.
1. Click *Dev Spaces*.
1. *Dev Spaces 사용*을 *예*로 변경하고 *저장*을 클릭합니다.

![Azure Portal에서 Dev Spaces를 사용하도록 설정](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Installing Azure Dev Spaces using the Azure portal **does not** install any client-side tooling for Azure Dev Spaces.

## <a name="install-the-client-side-tooling"></a>Install the client-side tooling

You can use the Azure Dev Spaces client-side tooling to interact with dev spaces on an AKS cluster from your local machine. There are several ways to install the client-side tooling:

* In [Visual Studio Code][vscode], install the [Azure Dev Spaces extension][vscode-extension].
* In [Visual Studio 2019][visual-studio], install the Azure Development workload.
* In Visual Studio 2017, install the Web Development workload and [Visual Studio Tools for Kubernetes][visual-studio-k8s-tools].
* Download and install the [Windows][cli-win], [Mac][cli-mac], or [Linux][cli-linux] CLI.

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
