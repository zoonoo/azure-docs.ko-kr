---
title: Azure Stack에서 추적 사용에 대 한 테 넌 트 등록 | Microsoft Docs
description: 테 넌 트 등록 및 Azure Stack에서 테 넌 트 사용을 추적 하는 방식을 관리 하는 데 사용 되는 작업에 대 한 세부 정보입니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: aca051dd20ceaeb608baa144a81e0584043a1c52
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002057"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Azure Stack에서 테 넌 트 등록 관리

*적용 대상: Azure Stack 통합 시스템*

이 문서는 등록 작업에 대 한 정보를 포함합니다. 이러한 작업을 사용할 수 있습니다.
- 테 넌 트 등록 관리
- 테 넌 트 사용 현황 추적 관리

목록에 추가 하거나 테 넌 트 매핑을 제거 하는 방법에 대 한 정보를 찾을 수 있습니다. 추적 사용을 관리 하려면 PowerShell 또는 청구 API 끝점을 사용할 수 있습니다. 목록에 추가 하거나 테 넌 트 매핑을 제거 하는 방법에 대 한 정보를 찾을 수 있습니다. 추적 사용을 관리 하려면 PowerShell 또는 청구 API 끝점을 사용할 수 있습니다.

## <a name="add-tenant-to-registration"></a>테 넌 트 등록에 추가

새 테 넌 트 등록에 추가 하려는 경우 작업을 사용 합니다. Azure Active Directory (Azure AD) 테 넌 트를 사용 하 여 연결 된 Azure 구독에서 테 넌 트 사용량이 보고 됩니다.

테 넌 트와 연결 된 구독을 변경 하려는 경우에 작업을 사용할 수 있습니다. PUT/New-azurermresource 이전 매핑 덮어쓸 것인지를 호출 합니다.

테 넌 트를 사용 하 여 단일 Azure 구독을 연결할 수 있습니다. 두 번째 구독을 기존 테 넌 트를 추가 하려고 하면 첫 번째 구독이 과도 하 게 작성 된입니다.

### <a name="use-api-profiles"></a>API 사용 하 여 프로필

등록 cmdlet은 PowerShell을 실행 하는 경우 API 프로필을 지정 해야 합니다. API 프로필에는 Azure 리소스 공급자 및 해당 API 버전 집합을 나타냅니다. 여러 Azure 클라우드를 사용 하 여 상호 작용 하는 경우 API의 올바른 버전을 사용할 수 있도록 합니다. 예를 들어 작업할 여러 클라우드를 사용 하 여 전역 Azure 및 Azure Stack을 사용 하 여 작업 하는 경우. 릴리스 날짜와 일치 하는 이름을 지정 하는 프로필입니다. 사용 해야 합니다 **2017-09-03** 프로필입니다.

Azure Stack 및 API 프로필에 대 한 자세한 내용은 참조 하세요. [Azure Stack에서 관리 하는 API 버전 프로필](user/azure-stack-version-profiles.md)합니다. 를 시작 및 PowerShell 사용 하 여 API 프로필을 사용 하 여 실행에 대 한 참조 [Azure Stack에서 PowerShell 사용 하 여 API 버전 프로필](user/azure-stack-version-profiles-powershell.md)합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수                  | 설명 |
|---                         | --- |
| registrationSubscriptionID | 초기 등록에 사용 된 Azure 구독입니다. |
| customerSubscriptionID     | Azure 구독 (Azure Stack 없습니다) 등록을 위해 고객에 속하는입니다. 만들어야 파트너 센터를 통해 클라우드 서비스 공급자 (CSP) 제안에서 합니다. 고객이는 Azure Stack에 로그인 할 테 넌 트에 대 한 구독을 만든 둘 이상의 테 넌 트 있는지 확인 합니다. |
| resourceGroup              | 등록 저장 되는 Azure에서 리소스 그룹입니다. |
| registrationName           | Azure Stack에 등록의 이름입니다. 이 Azure에 저장 된 개체입니다. 이름은 일반적으로 폼 azurestack의 경우-CloudID, 여기서 CloudID는 Azure Stack 배포의 클라우드 ID입니다. |

> [!Note]  
> 테 넌 트를 사용 하는 각 Azure Stack을 사용 하 여 등록 해야 합니다. 테 넌 트에서는 둘 이상의 Azure Stack 테 넌 트 구독을 사용 하 여 각 배포의 초기 등록을 업데이트 해야 합니다.

### <a name="powershell"></a>PowerShell

테 넌 트에 추가할 새 AzureRmResource cmdlet을 사용 합니다. [Azure Stack에 연결](/azure-stack-powershell-configure-admin.md), 다음 프롬프트에서 다음 cmdlet을 사용 하 여:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API 호출

**Operation**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**응답**: 201 생성됨  
**응답 본문**: Empty  

## <a name="list-all-registered-tenants"></a>등록 된 모든 테 넌 트를 나열 합니다.

등록에 추가 된 모든 테 넌 트의 목록을 가져옵니다.

 > [!Note]  
 > 테 넌 트가 등록 된 경우에 응답을 받을 수 없습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수                  | 설명          |
|---                         | ---                  |
| registrationSubscriptionId | 초기 등록에 사용 된 Azure 구독입니다.   |
| resourceGroup              | 등록 저장 되는 Azure에서 리소스 그룹입니다.    |
| registrationName           | Azure Stack에 등록의 이름입니다. 이 Azure에 저장 된 개체입니다. 이름은 일반적으로의 형태로 **azurestack**-***CloudID***여기서 ***CloudID*** Azure Stack 배포의 클라우드 ID입니다.   |

### <a name="powershell"></a>PowerShell

등록 된 모든 테 넌 트를 나열 하려면 Get AzureRmResource cmdlet을 사용 합니다. [Azure Stack에 연결](/azure-stack-powershell-configure-admin.md), 다음 프롬프트에서 다음 cmdlet을 사용 하 여:

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 호출

가져오기 작업을 사용 하 여 모든 테 넌 트 매핑의 목록을 가져올 수 있습니다.

**Operation**: GET  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**응답**: 200  
**응답 본문**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}",
            "name": " cspSubscriptionId 1",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}",
            "name": " cspSubscriptionId2 ",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>매핑 테 넌 트를 제거 합니다.

등록에 추가 된 테 넌 트를 제거할 수 있습니다. 해당 테 넌 트 리소스를 사용 하 여 Azure Stack에서 여전히는, 용도 초기 Azure Stack 등록에 사용 된 구독에 청구 됩니다.

### <a name="parameters"></a>매개 변수

| 매개 변수                  | 설명          |
|---                         | ---                  |
| registrationSubscriptionId | 등록에 대 한 구독 ID입니다.   |
| resourceGroup              | 등록에 대 한 리소스 그룹입니다.   |
| registrationName           | 등록의 이름입니다.  |
| customerSubscriptionId     | 고객 구독 id입니다.  |

### <a name="powershell"></a>PowerShell

테 넌 트를 제거 하려면 제거 AzureRmResource cmdlet을 사용 합니다. [Azure Stack에 연결](/azure-stack-powershell-configure-admin.md), 다음 프롬프트에서 다음 cmdlet을 사용 하 여:

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 호출

삭제 작업을 사용 하 여 테 넌 트 매핑을 제거할 수 있습니다.

**Operation**: 삭제  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**응답**: 204 콘텐츠 없음  
**응답 본문**: Empty

## <a name="next-steps"></a>다음 단계

 - Azure Stack에서 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 내용은 참조 하세요 [사용 및 Azure Stack에서 청구](azure-stack-billing-and-chargeback.md)합니다.
