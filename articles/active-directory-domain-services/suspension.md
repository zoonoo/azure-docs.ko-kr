---
title: 'Azure Active Directory Domain Services: 일시 중단된 도메인 | Microsoft Docs'
description: 관리되는 도메인 일시 중단 및 삭제
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: a4e43518ce70c040b8243034c52e4b215548a47c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246721"
---
# <a name="suspended-domains"></a>일시 중단된 도메인
Azure AD DS(Azure Active Directory Domain Services)가 관리되는 도메인을 오랫동안 서비스할 수 없는 경우 관리되는 도메인이 일시 중단됨 상태로 전환됩니다. 이 문서에서는 관리되는 도메인이 일시 중단된 이유와 일시 중단된 도메인을 수정하는 방법을 설명합니다.


## <a name="states-your-managed-domain-can-be-in"></a>관리되는 도메인의 가능한 상태

![일시 중단된 도메인 타임라인](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

앞의 그림은 Azure AD DS 관리되는 도메인의 가능한 상태를 간략하게 설명합니다.

### <a name="running-state"></a>"실행" 상태
올바르게 구성되고 정기적으로 작동하는 관리되는 도메인은 **실행** 상태입니다.

**고려해야 할 사항**
* Microsoft에서 관리되는 도메인의 상태를 정기적으로 모니터링할 수 있습니다.
* 관리되는 도메인의 도메인 컨트롤러가 정기적으로 패치 및 업데이트됩니다.
* Azure Active Directory의 변경 내용이 관리되는 도메인에 정기적으로 동기화됩니다.
* 관리되는 도메인에 대한 정기 백업이 수행됩니다.


### <a name="needs-attention-state"></a>"주의 필요" 상태
관리자가 작업을 수행해야 하는 문제가 하나 이상 있는 경우, 관리되는 도메인은 **주의 필요** 상태입니다. 이 상태에 있는 관리되는 도메인의 상태 페이지에는 하나 이상의 경고가 나열됩니다.

예를 들어, 가상 네트워크에 대해 제한적인 NSG를 구성한 경우, Microsoft에서 관리되는 도메인을 업데이트 및 모니터링하지 못할 수 있습니다. 이 잘못된 구성은 관리되는 도메인을 "주의 필요" 상태로 전환하는 경고를 트리거합니다.

각 경고에 일련의 해결 단계가 있습니다. 일부 경고는 일시적이며, 서비스에서 자동으로 해결됩니다. 이 경고에 해당하는 해결 단계의 지침에 따라 다른 일부 경고를 해결할 수 있습니다. 몇 가지 중요한 경고는 Microsoft 지원에 문의하여 해결해야 합니다.

자세한 내용은 [관리되는 도메인에서 경고의 문제를 해결하는 방법](troubleshoot-alerts.md)을 참조하세요.

**고려해야 할 사항**

관리되는 도메인의 도메인 컨트롤러에 연결할 수 없는 경우가 있습니다(예: 잘못된 네트워크 구성이 있는 경우). 관리되는 도메인이 "주의 필요" 상태이면 Microsoft는 이 도메인이 정기적으로 모니터링, 패치, 업데이트 또는 백업된다고 보장할 수 없습니다.

* 관리되는 도메인이 비정상 상태이며, 경고가 해결될 때까지 지속적인 상태 모니터링이 중지될 수 있습니다.
* 관리되는 도메인의 도메인 컨트롤러가 패치 또는 업데이트되지 않을 수 있습니다.
* Azure Active Directory의 변경 내용이 관리되는 도메인에 동기화되지 않을 수 있습니다.
* 가능한 경우, 관리되는 도메인의 백업이 수행될 수 있습니다.
* 관리되는 도메인에 영향을 주는 경고를 해결하면 도메인을 "실행" 상태로 복원할 수 있습니다.
* Microsoft에서 도메인 컨트롤러에 연결할 수 없는 구성 문제가 있을 경우, 중요한 경고가 트리거됩니다. 이러한 경고가 15일 이내에 해결되지 않으면 관리되는 도메인이 "일시 중단됨" 상태로 전환됩니다.


### <a name="the-suspended-state"></a>"일시 중단됨" 상태
관리되는 도메인이 **일시 중단됨** 상태로 전환되는 이유는 다음과 같습니다.

* 15일 이내에 해결되지 않은 중요한 경고가 하나 이상 있습니다. 중요한 경고는 Azure AD DS에 필요한 리소스에 대한 액세스를 차단하는 잘못된 구성으로 인해 발생할 수 있습니다.
    * 관리되는 도메인에서 [AADDS104: 네트워크 오류](alert-nsg.md) 경고가 15일을 초과하여 해결되지 않은 경우를 예로 들 수 있습니다.
* Azure 구독에 청구 문제가 있거나 Azure 구독이 만료되었습니다.

Microsoft에서 지속적으로 도메인을 관리, 모니터링, 패치 또는 백업할 수 없는 경우, 관리되는 도메인이 일시 중단됩니다.

**고려해야 할 사항**
* 관리되는 도메인의 도메인 컨트롤러가 프로비전 해제되고, 가상 네트워크 내에서 연결할 수 없습니다.
* 인터넷을 통한 관리되는 도메인에 대한 보안 LDAP 액세스가 작동 중지됩니다(사용하도록 설정된 경우).
* 관리되는 도메인에 인증, 도메인에 가입된 가상 머신에 로그온, LDAP/LDAPS를 통한 연결과 관련된 오류가 표시됩니다.
* 관리되는 도메인의 백업이 더 이상 수행되지 않습니다.
* Azure AD와 동기화가 중지됩니다.

경고를 해결하면 관리되는 도메인이 "일시 중단됨" 상태로 전환됩니다. 그러면 지원에 문의하세요.
고객 지원팀은 30일 이내에 생성된 백업이 있는 경우에만 관리되는 도메인을 복원할 수 있습니다.

관리되는 도메인은 15일 동안만 일시 중단됨 상태로 유지됩니다. 관리되는 도메인을 복구하려면 중요한 경고를 즉시 해결하는 것이 좋습니다.


### <a name="deleted-state"></a>"삭제됨" 상태
15일 동안 "일시 중단됨" 상태로 유지된 관리되는 도메인은 **삭제**됩니다.

**고려해야 할 사항**
* 관리되는 도메인에 대한 모든 리소스 및 백업이 삭제됩니다.
* 관리되는 도메인을 복원할 수 없으며, Azure AD DS를 사용하려면 관리되는 도메인을 새로 만들어야 합니다.
* 관리되는 도메인이 삭제된 후에는 요금이 청구되지 않습니다.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>관리되는 도메인이 일시 중단되었는지 확인하려면 어떻게 하나요?
Azure Portal의 Azure AD DS 상태 페이지에 도메인이 일시 중단되었다고 선언하는 [경고](troubleshoot-alerts.md)가 표시됩니다. 도메인 상태도 “일시 중단됨”으로 표시됩니다.


## <a name="restore-a-suspended-domain"></a>일시 중단된 도메인 복원
"일시 중단됨" 상태의 도메인을 복원하려면 다음 단계를 완료하세요.

1. Azure Portal에서 [Azure Active Directory Domain Services 페이지](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)로 이동합니다.
2. 관리되는 도메인을 선택합니다.
3. 왼쪽 패널에서 **상태**를 선택합니다.
4. 경고를 선택합니다. 경고 ID는 일시 중단의 원인에 따라 AADDS503 또는 AADDS504입니다.
5. 경고에 제공된 확인 링크를 선택합니다. 단계에 따라 경고를 해결합니다.

관리되는 도메인을 마지막 백업 날짜로만 복원할 수 있습니다. 마지막 백업 날짜는 관리되는 도메인의 상태 페이지에 표시됩니다. 마지막 백업 이후에 발생한 변경 내용은 복원되지 않습니다. 관리되는 도메인의 백업은 최대 30일 동안 저장됩니다. 30일이 지난 백업은 삭제됩니다.


## <a name="next-steps"></a>다음 단계
- [관리되는 도메인에 대한 경고 해결](troubleshoot-alerts.md)
- [Azure Active Directory Domain Services에 대해 자세히 알아보기](overview.md)
- [제품팀에 문의](contact-us.md)

## <a name="contact-us"></a>문의처
[지원이 필요하거나 피드백을 공유하려면](contact-us.md)Azure Active Directory Domain Services 제품 팀에 문의하세요.
