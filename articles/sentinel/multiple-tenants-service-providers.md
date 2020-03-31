---
title: MSSP 서비스 공급자를 위해 Azure Sentinel에 여러 테넌티로 작업 | 마이크로 소프트 문서
description: MSSP 서비스 공급자에 대 한 Azure Sentinel에 여러 테넌티와 함께 작업 하는 방법.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476018"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Azure Sentinel에서 여러 테넌티로 작업 

관리되는 보안 서비스 공급자(MSSP)이고 [Azure Lighthouse를](../lighthouse/overview.md) 사용하여 고객의 보안 운영 센터(SOC)를 관리하는 경우 자체 Azure 테넌트에서 고객의 테넌트에 직접 연결하지 않고도 고객의 Azure Sentinel 리소스를 관리할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
- [온보드 Azure 등대](../lighthouse/how-to/onboard-customer.md)
- 이 작업이 제대로 작동하려면 테넌트가 하나 이상의 구독에서 Azure Sentinel 리소스 공급자에 등록되어야 합니다. 테넌트에 등록된 Azure Sentinel이 있는 경우 시작할 준비가 된 것입니다. 그렇지 않은 경우 Azure 포털에서 구독을 선택한 다음 **리소스 공급자를** **선택합니다.**  그런 다음 **SOC - 리소스 공급자** 화면에서 `Microsoft.OperationalInsights` 을 `Microsoft.SecurityInsights`검색하고 선택하고 **등록을**선택합니다.
   ![리소스 공급자 확인](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>다른 테넌에서 Azure Sentinel에 액세스하는 방법
1. **디렉터리 + 구독에서**위임된 디렉터리 및 고객의 Azure Sentinel 작업 영역이 있는 구독을 선택합니다.

   ![보안 인시던트 생성](media/multiple-tenants-service-providers/directory-subscription.png)

1. Azure 센티넬을 엽니다. 선택한 구독의 모든 작업 영역이 표시되며 자체 테넌트의 모든 작업 영역과 마찬가지로 원활하게 작업할 수 있습니다.

> [!NOTE]
> 관리되는 작업 영역 내에서 Azure Sentinel에 커넥터를 배포할 수 없습니다. 커넥터를 배포하려면 커넥터를 배포하려는 테넌트에 직접 로그인하고 필요한 사용 권한으로 인증해야 합니다.





## <a name="next-steps"></a>다음 단계
이 문서에서는 여러 Azure Sentinel 테넌을 원활하게 관리하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

