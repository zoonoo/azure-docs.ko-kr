---
title: Azure Dev 공간 | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
manager: douge
ms.openlocfilehash: 14b51cc2ad2e8e0f294e5e73e542001e30d21c9d
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2018
ms.locfileid: "41921041"
---
# <a name="azure-dev-spaces"></a>Azure Dev 공간
Azure Dev Spaces는 팀에게 신속하고 반복적인 Kubernetes 개발 환경을 제공합니다. 최소한의 개발 컴퓨터 설정을 통해 AKS(Azure Kubernetes Service)에서 바로 컨테이너를 반복적으로 실행하고 디버그할 수 있습니다. Windows, Mac 또는 Linux에서 Visual Studio, Visual Studio Code 또는 명령줄과 같은 익숙한 도구를 사용하여 개발하세요.

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure Dev 공간이 Kubernetes 개발을 단순화하는 방법 

Azure Dev Spaces는 다음과 같은 방식으로 개발 팀이 Kubernetes에서 생산성을 높이도록 지원합니다.
- 각 팀 멤버의 로컬 개발 컴퓨터 설정을 최소화하고 Azure의 관리되는 Kubernetes 클러스터인 AKS에서 바로 작업합니다.
- Visual Studio 2017 또는 Visual Studio Code를 사용하여 Kubernetes에서 바로 코드를 반복하고 디버깅합니다.
- 개발 단계부터 프로덕션 단계까지 사용할 수 있는 Docker 및 Kubernetes configuration-as-code 자산을 생성합니다. 
- 팀과 관리되는 Kubernetes 클러스터를 공유하고 공동 작업합니다. 격리 상태에서 코드를 개발하고, 종속성을 복제하거나 목업하지 않고 다른 구성 요소로 통합형 테스트를 수행합니다.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>참고 항목

[Azure Kubernetes Service](/azure/aks)