---
title: Azure AD Domain Services에서 일시 중단된 도메인 | Microsoft Docs
description: Azure AD DS 관리되는 도메인의 다양한 성능 상태와 일시 중단된 도메인을 복원하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: b0b48e7ad494386052e6d94c32d7215b3f4f0202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618827"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에서 성능 상태를 파악하고 일시 중단된 도메인을 해결합니다.

Azure AD DS(Azure Active Directory Domain Services)가 관리되는 도메인을 오랫동안 서비스할 수 없는 경우 관리되는 도메인이 일시 중단됨 상태로 전환됩니다. 관리되는 도메인이 일시 중단된 상태로 유지되는 경우 자동으로 삭제됩니다. Azure AD DS 관리되는 도메인을 정상 상태로 유지하고 일시 중단을 방지하려면 가급적 빨리 경고를 해결해야 합니다.

이 문서에서는 관리되는 도메인이 일시 중단되는 이유와 일시 중단된 도메인을 복구하는 방법을 설명합니다.

## <a name="overview-of-managed-domain-states"></a>관리되는 도메인 상태 개요

관리되는 도메인의 수명 주기 동안 관리되는 도메인의 상태를 나타내는 다양한 상태가 있습니다. 관리되는 도메인에서 문제를 보고하는 경우 근본 원인을 신속하게 해결하여 상태가 계속 저하되지 않도록 합니다.

