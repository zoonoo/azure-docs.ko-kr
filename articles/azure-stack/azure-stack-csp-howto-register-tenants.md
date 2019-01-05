---
title: 추가 사용량에 대 한 테 넌 트 및 Azure Stack에 대금 청구 | Microsoft Docs
description: 클라우드 서비스 공급자 (CSP)에서 관리 되는 Azure Stack에 최종 사용자를 추가 하는 데 필요한 단계입니다.
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
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: be240ea17a8679d25623f971f7be5ea5c3ad2e8f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053245"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>사용량에 대 한 테 넌 트를 추가 하 고 Azure Stack에 대 한 청구

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 클라우드 서비스 공급자 (CSP)에서 관리 되는 Azure Stack 배포에는 최종 사용자를 추가 하는 데 필요한 단계를 설명 합니다. 새 테 넌 트 리소스를 사용 하는 경우 Azure Stack는 CSP 구독에 사용을 보고 합니다.

Csp는 종종 여러 최종 고객 (테 넌 트)에 게 Azure Stack 배포 서비스를 제공합니다. Azure Stack 등록을 위해 테 넌 트를 추가 하면 각 테 넌 트의 사용은 보고 해당 CSP 구독에 청구 됩니다. 이 문서의 단계를 완료 하면 Azure Stack의 초기 등록에 사용 된 구독에 테 넌 트 사용량 요금이 청구 됩니다. 사용 현황 추적에 대 한 Azure Stack에는 최종 사용자를 추가할 수 있습니다 하 고 테 넌 트를 관리 하려면 CSP로 Azure Stack 구성 해야 합니다. 단계 및 리소스를 참조 하세요 [사용 및 클라우드 서비스 공급자로 Azure Stack에 대 한 청구 관리](azure-stack-add-manage-billing-as-a-csp.md)합니다.

다음 그림에는 CSP를 통해 새 고객은 Azure Stack을 사용 하 고 사용 현황의 고객에 대 한 추적을 설정 하기 위해 수행 해야 하는 단계를 보여 줍니다. 최종 사용자를 추가 하면 수도 있습니다 Azure Stack에서 리소스를 관리할 수 있습니다. 해당 리소스를 관리 하기 위한 두 가지 옵션이 있습니다.

1. 최종 고객을 유지 하 고 최종 고객에 게 로컬 Azure Stack 구독에 대 한 자격 증명을 제공할 수 있습니다.  
2. 최종 고객 로컬로 해당 구독을 사용 하 고 CSP 게스트로 소유자 권한을 추가 합니다.  

## <a name="steps-to-add-an-end-customer"></a>최종 고객을 추가 하는 단계

