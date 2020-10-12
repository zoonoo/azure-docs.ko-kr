---
title: Azure Dev Spaces와 함께 kubectl를 사용 하는 방법
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Azure Dev Spaces 사용 하도록 설정 된 Azure Kubernetes Service 클러스터의 개발 공간 내에서 kubectl 명령을 사용 하는 방법을 알아봅니다.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75438359"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Azure Dev Space로 kubectl 사용

Azure Dev Space 내에서 Kubernetes 클러스터에 액세스할 수 있으며 `kubectl`과 같은 기존 Kubernetes 도구를 사용할 수 있습니다.

`az aks use-dev-spaces` 명령을 실행하면 `kubectl` 구성 컨텍스트가 자동으로 추가되므로 kubectl은 Azure Dev Spaces Kubernetes 클러스터에 이미 연결되어 있어야 합니다. 예제:
- 현재 컨텍스트를 확인합니다. `kubectl config current-context`
- 사용 가능한 모든 컨텍스트를 나열합니다. `kubectl config get-contexts` 
- 컨텍스트를 변경합니다. `kubectl config use-context <context-name>`
- Kubernetes 대시보드를 봅니다. `kubectl proxy`를 실행한 다음, 이 명령이 내보내는 주소에 대한 브라우저를 엽니다(URL에 `/ui`를 추가하여 Kubernetes 대시보드로 이동).
- 기본 Azure Dev Spaces 공간의 기본으로 실행 중인 서비스를 나열 *합니다.*`kubectl get services --namespace=default`