![관리되는 도메인이 일시 중단으로 진행할 때의 상태의 진행](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

관리되는 도메인의 상태는 다음 중 하나가 될 수 있습니다.

* [실행 중](#running-state)
* [주의 필요](#needs-attention-state)
* [일시 중지됨](#suspended-state)
* [삭제됨](#deleted-state)

## <a name="running-state"></a>실행 상태

올바르게 구성되고 문제가 없는 관리되는 도메인은 *실행 중* 상태입니다. 이는 관리되는 도메인의 바람직한 상태입니다.

### <a name="what-to-expect"></a>필요한 항목

* Azure 플랫폼은 관리되는 도메인의 상태를 정기적으로 모니터링할 수 있습니다.
* 관리되는 도메인의 도메인 컨트롤러가 정기적으로 패치 및 업데이트됩니다.
* Azure Active Directory의 변경 내용이 관리되는 도메인에 정기적으로 동기화됩니다.
* 관리되는 도메인에 대한 정기 백업이 수행됩니다.

## <a name="needs-attention-state"></a>주의 필요 상태

해결해야 하는 문제를 하나 이상 포함하는 관리되는 도메인은 *주의 필요* 상태입니다. 관리되는 도메인의 상태 페이지에는 경고가 나열되고 문제가 발생한 위치가 표시됩니다.

일부 경고는 일시적이며 Azure 플랫폼에서 자동으로 해결됩니다. 다른 경고의 경우 제공된 해결 단계를 수행하여 문제를 해결할 수 있습니다. 중요한 알림이 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다][azure-support].

경고의 한 가지 예는 제한적인 네트워크 보안 그룹이 있는 경우입니다. 이 구성에서는 Azure 플랫폼이 관리되는 도메인을 업데이트 및 모니터링하지 못할 수 있습니다. 경고가 생성되고 상태가 *주의 필요* 로 변경됩니다.

자세한 내용은 [관리되는 도메인에서 경고의 문제를 해결하는 방법][resolve-alerts]을 참조하세요.

### <a name="what-to-expect"></a>필요한 항목

관리되는 도메인이 *주의 필요* 상태에 있는 경우 Azure 플랫폼에서 정기적으로 데이터를 모니터링, 패치, 업데이트 또는 백업하지 못할 수 있습니다. 잘못된 네트워크 구성처럼 일부 경우에 있어서는 관리되는 도메인의 도메인 컨트롤러에 연결할 수 없습니다.

* 관리되는 도메인이 비정상 상태이며, 경고가 해결될 때까지 지속적인 상태 모니터링이 중지될 수 있습니다.
* 관리되는 도메인의 도메인 컨트롤러가 패치 또는 업데이트되지 않을 수 있습니다.
* Azure Active Directory의 변경 내용이 관리되는 도메인에 동기화되지 않을 수 있습니다.
* 관리되는 도메인의 백업이 수행되지 않을 수 있습니다.
* 관리되는 도메인에 영향을 주는 중요하지 않은 경고를 해결하는 경우 상태가 *실행 중* 상태로 돌아옵니다.
* Azure 플랫폼에서 도메인 컨트롤러에 연결할 수 없는 구성 문제에 대하여 중요한 알림이 트리거됩니다. 이러한 중요한 알림이 15일 내에 해결되지 않으면 관리되는 도메인이 *일시 중단됨* 상태가 됩니다.

## <a name="suspended-state"></a>일시 중단됨 상태

관리되는 도메인이 **일시 중단됨** 상태로 전환되는 이유는 다음과 같습니다.

* 15일 이내에 해결되지 않은 중요한 경고가 하나 이상 있습니다.
    * 중요한 경고는 Azure AD DS에 필요한 리소스에 대한 액세스를 차단하는 잘못된 구성으로 인해 발생할 수 있습니다. 관리되는 도메인에서 [AADDS104: 네트워크 오류][alert-nsg] 경고가 16일 이상 해결되지 않은 경우를 예로 들 수 있습니다.
* Azure 구독에 청구 문제가 있거나 Azure 구독이 만료되었습니다.

관리되는 도메인은 Azure 플랫폼에서 도메인을 관리, 모니터링, 패치 또는 백업할 수 없을 때 일시 중단됩니다. 관리되는 도메인은 15일 동안 *일시 중단됨* 상태로 유지됩니다. 관리되는 도메인에 대한 액세스를 유지하려면 중요한 알림을 즉시 해결해야 합니다.

### <a name="what-to-expect"></a>필요한 항목

관리되는 도메인이 *일시 중단됨* 상태에 있는 경우 다음과 같은 동작이 발생합니다.

* 관리되는 도메인의 도메인 컨트롤러가 프로비전 해제되고, 가상 네트워크 내에서 연결할 수 없습니다.
* 인터넷을 통한 관리되는 도메인에 대한 보안 LDAP 액세스가 작동 중지됩니다(사용하도록 설정된 경우).
* 관리되는 도메인에 인증, 도메인 조인 VM에 로그온, LDAP/LDAPS를 통한 연결 등이 실패합니다.
* 관리되는 도메인의 백업이 더 이상 수행되지 않습니다.
* Azure AD와 동기화가 중지됩니다.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>관리되는 도메인이 일시 중단되었는지 확인하려면 어떻게 하나요?

Azure Portal의 Azure AD DS 상태 페이지에 도메인이 일시 중단되었다고 언급하는 [경고][resolve-alerts]가 표시됩니다. 도메인 상태도 *일시 중단됨* 으로 표시됩니다.

### <a name="restore-a-suspended-domain"></a>일시 중단된 도메인 복원

*일시 중단됨* 상태에 있는 관리되는 도메인의 상태를 복원하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **도메인 서비스** 를 검색하여 선택합니다.
1. 목록에서 관리되는 도메인(예: *aaddscontoso.com*)을 선택한 다음, **상태** 를 선택합니다.
1. 일시 중단의 원인에 따라 경고를 선택합니다(예: *AADDS503* 또는 *AADDS504*).
1. 경고에 제공된 해결 링크를 선택하고 단계대로 경고를 해결합니다.

관리되는 도메인을 마지막 백업 날짜로만 복원할 수 있습니다. 마지막 백업 날짜는 관리되는 도메인의 **상태** 페이지에 표시됩니다. 마지막 백업 이후에 발생한 변경 내용은 복원되지 않습니다. 관리되는 도메인의 백업은 최대 30일 동안 저장됩니다. 30일이 지난 백업은 삭제됩니다.

관리되는 도메인이 *일시 중단됨* 상태에 있을 때 경고를 해결한 후에는 [Azure 지원 요청을 열어][azure-support] 정상 상태로 돌아갑니다. 30일 이내에 백업이 있는 경우 Azure 지원에서 관리되는 도메인을 복원할 수 있습니다.

## <a name="deleted-state"></a>삭제됨 상태

15일 동안 *일시 중단됨* 상태로 유지된 관리되는 도메인은 삭제됩니다. 이 프로세스는 복구가 불가능합니다.

### <a name="what-to-expect"></a>필요한 항목

관리되는 도메인이 *삭제됨* 상태가 되면 다음과 같은 동작이 나타납니다.

* 관리되는 도메인에 대한 모든 리소스 및 백업이 삭제됩니다.
* 관리되는 도메인을 복원할 수 없습니다. Azure AD DS를 다시 사용하려면 대체 관리되는 도메인을 만들어야 합니다.
* 관리되는 도메인이 삭제된 후에는 요금이 청구되지 않습니다.

## <a name="next-steps"></a>다음 단계

관리되는 도메인을 정상 상태로 유지하고 일시 중단되는 위험을 최소화하려면 [관리되는 도메인에 대한 경고 해결][resolve-alerts] 방법을 알아봅니다.

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