![사용 현황 추적에 대 한 최종 고객 계정을 관리 하려면 클라우드 서비스 공급자 설정](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>파트너 센터에서 새 고객 만들기

파트너 센터 고객에 대 한 새 Azure 구독을 만듭니다. 자세한 내용은 [새 고객이 추가](https://msdn.microsoft.com/partner-center/add-a-new-customer)합니다.

### <a name="create-an-azure-subscription-for-the-end-customer"></a>최종 사용자에 대 한 Azure 구독 만들기

파트너 센터에서 고객의 레코드를 만든 후 판매할 수 있습니다 카탈로그의 제품을 구독 합니다. 자세한 내용은 [만들기, 일시 중단 또는 고객 구독을 취소](https://msdn.microsoft.com/partner-center/create-a-new-subscription)합니다.

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>최종 고객 디렉터리에 게스트 사용자를 만듭니다.

최종 사용자가 자신의 계정을 관리 하는 경우 해당 디렉터리에 게스트 사용자를 만듭니다 및 정보를 보낼 합니다. 최종 사용자는 다음 게스트를 추가 하 고에 게스트 권한 상승 **소유자** Azure Stack CSP 계정에 대 한 합니다.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>최종 고객 구독을 사용 하 여 등록을 업데이트 합니다.

새 고객 구독을 사용 하 여 등록을 업데이트 합니다. Azure는 고객의 id를 사용 하 여 파트너 센터에서 고객의 사용량을 보고 합니다. 이렇게 하면 각 고객의 사용은 해당 고객의 개별 CSP 구독에서 보고 됩니다. 그러면 사용자 사용 현황 추적 및 청구 더 쉽습니다.

> [!NOTE]  
> 이 단계를 수행 하려면 있어야 [Azure Stack 등록](azure-stack-register.md)합니다.

1. 상승된 된 프롬프트를 사용 하 여 Windows PowerShell을 열고 실행 합니다.  
    `Add-AzureRmAccount`
2. Azure 자격 증명을 입력 합니다.
3. PowerShell 세션에서 다음을 실행 합니다.

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>AzureRmResource 새 PowerShell 매개 변수

다음 섹션에 대 한 매개 변수를 설명 합니다 **New-azurermresource** cmdlet:

| 매개 변수 | 설명 |
| --- | --- |
|registrationSubscriptionID | Azure Stack의 초기 등록에 사용 된 Azure 구독입니다.|
| customerSubscriptionID | Azure 구독 (Azure Stack 없습니다) 등록을 위해 고객에 속하는입니다. 해야 CSP 제품;에서 만든 실제로이 파트너 센터를 통해 의미합니다. 고객이 Azure Active Directory 테 넌 트를 여러 개 있으면이 구독의 Azure Stack에 로그인 하는 데 사용할 테 넌 트에 생성 되어야 합니다. 고객 구독 ID는 소문자를 사용 해야 합니다. |
| resourceGroup | 등록 저장 되는 Azure에서 리소스 그룹입니다. |
| registrationName | Azure Stack에 등록의 이름입니다. 이 Azure에 저장 된 개체입니다. |
| properties | 리소스에 대 한 속성을 지정합니다. 리소스 종류에 관련 된 속성의 값을 지정 하려면이 매개 변수를 사용 합니다.

> [!NOTE]  
> 사용 하는 각 Azure Stack 테 넌 트에 등록 해야 합니다. 두 개의 Azure Stack 배포 해야 하는 경우 테 넌 트 둘 다 사용 하 여 테 넌 트 구독을 사용 하 여 각 배포의 초기 등록을 업데이트 해야 합니다.

### <a name="onboard-tenant-to-azure-stack"></a>Azure Stack 테 넌 트 온 보 딩

Azure Stack에서 서비스를 사용 하도록 여러 Azure AD 테 넌 트에서 사용자를 지 원하는 Azure Stack을 구성 합니다. 자세한 내용은 [Azure Stack에서 다중 테 넌 트를 사용 하도록 설정](azure-stack-enable-multitenancy.md)합니다.

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Azure Stack에서 최종 고객 테 넌 트의 로컬 리소스 만들기

Azure Stack에 새 고객을 추가한 또는 최종 고객 테 넌 트 소유자 권한 사용 하 여 게스트 계정을 사용 하도록 설정한 후에 테 넌 트의 리소스를 만들 수 있는지 확인 합니다. 예를 들어 할 [Azure Stack 포털을 사용 하 여 Windows 가상 머신 만들기](user/azure-stack-quick-windows-portal.md)합니다.

## <a name="next-steps"></a>다음 단계

- 등록 프로세스에서 트리거되는 경우 오류 메시지를 검토, 참조 [등록 오류 메시지를 테 넌 트](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes)합니다.
- Azure Stack에서 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 내용은 참조 하세요 [사용 및 Azure Stack에서 청구](azure-stack-billing-and-chargeback.md)합니다.
- 참조 하는 방법, 최종 고객이 추가할 수 있습니다, 관리자가 Azure Stack 테 넌 트에 대 한 CSP,으로 검토 [Azure Stack 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정](user/azure-stack-csp-enable-billing-usage-tracking.md)합니다.
