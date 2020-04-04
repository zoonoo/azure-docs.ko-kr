---
title: Azure AD 도메인 서비스의 일시 중단된 도메인 | 마이크로 소프트 문서
description: Azure AD DS 관리 도메인의 다양한 상태 및 일시 중단된 도메인을 복원하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 42b26911c12b1e7c62444a6fb2ee68720b02a56b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654602"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Azure Active Directory 도메인 서비스에서 상태 상태를 이해하고 일시 중단된 도메인을 해결합니다.

Azure AD DS(Azure Active Directory Domain Services)가 관리되는 도메인을 오랫동안 서비스할 수 없는 경우 관리되는 도메인이 일시 중단됨 상태로 전환됩니다. 관리되는 도메인이 일시 중단된 상태로 유지되면 자동으로 삭제됩니다. Azure AD DS 관리 도메인을 정상 상태로 유지하고 일시 중단을 방지하려면 가능한 한 빨리 경고를 해결하십시오.

이 문서에서는 관리되는 도메인이 일시 중단되는 이유와 일시 중단된 도메인을 복구하는 방법에 대해 설명합니다.

## <a name="overview-of-managed-domain-states"></a>관리되는 도메인 상태 개요

Azure AD DS 관리 도메인의 수명 주기를 통해 상태를 나타내는 여러 상태가 있습니다. 관리되는 도메인에서 문제를 보고하는 경우 근본 원인을 신속하게 해결하여 상태가 계속 저하되지 않도록 합니다.

