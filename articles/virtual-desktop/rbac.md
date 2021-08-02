---
title: 기본 제공 역할 Azure Virtual Desktop - Azure
description: Azure RBAC에 사용할 수 있는 Azure Virtual Desktop의 기본 제공 역할에 대한 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: f19035ea08e95d5535903467ddbc11fc53c0deb7
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753110"
---
# <a name="built-in-roles-for-azure-virtual-desktop"></a>Azure Virtual Desktop의 기본 제공 역할

Azure Virtual Desktop은 Azure RBAC(역할 기반 액세스 제어)를 사용하여 사용자 및 관리자에게 역할을 할당합니다. 이러한 역할은 관리자에게 특정 작업을 수행할 수 있는 권한을 부여합니다. Azure RBAC의 기본 제공 역할에 대해 자세히 알아보려면 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

Azure의 표준 기본 제공 역할은 소유자, 기여자 및 리더입니다. 그러나 Azure Virtual Desktop에는 호스트 풀, 앱 그룹 및 작업 영역에 대한 관리 역할을 구분할 수 있는 추가 역할이 있습니다. 이러한 분리를 통해 관리 작업을 보다 세부적으로 제어할 수 있습니다. 이러한 역할은 Azure의 표준 역할 및 최소 권한 방법에 따라 명명됩니다.

Azure Virtual Desktop에는 특정 소유자 역할이 없습니다. 그러나 서비스 개체에 대해 표준 소유자 역할을 사용할 수 있습니다.

## <a name="desktop-virtualization-contributor"></a>데스크톱 가상화 기여자

데스크톱 가상화 기여자 역할을 통해 배포의 모든 측면을 관리할 수 있습니다. 그러나 컴퓨팅 리소스에 대한 액세스 권한은 부여하지 않습니다. 사용자 또는 사용자 그룹에 앱 그룹을 게시하려면 사용자 액세스 관리자 역할도 필요합니다.


- Microsoft.DesktopVirtualization/\* 
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-reader"></a>데스크톱 가상화 읽기 권한자

데스크톱 가상화 리더 역할을 사용하면 배포의 모든 항목을 볼 수 있지만 변경할 수는 없습니다.

- Microsoft.DesktopVirtualization/\*/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-contributor"></a>호스트 풀 기여자

호스트 풀 기여자 역할을 사용하면 리소스에 대한 액세스를 포함하여 호스트 풀의 모든 측면을 관리할 수 있습니다. 가상 머신을 만들려면 추가 기여자 역할인 Virtual Machine Contributor가 필요합니다. 포털을 사용하여 호스트 풀을 만들려면 AppGroup 및 작업 영역 기여자 역할이 필요하거나 데스크톱 가상화 기여자 역할을 사용할 수도 있습니다.

다음 목록에서는 이 역할에 액세스할 수 있는 권한을 설명합니다.

- Microsoft.DesktopVirtualization/hostpools/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-reader"></a>호스트 풀 리더

호스트 풀 리더 역할을 사용하면 호스트 풀의 모든 항목을 볼 수 있지만 변경할 수는 없습니다.

- Microsoft.DesktopVirtualization/hostpools/\*/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-contributor"></a>애플리케이션 그룹 기여자

애플리케이션 그룹 기여자 역할을 사용하면 앱 그룹의 모든 측면을 관리할 수 있습니다. 앱 그룹을 사용자 또는 사용자 그룹에 게시하려면 사용자 액세스 관리자 역할이 필요합니다.

다음 목록에서는 이 역할에 액세스할 수 있는 권한을 설명합니다.

- Microsoft.DesktopVirtualization/applicationgroups/\*
- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-reader"></a>애플리케이션 그룹 리더

애플리케이션 그룹 리더 역할을 사용하면 앱 그룹의 모든 항목을 볼 수 있지만 변경할 수는 없습니다.

다음 목록에서는 이 역할에 액세스할 수 있는 권한을 설명합니다.

- Microsoft.DesktopVirtualization/applicationgroups/\*/read
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-contributor"></a>작업 영역 기여자

작업 영역 기여자 역할을 사용하면 작업 영역의 모든 측면을 관리할 수 있습니다. 앱 그룹에 추가된 애플리케이션에 대한 정보를 가져오려면 애플리케이션 그룹 리더 역할도 할당받아야 합니다.

다음 목록에서는 이 역할에 액세스할 수 있는 권한을 설명합니다.

- Microsoft.DesktopVirtualization/workspaces/\*
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-reader"></a>작업 영역 리더

작업 영역 리더 역할을 사용하면 작업 영역의 모든 항목을 볼 수 있지만 변경할 수는 없습니다.

다음 목록에서는 이 역할에 액세스할 수 있는 권한을 설명합니다.

- Microsoft.DesktopVirtualization/workspaces/read
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="user-session-operator"></a>사용자 세션 운영자

사용자 세션 운영자 역할을 사용하면 메시지를 보내고, 세션의 연결을 해제하고, "로그오프" 함수를 사용하여 세션 호스트에서 세션을 로그아웃할 수 있습니다. 그러나 이 역할을 통해 세션 호스트 제거, 드레인 모드 변경 등의 세션 호스트 관리를 수행할 수 없습니다. 이 역할은 할당을 볼 수 있지만 관리자를 수정할 수는 없습니다. 특정 호스트 풀에 이 역할을 할당하는 것이 좋습니다. 리소스 그룹 수준에서 이 권한을 부여하면 관리자는 리소스 그룹 아래의 모든 호스트 풀에 대한 읽기 권한을 갖게 됩니다.

다음 목록에서는 이 역할에 액세스할 수 있는 권한을 설명합니다.

- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="session-host-operator"></a>세션 호스트 운영자

세션 호스트 운영자 역할을 사용하면 세션 호스트를 보고 제거할 뿐만 아니라 드레인 모드도 변경할 수 있습니다. 호스트 풀 개체에 대한 쓰기 권한이 없으므로 Azure Portal을 사용하여 세션 호스트를 추가할 수 없습니다. 등록 토큰이 유효하고(생성되고 만료되지 않음) 관리자에게 Virtual Machine Contributor 역할을 통해 컴퓨팅 권한이 있는 경우 이 역할을 사용하여 Azure Portal 외부의 호스트 풀에 세션 호스트를 추가할 수 있습니다.

다음 목록에서는 이 역할에 액세스할 수 있는 권한을 설명합니다.

- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*
