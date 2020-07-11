---
title: Azure Dev Spaces 공유 하는 방법
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Azure Dev Spaces를 사용 하 여 Azure Kubernetes Service의 개발 공간을 팀의 다른 사용자와 공유 하는 방법을 알아봅니다.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: 3cef7cd6a5113401cb1df3dd6c76e4dea3a7524d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229298"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces 공유

Azure Dev Spaces를 사용하여 팀의 다른 사용자와 개발 공간을 공유할 수 있습니다. 각 개발자는 다른 사용자를 중단시킬 걱정 없이 자체 공간에서 작업할 수 있습니다. 또한 하나의 공간에서 함께 작업하면 모의 개체를 만들거나 종속성을 시뮬레이션할 필요 없이 엔드투엔드 코드를 테스트할 수 있습니다. 자세한 내용은 [팀 개발 알아보기](../team-development-nodejs.md) 가이드를 참조하세요.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>여러 개발자를 위한 개발 공간 설정

1. Azure에서 Dev Space를 만듭니다. [.NET Core 및 VS Code](../get-started-netcore.md), [.NET Core 및 Visual Studio](../get-started-netcore-visualstudio.md) 또는 [Node.js 및 VS Code](../get-started-nodejs.md)를 선택합니다. 선택한 Azure 구독에 대한 소유자 또는 기여자 액세스 권한이 있어야 합니다.
1. 각 팀 멤버에 게 [Azure Dev Spaces 컨트롤러에 액세스할 수 있는 적절 한 권한이](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis)있는지 확인 합니다. 예를 들어 각 팀 멤버에 대 한 [참가자 액세스 권한을 부여](../../role-based-access-control/role-assignments-portal.md) 하도록 Azure Dev 공간의 **리소스 그룹** 을 구성할 수 있습니다. `azds show-context` 명령을 실행하여 개발 공간의 리소스 그룹을 확인할 수 있습니다.
1. 개발 공간에서 개발할 수 있도록 **개발 공간을 선택**하라고 팀 멤버에게 요청합니다.
   * **명령줄 또는 VS Code**: 액세스 권한이 있는 기존 Azure Dev Spaces를 보려면 `azds space list` 명령을 사용합니다. 개발 공간을 선택하려면 `azds space select` 명령을 사용합니다.
   * **Visual Studio IDE**: Visual Studio에서 프로젝트를 열고, 시작 설정 드롭다운에서 **Azure Dev Spaces**를 선택합니다. 열리는 대화 상자에서 기존 클러스터를 선택합니다.

     ![Visual Studio 시작 설정 드롭다운](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [팀 개발 알아보기](../team-development-nodejs.md)를 참조하세요.
