---
title: 기본 제공 역할 Windows 가상 데스크톱-Azure
description: Azure RBAC에서 사용할 수 있는 Windows 가상 데스크톱의 기본 제공 역할에 대 한 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 52f0151fd4be7505cf7beea0eeb54f8e34404997
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577702"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Windows 가상 데스크톱의 기본 제공 역할

Windows 가상 데스크톱은 Azure 역할 기반 액세스 제어 (RBAC)를 사용 하 여 사용자 및 관리자에 게 역할을 할당 합니다. 이러한 역할은 관리자에 게 특정 작업을 수행할 수 있는 권한을 부여 합니다. Azure RBAC의 기본 제공 역할에 대해 자세히 알아보려면 [azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조 하세요.

Azure에 대 한 표준 기본 제공 역할은 소유자, 참가자 및 읽기 권한자입니다. 그러나 Windows 가상 데스크톱에는 호스트 풀, 앱 그룹 및 작업 영역에 대 한 관리 역할을 구분할 수 있는 추가 역할이 있습니다. 이러한 분리를 통해 관리 작업을 보다 세부적으로 제어할 수 있습니다. 이러한 역할은 Azure의 표준 역할 및 최소 권한 방법을 준수 하 여 명명 됩니다.

Windows 가상 데스크톱에는 특정 소유자 역할이 없습니다. 그러나 서비스 개체에 대 한 표준 소유자 역할을 사용할 수 있습니다.

## <a name="desktop-virtualization-contributor"></a>데스크톱 가상화 기여자

데스크톱 가상화 참가자 역할을 사용 하면 배포의 모든 측면을 관리할 수 있습니다. 그러나 계산 리소스에 대 한 액세스 권한은 부여 하지 않습니다. 또한 사용자 액세스 관리자 역할을 사용 하 여 사용자 또는 사용자 그룹에 앱 그룹을 게시 해야 합니다.


- Microsoft DesktopVirtualization/\* 
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-reader"></a>데스크톱 가상화 판독기

데스크톱 가상화 판독기 역할을 사용 하면 배포의 모든 항목을 볼 수 있지만 변경할 수는 없습니다.

- Microsoft DesktopVirtualization/ \* /cread
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-contributor"></a>호스트 풀 기여자

호스트 풀 기여자 역할을 사용 하면 리소스에 대 한 액세스를 포함 하 여 호스트 풀의 모든 측면을 관리할 수 있습니다. 가상 컴퓨터를 만들려면 추가 참가자 역할, 가상 컴퓨터 참가자가 필요 합니다. 포털을 사용 하 여 호스트 풀을 만들려면 AppGroup 및 작업 영역 참가자 역할이 필요 하며, 데스크톱 가상화 참가자 역할을 사용할 수도 있습니다.

다음 목록에서는이 역할에 액세스할 수 있는 권한을 설명 합니다.

- Microsoft DesktopVirtualization/hostpools/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-reader"></a>호스트 풀 판독기

호스트 풀 판독기 역할을 사용 하면 호스트 풀의 모든 항목을 볼 수 있지만 변경할 수는 없습니다.

- Microsoft DesktopVirtualization/hostpools// \* > 읽기
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-contributor"></a>응용 프로그램 그룹 기여자

응용 프로그램 그룹 참가자 역할을 사용 하면 앱 그룹의 모든 측면을 관리할 수 있습니다. 사용자 또는 사용자 그룹에 앱 그룹을 게시 하려면 사용자 액세스 관리자 역할이 필요 합니다.

다음 목록에서는이 역할에 액세스할 수 있는 권한을 설명 합니다.

- Microsoft DesktopVirtualization/applicationgroups/\*
- Microsoft DesktopVirtualization/hostpools/읽기
- Microsoft DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-reader"></a>응용 프로그램 그룹 판독기

응용 프로그램 그룹 읽기 권한자 역할을 사용 하면 앱 그룹의 모든 항목을 볼 수 있으며 변경할 수 없습니다.

다음 목록에서는이 역할에 액세스할 수 있는 권한을 설명 합니다.

- Microsoft. DesktopVirtualization/applicationgroups// \* > 읽기
- Microsoft DesktopVirtualization/applicationgroups/read
- Microsoft DesktopVirtualization/hostpools/읽기
- Microsoft DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-contributor"></a>작업 영역 참가자

작업 영역 참가자 역할을 사용 하면 작업 영역의 모든 측면을 관리할 수 있습니다. 앱 그룹에 추가 된 응용 프로그램에 대 한 정보를 얻으려면 응용 프로그램 그룹 읽기 권한자 역할에도 할당 해야 합니다.

다음 목록에서는이 역할에 액세스할 수 있는 권한을 설명 합니다.

- Microsoft DesktopVirtualization/작업 영역/\*
- Microsoft DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-reader"></a>작업 영역 판독기

작업 영역 판독기 역할을 사용 하면 작업 영역에서 모든 항목을 볼 수 있지만 변경할 수는 없습니다.

다음 목록에서는이 역할에 액세스할 수 있는 권한을 설명 합니다.

- Microsoft DesktopVirtualization/작업 영역/읽기
- Microsoft DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="user-session-operator"></a>사용자 세션 운영자

사용자 세션 운영자 역할을 사용 하면 메시지를 보내고, 세션의 연결을 해제 하 고, "로그 오프" 함수를 사용 하 여 세션 호스트에서 세션을 서명할 수 있습니다. 그러나이 역할을 통해 세션 호스트 제거, 드레이닝 모드 변경 등의 세션 호스트 관리를 수행할 수 없습니다. 이 역할은 할당을 볼 수 있지만 관리자는 수정할 수 없습니다. 특정 호스트 풀에이 역할을 할당 하는 것이 좋습니다. 리소스 그룹 수준에서이 사용 권한을 부여 하는 경우 관리자는 리소스 그룹 아래의 모든 호스트 풀에 대해 읽기 권한을 가집니다.

다음 목록에서는이 역할에 액세스할 수 있는 권한을 설명 합니다.

- Microsoft DesktopVirtualization/hostpools/읽기
- Microsoft DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="session-host-operator"></a>세션 호스트 운영자

세션 호스트 참여자 역할을 사용 하면 세션 호스트를 보고 제거할 수 있을 뿐만 아니라 드레이닝 모드를 변경할 수 있습니다. 호스트 풀 개체에 대 한 쓰기 권한이 없기 때문에 Azure Portal를 사용 하 여 세션 호스트를 추가할 수 없습니다. 등록 토큰이 유효한 경우 (생성 되 고 만료 되지 않음)이 역할을 사용 하 여 관리자에 게 가상 컴퓨터 참가자 역할을 통해 계산 권한이 있는 경우이 역할을 사용 하 여 Azure Portal 외부의 호스트 풀에 세션 호스트를 추가할 수 있습니다.

다음 목록에서는이 역할에 액세스할 수 있는 권한을 설명 합니다.

- Microsoft DesktopVirtualization/hostpools/읽기
- Microsoft DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*
