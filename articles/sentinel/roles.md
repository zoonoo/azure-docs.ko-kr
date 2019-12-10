---
title: Azure 센티널의 권한 | Microsoft Docs
description: 이 문서에서는 Azure 센티널에서 역할 기반 액세스 제어를 사용 하 여 사용자에 게 권한을 할당 하 고 각 역할에 대해 허용 되는 작업을 식별 하는 방법을 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: rkarlin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: rkarlin
ms.openlocfilehash: 0479eba13fd747ea1bcab2672fce1df2156802e8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947589"
---
# <a name="permissions-in-azure-sentinel"></a>Azure 센티널의 사용 권한

Azure 센티널은 [RBAC (역할 기반 Access Control)](../role-based-access-control/role-assignments-portal.md)를 사용 하 여 Azure의 사용자, 그룹 및 서비스에 할당할 수 있는  [기본 제공 역할](../role-based-access-control/built-in-roles.md) 을 제공 합니다.

RBAC를 사용 하 여 보안 운영 팀 내에서 역할을 만들고 Azure 센티널에 적절 한 액세스 권한을 부여할 수 있습니다. 역할에 따라 Azure 센티널에 대 한 액세스 권한이 있는 사용자가 볼 수 있는 기능을 세분화 하 여 제어할 수 있습니다. Azure 센티널 작업 영역에서 직접 또는 작업 영역이 속한 구독 또는 리소스 그룹에 RBAC 역할을 할당할 수 있습니다.

세 가지 기본 제공 Azure 센티널 역할이 있습니다.  
**모든 Azure 센티널 기본 제공 역할은 Azure 센티널 작업 영역에서 데이터에 대 한 읽기 액세스 권한을 부여 합니다.**
- [Azure 센티널 판독기](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure 센티널 응답자](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure 센티널 기여자](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Azure 센티널 전용 RBAC 역할 외에도 azure 및 Log Analytics RBAC 역할은 Azure 센티널 작업 영역 및 기타 리소스에 대 한 액세스를 포함 하는 광범위 한 사용 권한 집합을 부여할 수 있습니다.

- **Azure 역할:** [소유자](../role-based-access-control/built-in-roles.md#owner), [참가자](../role-based-access-control/built-in-roles.md#contributor)및 [읽기 권한자](../role-based-access-control/built-in-roles.md#reader)입니다. Azure 역할은 Log Analytics 작업 영역 및 Azure 센티널 리소스를 포함 하 여 모든 Azure 리소스에 대 한 액세스 권한을 부여 합니다.

-   **역할 Log Analytics:** [Log Analytics 참가자](../role-based-access-control/built-in-roles.md#log-analytics-contributor) [Log Analytics 읽기 권한자](../role-based-access-control/built-in-roles.md#log-analytics-reader)입니다. Log Analytics 역할은 모든 Log Analytics 작업 영역에서 액세스 권한을 부여 합니다. 

> [!NOTE]
> 또한 Log Analytics 역할은 모든 Azure 리소스에 대 한 읽기 액세스 권한을 부여 하지만 Log Analytics 리소스에 대 한 쓰기 권한만 할당 합니다.


예를 들어 azure 센티널 **판독기** 및 **Azure 참여자** ( **azure 센티널 참여자**아님) 역할을 사용 하 여 할당 된 사용자는 **센티널 판독기** 권한만 있는 경우에도 azure 센티널에서 데이터를 편집할 수 있습니다. 따라서 Azure 센티널에서에만 권한을 부여 하려는 경우에는이 사용자의 이전 권한을 신중 하 게 제거 하 여 다른 리소스에 필요한 권한 역할을 중단 하지 않도록 해야 합니다.

> [!NOTE]
>- Azure 센티널은 자동화 된 위협 대응을 위해 플레이 북을 사용 합니다. 플레이 북은 Azure Logic Apps를 활용 하 고 별도의 Azure 리소스입니다. 보안 오케스트레이션, 자동화 및 응답 (대화 충성도) 작업에 Logic Apps를 사용 하는 옵션을 사용 하 여 보안 운영 팀의 특정 멤버를 할당할 수 있습니다. [논리 앱 참가자](../role-based-access-control/built-in-roles.md#logic-app-contributor) 역할 또는 [논리 앱 운영자](../role-based-access-control/built-in-roles.md#logic-app-operator) 역할을 사용 하 여 playbooks 사용에 대 한 명시적 권한을 할당할 수 있습니다.
>- 데이터 커넥터를 추가 하기 위해 각 커넥터에 필요한 역할은 커넥터 유형별 이며 관련 커넥터 페이지에 나열 됩니다. 또한 모든 데이터 원본을 연결 하려면 Azure 센티널 작업 영역에 대 한 쓰기 권한이 있어야 합니다.



## <a name="roles-and-allowed-actions"></a>역할 및 허용되는 작업

다음 표에서는 Azure 센티널의 역할 및 허용 되는 작업을 보여 줍니다. X는 작업이 해당 역할에 대해 허용된다는 것을 나타냅니다.

| 역할 | 플레이 북 만들기 및 실행| 대시보드, 분석 규칙 및 기타 Azure 센티널 리소스 만들기 및 편집 | 인시던트 관리 (해제, 할당 등) | 데이터, 인시던트, 대시보드 및 기타 Azure 센티널 리소스 보기 |
|--- |---|---|---|---|
| Azure 센티널 판독기 | -- | -- | -- | X |
| Azure 센티널 응답자|--|--| X | X |
| Azure 센티널 기여자 | -- | X | X | X |
| Azure 센티널 기여자 + 논리 앱 참가자 | X | X | X | X |


> [!NOTE]
> - 사용자가 자신의 작업을 완료하는 데 필요한 최소한의 역할을 할당하는 것이 좋습니다. 예를 들어 규칙 또는 대시보드를 만들어야 하는 사용자 에게만 Azure 센티널 참여자 역할을 할당 합니다.
> - 사용자가 동일한 리소스 그룹의 모든 Azure 센티널 작업 영역에 액세스할 수 있도록 리소스 그룹 범위에서 Azure 센티널에 대 한 사용 권한을 설정 하는 것이 좋습니다.
>
## <a name="building-custom-rbac-roles"></a>사용자 지정 RBAC 역할 빌드

기본 제공 RBAC 역할을 사용 하는 것 외에도, 또는 대신 Azure 센티널에 대 한 사용자 지정 RBAC 역할을 만들 수 있습니다. Azure 센티널에 대 한 [특정 사용 권한](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) 및 [azure Log Analytics 리소스](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)에 따라 다른 [사용자 지정 azure rbac](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) 역할을 만드는 것과 동일한 방식으로 azure 센티널에 대 한 사용자 지정 RBAC 역할이 만들어집니다.

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Azure 센티널에 저장 하는 데이터에 대 한 고급 RBAC
  
Azure 센티널 작업 영역의 데이터에서 Log Analytics 고급 역할 기반 액세스 제어를 사용할 수 있습니다. 여기에는 데이터 형식 및 리소스 중심의 역할 기반 액세스 제어 별로 역할 기반 액세스 제어가 모두 포함 됩니다. Log Analytics 역할에 대 한 자세한 내용은 [Azure Monitor에서 로그 데이터 및 작업 영역 관리](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널 사용자를 위해 역할을 수행 하는 방법 및 사용자가 수행할 수 있는 각 역할에 대해 알아보았습니다.

* [Azure 센티널 블로그](https://aka.ms/azuresentinelblog). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
