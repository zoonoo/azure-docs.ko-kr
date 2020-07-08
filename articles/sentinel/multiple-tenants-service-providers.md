---
title: MSSP 서비스 공급자에 대 한 Azure 센티널에 다중 테 넌 트 작업 | Microsoft Docs
description: MSSP 서비스 공급자에 대해 여러 테 넌 트를 Azure 센티널로 작업 하는 방법입니다.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79476018"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Azure 센티널에서 여러 테 넌 트 작업 

사용자가 관리 되는 MSSP (security service provider) 인 경우 [Azure Lighthouse](../lighthouse/overview.md) 를 사용 하 여 고객의 SOC (security operations center)를 관리 하는 경우 사용자의 azure 테 넌 트에서 고객 테 넌 트에 직접 연결 하지 않고도 고객의 azure 센티널 리소스를 관리할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
- [Azure Lighthouse 등록](../lighthouse/how-to/onboard-customer.md)
- 이 작업이 제대로 작동 하려면 하나 이상의 구독에서 테 넌 트가 Azure 센티널 리소스 공급자에 등록 되어 있어야 합니다. 테 넌 트에 등록 된 Azure 센티널가 있는 경우 시작할 준비가 된 것입니다. 그렇지 않으면 Azure Portal에서 **구독** 을 선택한 다음 **리소스 공급자**를 선택 합니다.  그런 다음 **SOC-리소스 공급자** 화면에서 및를 검색 하 고 `Microsoft.OperationalInsights` `Microsoft.SecurityInsights` , **등록**을 선택 합니다.
   ![리소스 공급자 확인](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>다른 테 넌 트에서 Azure 센티널에 액세스 하는 방법
1. **디렉터리 + 구독**에서 위임 된 디렉터리 및 고객의 Azure 센티널 작업 영역이 있는 구독을 선택 합니다.

   ![보안 인시던트 생성](media/multiple-tenants-service-providers/directory-subscription.png)

1. Azure 센티널를 엽니다. 선택한 구독의 모든 작업 영역을 볼 수 있으며, 사용자는 테 넌 트의 모든 작업 영역 처럼 원활 하 게 작업할 수 있습니다.

> [!NOTE]
> 관리 되는 작업 영역 내에서 Azure 센티널의 커넥터를 배포할 수 없습니다. 커넥터를 배포 하려면 커넥터를 배포 하려는 테 넌 트에 직접 로그인 하 고 필요한 사용 권한이 있는 사용자를 인증 해야 합니다.





## <a name="next-steps"></a>다음 단계
이 문서에서는 여러 Azure 센티널 테 넌 트를 원활 하 게 관리 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