![Azure AD DS 관리 도메인이 일시 중단으로 이동하는 상태의 진행](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Azure AD DS 관리 도메인은 다음 상태 중 하나에 있을 수 있습니다.

* [실행 중](#running-state)
* [주의가 필요](#needs-attention-state)
* [일시 중단](#suspended-state)
* [삭제](#deleted-state)

## <a name="running-state"></a>실행 상태

올바르게 구성되고 문제 없이 실행되는 Azure AD DS 관리 도메인이 *실행 중* 상태입니다. 관리되는 도메인에 대해 원하는 상태입니다.

### <a name="what-to-expect"></a>예상 프로그램

* Azure 플랫폼은 관리되는 도메인의 상태를 정기적으로 모니터링할 수 있습니다.
* 관리되는 도메인의 도메인 컨트롤러는 정기적으로 패치되고 업데이트됩니다.
* Azure Active Directory의 변경 내용은 관리되는 도메인에 정기적으로 동기화됩니다.
* 관리되는 도메인에 대해 정기적인 백업이 수행됩니다.

## <a name="needs-attention-state"></a>주의 상태 필요

해결해야 할 하나 이상의 문제가 있는 Azure AD DS 관리 도메인은 *주의 필요* 상태에 있습니다. 관리되는 도메인의 상태 페이지에는 경고가 나열되고 문제가 있는 위치를 나타냅니다. 일부 경고는 일시적이며 Azure 플랫폼에서 자동으로 해결됩니다. 다른 경고의 경우 제공된 해결 단계에 따라 문제를 해결할 수 있습니다. 중요한 경고가 있고 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

경고의 한 예는 제한적인 네트워크 보안 그룹이 있는 경우입니다. 이 구성에서 Azure 플랫폼은 관리되는 도메인을 업데이트하고 모니터링하지 못할 수 있습니다. 경고가 생성되고 상태가 주의 *필요로*변경됩니다.

자세한 내용은 [Azure AD DS 관리 도메인에 대한 경고 문제를 해결하는 방법을][resolve-alerts]참조하세요.

### <a name="what-to-expect"></a>예상 프로그램

Azure AD DS 관리 도메인이 *주의 필요* 상태에 있는 경우 Azure 플랫폼은 정기적으로 데이터를 모니터링, 패치, 업데이트 또는 백업하지 못할 수 있습니다. 잘못된 네트워크 구성과 같이 관리되는 도메인의 도메인 컨트롤러에 연결할 수 없는 경우도 있습니다.

* 관리되는 도메인이 비정상 상태이며 경고가 해결될 때까지 진행 중인 상태 모니터링이 중지될 수 있습니다.
* 관리되는 도메인의 도메인 컨트롤러는 패치하거나 업데이트할 수 없습니다.
* Azure Active Directory의 변경 내용은 관리되는 도메인에 동기화되지 않을 수 있습니다.
* 관리되는 도메인에 대한 백업은 수행되지 않을 수 있습니다.
* 관리되는 도메인에 영향을 주는 중요하지 않은 경고를 해결하는 경우 상태는 *실행 중* 상태로 돌아와야 합니다.
* Azure 플랫폼이 도메인 컨트롤러에 연결할 수 없는 구성 문제에 대해 중요한 경고가 트리거됩니다. 이러한 중요 경고가 15일 이내에 해결되지 않으면 관리되는 도메인이 *일시 중단된* 상태로 들어갑니다.

## <a name="suspended-state"></a>일시 중단된 상태

Azure AD DS 관리 도메인은 다음 이유 중 하나로 **일시 중단** 된 상태로 들어갑니다.

* 15일 이내에 해결되지 않은 중요한 경고가 하나 이상 있습니다.
    * 중요한 경고는 Azure AD DS에 필요한 리소스에 대한 액세스를 차단하는 잘못된 구성으로 인해 발생할 수 있습니다. 관리되는 도메인에서 [AADDS104: 네트워크 오류][alert-nsg] 경고가 16일 이상 해결되지 않은 경우를 예로 들 수 있습니다.
* Azure 구독에 대한 청구 문제가 있거나 Azure 구독이 만료되었습니다.

Azure 플랫폼이 도메인을 관리, 모니터링, 패치 또는 백업할 수 없을 때 관리되는 도메인이 일시 중단됩니다. 관리되는 도메인은 15일 동안 *일시 중단된* 상태로 유지됩니다. 관리되는 도메인에 대한 액세스를 유지하려면 중요한 경고를 즉시 해결합니다.

### <a name="what-to-expect"></a>예상 프로그램

Azure AD DS 관리 도메인이 *일시 중단된* 상태에 있을 때 다음 동작이 발생합니다.

* 관리되는 도메인의 도메인 컨트롤러는 프로비저닝이 해제되고 가상 네트워크 내에서 연결할 수 없습니다.
* 활성화된 경우 인터넷을 통해 관리되는 도메인에 대한 안전한 LDAP 액세스가 작동을 중지합니다.
* 관리되는 도메인에 인증하거나, 도메인에 가입한 VM에 로그온하거나, LDAP/LDAPS를 통해 연결하는 데 오류가 있습니다.
* 관리되는 도메인에 대한 백업은 더 이상 수행되지 않습니다.
* Azure AD와 동기화가 중지됩니다.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>관리되는 도메인이 일시 중단되었는지 확인하려면 어떻게 하나요?

Azure 포털의 Azure AD DS 상태 페이지에 도메인이 일시 중단됨을 나타내는 [경고가][resolve-alerts] 표시됩니다. 도메인 의 상태에일시 *중단된*상태도 표시됩니다.

### <a name="restore-a-suspended-domain"></a>일시 중단된 도메인 복원

*일시 중단* 된 상태에 있는 Azure AD DS 관리 도메인의 상태를 복원 하려면 다음 단계를 완료 합니다.

1. Azure 포털에서 **도메인 서비스를**검색하고 선택합니다.
1. *aaddscontoso.com*와 같은 목록에서 Azure AD DS 관리 도메인을 선택한 다음 **상태를 선택합니다.**
1. 일시 중단 의 원인에 따라 *AADDS503* 또는 *AADDS504와*같은 경고를 선택합니다.
1. 경고에 제공된 확인 링크를 선택하고 단계에 따라 해결합니다.

관리되는 도메인은 마지막 백업 날짜로만 복원할 수 있습니다. 마지막 백업 날짜가 관리되는 도메인의 **상태** 페이지에 표시됩니다. 마지막 백업 이후에 발생한 변경 내용은 복원되지 않습니다. 관리되는 도메인의 백업은 최대 30일 동안 저장됩니다. 30일이 지난 백업은 삭제됩니다.

관리되는 도메인이 *일시 중단된* 상태에 있을 때 경고를 해결한 후 [Azure 지원 요청을 열어][azure-support] 정상 상태로 돌아갑니다. 백업이 30일 미만인 경우 Azure 지원은 관리되는 도메인을 복원할 수 있습니다.

## <a name="deleted-state"></a>삭제된 상태

Azure AD DS 관리 도메인이 일시 *중단된* 상태로 15일 동안 유지되면 삭제됩니다. 이 프로세스는 복구할 수 없습니다.

### <a name="what-to-expect"></a>예상 프로그램

Azure AD DS 관리 도메인이 *삭제됨* 상태로 들어가면 다음 동작이 표시됩니다.

* 관리되는 도메인에 대한 모든 리소스 및 백업이 삭제됩니다.
* 관리되는 도메인을 복원할 수 없으며 Azure AD DS를 재사용하기 위해 대체 관리되는 도메인을 만들어야 합니다.
* 관리되는 도메인이 삭제된 후에는 요금이 청구되지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS 관리 도메인을 정상 상태로 유지하고 일시 중단될 위험을 최소화하려면 [관리되는 도메인에 대한 경고를 해결하는][resolve-alerts]방법을 알아봅니다.

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
