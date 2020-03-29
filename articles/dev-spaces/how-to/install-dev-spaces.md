---
title: 클라이언트 쪽 도구를 설치하기 & AKS에서 Azure 개발자 공간 사용
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: AKS 클러스터에서 Azure 개발자 공간을 사용하도록 설정하고 클라이언트 쪽 도구를 설치하는 방법을 알아봅니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898942"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>AKS 클러스터에서 Azure 개발자 공간 활성화 및 클라이언트 측 도구 설치

이 문서에서는 AKS 클러스터에서 Azure 개발자 공간을 사용하도록 설정하고 클라이언트 측 도구를 설치하는 여러 가지 방법을 보여 주며 이 문서에서는

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>CLI를 사용하여 Azure 개발자 공간 활성화 또는 제거

CLI를 사용하여 개발자 공간을 활성화하려면 다음이 필요합니다.
* Azure 구독 Azure 구독이 없는 경우 [체험 계정][az-portal-create-account]을 만들 수 있습니다.
* [Azure CLI가 설치되었습니다.][install-cli]
* [지원되는 지역의][supported-regions] [AKS 클러스터입니다.][create-aks-cli]

`use-dev-spaces` 명령을 사용하여 AKS 클러스터에서 Dev Spaces를 사용하도록 설정하고 프롬프트의 지시를 따릅니다.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

위의 명령을 사용하면 *myResourceGroup* 그룹의 *myAKSCluster* 클러스터에 있는 개발자 공간을 활성화하고 *기본* 개발 공간을 만듭니다.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

이 `use-dev-spaces` 명령은 Azure 개발자 공간 CLI도 설치합니다.

AKS 클러스터에서 Azure 개발자 공간을 제거하려면 `azds remove` 명령을 사용합니다. 예를 들어:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

위의 명령은 *MyResourceGroup의* *MyAKS* 클러스터에서 Azure 개발자 공간을 제거합니다. Azure 개발자 공백으로 만든 모든 네임스페이스는 워크로드와 함께 유지되지만 해당 네임스페이스의 새 워크로드는 Azure 개발자 공백으로 계측되지 않습니다. 또한 Azure 개발자 공간으로 계측된 기존 포드를 다시 시작하면 오류가 발생할 수 있습니다. 이러한 포드는 Azure 개발자 공간 도구 없이 다시 배포해야 합니다. 클러스터에서 Azure 개발자 공간을 완전히 제거하려면 Azure 개발자 공간이 활성화된 모든 네임스페이스의 모든 창을 삭제합니다.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 개발자 공간 활성화 또는 제거

Azure 포털을 사용하여 개발자 공간을 활성화하려면 다음이 필요합니다.
* Azure 구독 Azure 구독이 없는 경우 [체험 계정][az-portal-create-account]을 만들 수 있습니다.
* [지원되는 지역의][supported-regions] [AKS 클러스터입니다.][create-aks-portal]

Azure 포털을 사용하여 Azure 개발자 공간을 사용하려면 다음을 수행합니다.
1. [Azure 포털에][az-portal]로그인합니다.
1. AKS 클러스터로 이동합니다.
1. 개발자 *공간* 메뉴 항목을 선택합니다.
1. *Dev Spaces 사용*을 *예*로 변경하고 *저장*을 클릭합니다.

![Azure Portal에서 Dev Spaces를 사용하도록 설정](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Azure 포털을 사용하여 Azure 개발자 공간을 사용하도록 설정하면 Azure 개발자 공간에 대한 클라이언트 쪽 도구가 **설치되지 않습니다.**

AKS 클러스터에서 Azure 개발자 공간을 제거하려면 *개발자 공간 사용 없음을* 변경하고 *저장을*클릭합니다. *No* Azure 개발자 공백으로 만든 모든 네임스페이스는 워크로드와 함께 유지되지만 해당 네임스페이스의 새 워크로드는 Azure 개발자 공백으로 계측되지 않습니다. 또한 Azure 개발자 공간으로 계측된 기존 포드를 다시 시작하면 오류가 발생할 수 있습니다. 이러한 포드는 Azure 개발자 공간 도구 없이 다시 배포해야 합니다. 클러스터에서 Azure 개발자 공간을 완전히 제거하려면 Azure 개발자 공간이 활성화된 모든 네임스페이스의 모든 창을 삭제합니다.

## <a name="install-the-client-side-tools"></a>클라이언트 측 도구 설치

Azure 개발자 공간 클라이언트 쪽 도구를 사용하여 로컬 컴퓨터에서 AKS 클러스터의 개발 공간과 상호 작용할 수 있습니다. 클라이언트 쪽 도구를 설치하는 방법에는 여러 가지가 있습니다.

* [시각적 스튜디오 코드에서][vscode] [Azure 개발자 공간 확장을 설치합니다.][vscode-extension]
* [Visual Studio 2019에서][visual-studio]Azure 개발 워크로드를 설치합니다.
* Visual Studio 2017에서는 [Kubernetes용][visual-studio-k8s-tools]웹 개발 워크로드 및 비주얼 스튜디오 도구를 설치합니다.
* 다운로드 및 [윈도우를][cli-win]설치, [맥,][cli-mac]또는 [리눅스][cli-linux] CLI.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces를 통해 여러 컨테이너에서 더 복잡한 애플리케이션을 개발할 수 있는 방법 및 사용자가 다양한 환경에서 다양한 코드 버전이나 분기로 작업하여 공동 개발을 간소화하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces에서 팀 개발][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
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
