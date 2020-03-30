---
title: Azure 센티넬의 사용 권한 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Sentinel이 역할 기반 액세스 제어를 사용하여 사용자에게 권한을 할당하고 각 역할에 대해 허용된 작업을 식별하는 방법을 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587178"
---
# <a name="permissions-in-azure-sentinel"></a>Azure 센티넬의 사용 권한

Azure Sentinel은 [RBAC(역할 기반 액세스 제어)를](../role-based-access-control/role-assignments-portal.md)사용하여 Azure의 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 [역할을](../role-based-access-control/built-in-roles.md) 제공합니다.

RBAC를 사용하면 보안 운영 팀 내에서 역할을 사용하고 만들어 Azure Sentinel에 대한 적절한 액세스 권한을 부여할 수 있습니다. 역할에 따라 Azure Sentinel에 액세스할 수 있는 사용자가 볼 수 있는 내용을 세밀하게 제어할 수 있습니다. Azure Sentinel 작업 영역에서 직접 또는 작업 영역이 속한 구독 또는 리소스 그룹에 RBAC 역할을 할당할 수 있습니다.

세 가지 특정 기본 제공 Azure Sentinel 역할이 있습니다.  
**모든 Azure Sentinel 기본 제공 역할은 Azure Sentinel 작업 영역의 데이터에 대한 읽기 액세스 권한을 부여합니다.**
- [Azure 센티넬 리더](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure 센티넬 응답자](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure 센티넬 기여자](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Azure Sentinel 전용 RBAC 역할 외에도 Azure Sentinel 작업 영역 및 기타 리소스에 대한 액세스를 포함하는 광범위한 사용 권한 집합을 부여할 수 있는 Azure 및 Log Analytics RBAC 역할이 있습니다.

- **Azure 역할:** [소유자,](../role-based-access-control/built-in-roles.md#owner) [기여자](../role-based-access-control/built-in-roles.md#contributor)및 [판독기](../role-based-access-control/built-in-roles.md#reader). Azure 역할은 로그 분석 작업 영역 및 Azure Sentinel 리소스를 비롯한 모든 Azure 리소스에 대한 액세스 권한을 부여합니다.

-   **로그 분석 역할:** [로그 분석 기여자,](../role-based-access-control/built-in-roles.md#log-analytics-contributor) [로그 분석 리더](../role-based-access-control/built-in-roles.md#log-analytics-reader). 로그 애널리틱스 역할은 모든 로그 애널리틱스 작업 영역에 액세스 권한을 부여합니다. 

> [!NOTE]
> 또한 Log Analytics 역할은 모든 Azure 리소스에서 읽기 액세스 권한을 부여하지만 로그 애널리틱스 리소스에 쓰기 권한만 할당합니다.


예를 들어 **Azure Sentinel 판독기** 및 **Azure 기여자(Azure** **Sentinel 기여자)** 역할로 할당된 사용자는 **Sentinel 판독기** 권한만 있지만 Azure Sentinel에서 데이터를 편집할 수 있습니다. 따라서 Azure Sentinel에서만 사용 권한을 부여하려면 다른 리소스에 필요한 사용 권한 역할을 중단하지 않도록 이 사용자의 이전 권한을 신중하게 제거해야 합니다.

> [!NOTE]
>- Azure Sentinel은 자동화된 위협 대응을 위해 플레이북을 사용합니다. 플레이북은 Azure 논리 앱을 활용하며 별도의 Azure 리소스입니다. 보안 오케스트레이션, 자동화 및 SOAR(응답) 작업에 논리 앱을 사용할 수 있는 옵션으로 보안 운영 팀의 특정 구성원을 할당할 수 있습니다. [로직 앱 기여자](../role-based-access-control/built-in-roles.md#logic-app-contributor) 역할 또는 [논리 앱 연산자](../role-based-access-control/built-in-roles.md#logic-app-operator) 역할을 사용하여 플레이북 사용에 대한 명시적 권한을 할당할 수 있습니다.
>- 데이터 커넥터를 추가하려면 각 커넥터에 필요한 역할이 커넥터 유형별로 수행되며 관련 커넥터 페이지에 나열됩니다. 또한 데이터 원본을 연결하려면 Azure Sentinel 작업 영역에 대한 쓰기 권한이 있어야 합니다.



## <a name="roles-and-allowed-actions"></a>역할 및 허용되는 작업

다음 표에는 Azure Sentinel에서 역할 및 허용된 작업이 표시됩니다. X는 작업이 해당 역할에 대해 허용된다는 것을 나타냅니다.

| 역할 | 플레이북 만들기 및 실행| 대시보드, 분석 규칙 및 기타 Azure Sentinel 리소스 생성 및 편집 | 인시던트 관리(해제, 할당 등) | 데이터, 인시던트, 대시보드 및 기타 Azure Sentinel 리소스 보기 |
|--- |---|---|---|---|
| Azure 센티넬 리더 | -- | -- | -- | X |
| Azure 센티넬 응답자|--|--| X | X |
| Azure 센티넬 기여자 | -- | X | X | X |
| Azure Sentinel 기여자 + 논리 앱 기여자 | X | X | X | X |


> [!NOTE]
> - 사용자가 자신의 작업을 완료하는 데 필요한 최소한의 역할을 할당하는 것이 좋습니다. 예를 들어 규칙 또는 대시보드를 만들어야 하는 사용자에게만 Azure Sentinel 기여자 역할을 할당합니다.
> - 사용자가 동일한 리소스 그룹의 모든 Azure Sentinel 작업 영역에 액세스할 수 있도록 리소스 그룹 범위에서 Azure Sentinel에 대한 사용 권한을 설정하는 것이 좋습니다.
>
## <a name="building-custom-rbac-roles"></a>사용자 지정 RBAC 역할 빌드

기본 제공 RBAC 역할을 사용하는 대신 Azure Sentinel에 대한 사용자 지정 RBAC 역할을 만들 수 있습니다. Azure Sentinel에 대한 사용자 지정 RBAC 역할은 [Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) 및 [Azure Log Analytics 리소스에](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)대한 특정 사용 권한을 기반으로 다른 [사용자 지정 Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) 역할을 만드는 것과 동일한 방식으로 만들어집니다.

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Azure Sentinel에 저장하는 데이터에 대한 고급 RBAC
  
Azure Sentinel 작업 영역의 데이터 에서 Log Analytics 고급 역할 기반 액세스 제어를 사용할 수 있습니다. 여기에는 데이터 유형당 역할 기반 액세스 제어와 리소스 중심 역할 기반 액세스 제어가 모두 포함됩니다. 로그 분석 역할에 대한 자세한 내용은 [Azure Monitor의 로그 데이터 및 작업 영역 관리를](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)참조하십시오.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel 사용자에 대한 역할로 작업하는 방법과 각 역할을 통해 사용자가 수행할 수 있는 작업을 배웠습니다.

* [Azure 센티넬 블로그](https://aka.ms/azuresentinelblog). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
