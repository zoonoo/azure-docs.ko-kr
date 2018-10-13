---
title: 사용 및 클라우드 서비스 공급자로 Azure Stack에 대 한 청구 관리 | Microsoft Docs
description: Azure Stack으로 공급자 (CSP (클라우드)을 등록 하 고 고객 청구에 대 한 추가 연습
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: b4df9d3b107945b2c0797875718f3266b7fd0b10
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166546"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>사용량 및 클라우드 서비스 공급자로 Azure Stack에 대 한 청구를 관리 합니다. 

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 Azure Stack으로 공급자 (CSP (클라우드)을 등록 하 고 고객 추가 보여 줍니다.

CSP로 Azure Stack에 사용 하 여 다양 한 고객과 작동 합니다. 각 고객의 Azure CSP 구독을 있습니다. 각 사용자 구독에 Azure Stack에서 사용량을 직접 해야 합니다.

다음 다이어그램에는 공유 서비스 계정을 선택 하 여 Azure Stack 계정을 사용 하 여 Azure 계정을 등록 해야 하는 단계를 보여 줍니다. 등록을 등록할 수 있습니다 최종 고객.

**사용 현황 추적 CSP로 추가 하는 단계**

![사용 및 클라우드 서비스 공급자 관리를 사용 하도록 설정 하는 것에 대 한 프로세스입니다.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-apss-subscription"></a>CSP 또는 APSS 구독 만들기

### <a name="cloud-service-provider-subscription-types"></a>클라우드 서비스 공급자 구독 유형

Azure Stack에 사용 하는 공유 서비스 계정 유형을 선택 해야 합니다. Azure Stack 다중 테 넌 트의 등록에 사용할 수 있는 구독 유형은 다음과 같습니다.

 - 클라우드 서비스 공급자 
 - 파트너 공유 서비스 구독 

#### <a name="azure-partner-shared-services"></a>Azure 파트너 공유 서비스

Azure 파트너 공유 서비스 (APSS) 구독은 때 직접 CSP 등록에 대 한 기본으로 선택 하거나 Azure Stack을 작동 하는 CSP 배포자입니다.

APSS 구독 공유 서비스 테 넌 트와 연결 됩니다. Azure Stack을 등록 하면 구독의 소유자가 계정에 대 한 자격 증명을 제공 해야 합니다. Azure Stack 등록을 사용 하는 계정이 배포에 사용 하는 관리자 계정과 다를 수 있습니다. 또한 두 개의 계정을 수행 *되지* 동일한 도메인에 속해야 합니다. 즉, 이미 사용 하는 테 넌 트를 사용 하 여 배포할 수 있습니다. 예를 들어 ContosoCSP.onmicrosoft.com를 사용 하 여 다음을 다른 테 넌 트, 예를 들어 IURContosoCSP.onmicrosoft.com를 사용 하 여 등록할 수 있습니다. 그러려면 일 Azure Stack 관리를 수행할 때 ContosoCSP.onmicrosoft.com를 사용 하 여 로그인 해야 해야 합니다. 로그인 하면 등록 작업을 수행 해야 할 때 IURContosoCSP.onmicrosoft.com를 사용 하 여 Azure에 있습니다.

구독을 만드는 방법에 대 한 설명은 APSS 구독 및 지침 다음을 참조 하십시오 [Azure 파트너 공유 서비스 추가](https://msdn.microsoft.com/partner-center/shared-services)합니다.

#### <a name="csp-subscriptions"></a>CSP 구독

클라우드 서비스 공급자 (CSP) 구독에 등록 하면 CSP 재판매인에 대 한 기본으로 선택 됩니다 또는 최종 고객은 Azure Stack을 작동 합니다.

## <a name="register-azure-stack"></a>Azure Stack 등록

Azure를 사용 하 여 Azure Stack 등록 APSS 만든 구독에는 이전 섹션의 정보를 사용 합니다. 자세한 내용은 [Azure 구독을 사용 하 여 Azure Stack 등록](azure-stack-registration.md)합니다.

## <a name="add-end-customer"></a>최종 사용자를 추가 합니다.

Azure Stack 리소스를 사용 하는 새 테 넌 트 사용량 보고 됩니다. 해당 클라우드 서비스 공급자 (CSP) 구독에 있도록 구성 하려면 [사용에 대 한 테 넌 트를 추가 하 고 Azure Stack에 청구](azure-stack-csp-howto-register-tenants.md)합니다.

## <a name="charge-the-right-subscriptions"></a>올바른 구독 요금이 청구 됩니다.

Azure Stack 등록을 호출 하는 기능을 사용 합니다. 등록은 Azure에 저장 된 개체입니다. 등록 개체를 지정된 하는 Azure Stack에 대 한 요금을 청구 하는 데는 Azure 구독을 설명 합니다. 이 섹션에서는 등록의 중요성을 해결합니다.

등록을 사용 하 여 Azure Stack 수행할 수 있습니다.
 - Azure 상거래에 Azure Stack 사용 현황 데이터를 전달 하 고 Azure 구독을 청구 합니다.
 - 다중 테 넌 트 Azure Stack 배포와 다른 구독에서 각 고객의 사용량을 보고 합니다. 다중 테 넌 트에 동일한 Azure Stack 인스턴스에서 다른 조직을 지원 하도록 Azure Stack 수 있습니다.

각 Azure Stack에 대 한 기본 구독을 하나 이며 구독 테 넌 트 여러 합니다. 기본 구독이 없으면 다음 테 넌 트 별 구독 청구 되는 Azure 구독이입니다. 첫 번째 구독을 등록 해야 합니다. 보고 기능이 작동 하려면 다중 테 넌 트 사용량에 대 한 구독 CSP 또는 APSS 구독 이어야 합니다.

그런 다음 Azure Stack을 사용 하려는 각 테 넌 트에 대 한 Azure 구독을 사용 하 여 등록이 업데이트 됩니다. 테 넌 트 구독 CSP 형식 이어야 하며 기본 구독을 소유 하는 파트너에 롤업 합니다. 즉, 다른 사용자의 고객을 등록할 수 없습니다.

전역 azure 사용량 정보를 전달 하는 Azure Stack을 하는 경우 Azure의 서비스 등록을 확인 하 고 해당 테 넌 트 구독에 각 테 넌 트의 사용을 매핑합니다. 테 넌 트에 등록 되지 않은 경우 해당 사용량 원래 위치에서 Azure Stack 인스턴스에 대 한 기본 구독으로 이동 합니다.

테 넌 트 구독은 CSP 구독을 CSP 파트너에 게 청구 보내지고 사용량 정보가 최종 고객에 게 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

 - CSP 프로그램에 대 한 자세한 내용은 참조 하세요 [클라우드 솔루션 공급자 프로그램](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)합니다.
 - Azure Stack에서 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 내용은 참조 하세요 [사용 및 Azure Stack에서 청구](azure-stack-billing-and-chargeback.md)합니다.
