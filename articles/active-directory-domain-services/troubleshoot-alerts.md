---
title: 'Azure Active Directory Domain Services: 경고 문제 해결 | Microsoft Docs'
description: Azure AD Domain Services에 대한 경고 문제 해결
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: ed0ff8cde39f2660c2149e43399d937184620773
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246706"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services - 경고 문제 해결
이 문서에서는 관리되는 도메인에서 발생할 수 있는 경고에 대한 문제 해결 가이드를 제공합니다.


경고의 ID 또는 메시지에 해당하는 문제 해결 단계를 선택합니다.

| **경고 ID** | **경고 메시지** | **해결 방법** |
| --- | --- | :--- |
| AADDS001 | *관리되는 도메인에 대해 인터넷을 통해 보안 LDAP가 사용되도록 설정됩니다. 그러나 포트 636에 대한 액세스는 네트워크 보안 그룹을 사용하여 잠글 수 없습니다. 이로 인해 관리되는 도메인의 사용자 계정은 암호 무차별 대입 공격에 노출될 수 있습니다.* | [잘못된 보안 LDAP 구성](alert-ldaps.md) |
| AADDS100 | *관리되는 도메인에 연결된 Azure AD 디렉터리가 삭제되었을 수 있습니다. 관리되는 도메인은 더 이상 지원되는 구성에 포함되지 않습니다. Microsoft는 관리되는 도메인을 모니터링, 관리, 패치 및 동기화할 수 없습니다.* | [누락된 디렉터리](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services를 Azure AD B2B 디렉터리에서 사용 가능하게 설정할 수 없습니다.* | [이 디렉터리에서 Azure AD B2C가 실행되고 있습니다.](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Azure AD Domain Services가 제대로 작동하는 데 필요한 서비스 주체가 Azure AD 디렉터리에서 삭제되었습니다. 이 구성은 관리되는 도메인을 모니터링, 관리, 패치, 동기화하는 Microsoft의 기능에 영향을 줍니다.* | [누락된 서비스 주체](alert-service-principal.md) |
| AADDS103 | *Azure AD Domain Services를 사용하도록 설정한 가상 네트워크의 IP 주소 범위가 공용 IP 범위에 있습니다. Azure AD Domain Services는 개인 IP 주소 범위를 갖는 가상 네트워크에서 사용되도록 설정해야 합니다. 이 구성은 관리되는 도메인을 모니터링, 관리, 패치, 동기화하는 Microsoft의 기능에 영향을 줍니다.* | [주소가 공용 IP 범위에 있습니다.](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft는 이 관리되는 도메인에 대한 도메인 컨트롤러에 연결할 수 없습니다. 가상 네트워크에 구성된 NSG(네트워크 보안 그룹)가 관리되는 도메인에 대한 액세스를 차단하려고 할 때 이 문제가 발생할 수 있습니다. 다른 가능한 이유는 인터넷에서 들어오는 트래픽을 차단하는 사용자 정의 경로가 있는 경우입니다.* | [네트워크 오류](alert-nsg.md) |
| AADDS105 | *애플리케이션 ID가 “d87dcbc6-a371-462e-88e3-28ad15ec4e64”인 서비스 주체가 삭제된 다음, 다시 생성됩니다. 다시 만들기를 수행할 경우 관리되는 도메인을 서비스하는 데 필요한 Azure AD Domain Services 리소스에 일치하지 않는 권한이 남게 됩니다. 관리되는 도메인에서 암호 동기화에 영향이 있을 수 있습니다.* | [암호 동기화 애플리케이션이 만료됨](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *관리되는 도메인에 연결된 Azure 구독이 삭제되었습니다.  제대로 계속 작동하려면 Azure AD Domain Services에 활성 구독이 있어야 합니다.* | [Azure 구독을 찾을 수 없음](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *관리되는 도메인에 연결된 Azure 구독이 활성화되지 않았습니다.  제대로 계속 작동하려면 Azure AD Domain Services에 활성 구독이 있어야 합니다.* | [Azure 구독이 비활성화됨](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Azure AD Domain Services에서 사용 하는 구독을 다른 디렉터리로 이동했습니다. Azure AD Domain Services는 제대로 작동하려면 같은 디렉터리에 활성 구독을 포함해야 합니다.* | [구독 이동 디렉터리](#aadds108-subscription-moved-directories) |
| AADDS109 | *관리되는 도메인에 사용되는 리소스가 삭제되었습니다. 이 리소스는 Azure AD Domain Services가 제대로 작동하기 위해 필요합니다.* | [리소스가 삭제됨](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Azure AD Domain Services의 배포에 선택된 서브넷이 꽉 차서 만들어야 하는 추가 도메인 컨트롤러에 사용할 수 있는 공간이 없습니다.* | [서브넷이 꽉 참](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Azure AD Domain Services가 도메인을 서비스하는 데 사용하는 서비스 주체는 Azure 구독에서 리소스를 관리할 권한이 없습니다. 서비스 주체는 관리되는 도메인을 서비스하기 위한 사용 권한을 획득해야 합니다.* | [권한 없는 서비스 주체](#aadds111-service-principal-unauthorized) |
| AADDS112 | *이 도메인에서 가상 네트워크의 서브넷에 충분한 IP 주소가 없을 수도 있음을 확인했습니다. Azure AD Domain Services에는 두 개의 이상의 사용 가능한 IP 주소가 활성화된 서브넷 내에 있어야 합니다. 최소 3-5개 예비 IP 주소가 서브넷 내에 있는 것이 좋습니다. 이는 다른 가상 머신이 서브넷 내에 배포되어 사용 가능한 IP 주소 수가 소진되거나 서브넷에서 사용 가능한 IP 주소 수가 제한되는 경우에 발생할 수 있습니다.* | [IP 주소가 부족함](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Azure AD Domain Services에서 사용되는 리소스가 예기치 않은 상태로 발견되었으며 복구할 수 없습니다.* | [리소스를 복구할 수 없음](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *Azure AD Domain Services의 배포를 위해 선택한 서브넷이 유효하지 않아 사용할 수 없습니다.* | [잘못된 서브넷](#aadds114-subnet-invalid) |
| AADDS115 | *대상 범위가 잠겨 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.* | [리소스가 잠김](#aadds115-resources-are-locked) |
| AADDS116 | *정책 제한으로 인해 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.* | [리소스를 사용할 수 없음](#aadds116-resources-are-unusable) |
| AADDS500 | *관리되는 도메인은 [date]에 Azure AD와 마지막으로 동기화되었습니다. 사용자가 관리되는 도메인에서 로그인할 수 없거나 그룹 멤버 자격이 Azure AD와 동기화되지 않을 수 있습니다.* | [잠시 후에 동기화가 수행되지 않았습니다.](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *관리되는 도메인은 마지막으로 [date]에 백업되었습니다.* | [잠시 후에 백업이 수행되지 않았습니다.](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *관리되는 도메인에 대한 보안 LDAP 인증서는 [date]에 만료됩니다.* | [보안 LDAP 인증서 만료](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *해당 도메인과 연결된 Azure 구독이 활성 상태가 아니기 때문에 관리되는 도메인은 일시 중단됩니다.* | [비활성화된 구독으로 인한 일시 중단](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *관리되는 도메인은 잘못된 구성으로 인해 일시 중단됩니다. 서비스는 오랜 시간 동안 관리되는 도메인의 도메인 컨트롤러를 관리하거나, 패치하거나, 업데이트할 수 없었습니다.* | [잘못된 구성으로 인한 일시 중단](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: 누락된 디렉터리
**경고 메시지:**

*관리되는 도메인에 연결된 Azure AD 디렉터리가 삭제되었을 수 있습니다. 관리되는 도메인은 더 이상 지원되는 구성에 포함되지 않습니다. Microsoft는 관리되는 도메인을 모니터링, 관리, 패치 및 동기화할 수 없습니다.*

**해결 방법:**

이 오류는 일반적으로 Azure 구독을 새 Azure AD 디렉터리로 잘못 이동하고, 여전히 Azure AD Domain Services와 연결되어 있는 이전 Azure AD 디렉터리를 삭제하는 경우에 발생합니다.

이 오류는 복구할 수 없습니다. 이 문제를 해결하려면 [기존의 관리되는 도메인을 삭제](delete-aadds.md)하고 새 디렉터리에 다시 만듭니다. 삭제하는 데 문제가 있으면 Azure Active Directory Domain Services 제품 팀에 문의하여 [지원](contact-us.md)을 요청하세요.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C가 이 디렉터리에서 실행되고 있음
**경고 메시지:**

*Azure AD Domain Services를 Azure AD B2B 디렉터리에서 사용 가능하게 설정할 수 없습니다.*

**해결 방법:**

>[!NOTE]
>Azure AD Domain Services를 계속 사용하려면 Azure AD B2C가 아닌 디렉터리에서 Azure AD Domain Services 인스턴스를 다시 만들어야 합니다.

서비스를 복원하려면 다음 단계를 수행합니다.

1. 기존 Azure AD 디렉터리에서 [관리되는 도메인을 삭제](delete-aadds.md)합니다.
2. Azure AD B2C 디렉터리가 아닌 새 디렉터리를 만듭니다.
3. [시작](create-instance.md) 가이드에 따라 관리되는 도메인을 다시 만듭니다.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: 주소가 공용 IP 범위에 있음

**경고 메시지:**

*Azure AD Domain Services를 사용하도록 설정한 가상 네트워크의 IP 주소 범위가 공용 IP 범위에 있습니다. Azure AD Domain Services는 개인 IP 주소 범위를 갖는 가상 네트워크에서 사용되도록 설정해야 합니다. 이 구성은 관리되는 도메인을 모니터링, 관리, 패치, 동기화하는 Microsoft의 기능에 영향을 줍니다.*

**해결 방법:**

> [!NOTE]
> 이 문제를 해결하기 위해 기존의 관리되는 도메인을 삭제하고, 개인 IP 주소 범위를 사용하여 가상 네트워크에 다시 만들어야 합니다. 이 프로세스는 중단됩니다.

시작하기 전에 [이 문서](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)의 **프라이빗 IP v4 주소 공간** 섹션을 읽습니다.

가상 네트워크 내에서 컴퓨터는 서브넷에 대해 구성된 것과 동일한 IP 주소 범위에 있는 Azure 리소스를 요청할 수 있습니다. 그러나 가상 네트워크가 이 범위에서 구성되므로 해당 요청은 가상 네트워크 내에서 라우팅되고 의도한 웹 리소스에 도달하지 않습니다. 이 구성으로 인해 Azure AD Domain Services에서 예측할 수 없는 오류가 발생할 수 있습니다.

**가상 네트워크에서 구성된 인터넷의 IP 주소 범위가 있는 경우 이 경고는 무시될 수 있습니다. 그러나 Azure AD Domain Services는 예측할 수 없는 오류를 일으킬 수 있으므로 이 구성을 사용하여 [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)]에 커밋할 수 없습니다.**


1. 디렉터리에서 [관리되는 도메인을 삭제](delete-aadds.md)합니다.
2. 서브넷에 대한 IP 주소 범위 수정
   1. [Azure Portal의 가상 네트워크 페이지](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)로 이동합니다.
   2. Azure AD Domain Services에 사용하려는 가상 네트워크를 선택합니다.
   3. 설정 아래의 **주소 공간**을 클릭합니다.
   4. 기존 주소 범위를 클릭하고 편집하거나 다른 주소 범위를 추가하여 주소 범위를 업데이트합니다. 새 주소 범위가 프라이빗 IP 범위에 있는지 확인합니다. 변경 내용을 저장합니다.
   5. 왼쪽 탐색 창에서 **서브넷**을 클릭합니다.
   6. 테이블에서 편집하려는 서브넷을 클릭합니다.
   7. 주소 범위를 업데이트하고 변경 내용을 저장합니다.
3. [Azure AD Domain Services를 사용하여 시작 가이드](create-instance.md)에 따라 관리되는 도메인을 다시 만듭니다. 개인 IP 주소 범위를 갖는 가상 네트워크를 선택해야 합니다.
4. 가상 머신을 새 도메인에 가입하려면 [이 가이드](join-windows-vm.md)를 따릅니다.
8. 경고를 해결하려면 두 시간 내에 도메인의 상태를 확인합니다.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Azure 구독을 찾을 수 없음

**경고 메시지:**

*관리되는 도메인에 연결된 Azure 구독이 삭제되었습니다.  제대로 계속 작동하려면 Azure AD Domain Services에 활성 구독이 있어야 합니다.*

**해결 방법:**

Azure AD Domain Services는 함수에 대한 구독이 필요하며 다른 구독으로 이동할 수 없습니다. 관리되는 도메인이 연결된 Azure 구독이 삭제되었으므로 Azure 구독 및 Azure AD Domain Services를 다시 생성해야 합니다.

1. Azure 구독을 만듭니다.
2. 기존 Azure AD 디렉터리에서 [관리되는 도메인을 삭제](delete-aadds.md)합니다.
3. [시작](create-instance.md) 가이드에 따라 관리되는 도메인을 다시 만듭니다.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Azure 구독이 사용하지 않도록 설정됨

**경고 메시지:**

*관리되는 도메인에 연결된 Azure 구독이 활성화되지 않았습니다.  제대로 계속 작동하려면 Azure AD Domain Services에 활성 구독이 있어야 합니다.*

**해결 방법:**


1. [Azure 구독을 갱신합니다](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. 구독이 갱신되면 Azure AD Domain Services는 Azure에서 관리되는 도메인을 다시 활성화하기 위한 알림을 받게 됩니다.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: 구독 이동 디렉터리

**경고 메시지:**

*Azure AD Domain Services에서 사용 하는 구독을 다른 디렉터리로 이동했습니다. Azure AD Domain Services는 제대로 작동하려면 같은 디렉터리에 활성 구독을 포함해야 합니다.*

**해결 방법:**

Azure AD Domain Services와 연결된 구독을 이전 디렉터리로 다시 이동할 수 있거나, 기존 디렉터리에서 [관리되는 도메인을 삭제](delete-aadds.md)하고 선택한 디렉터리에서 도메인을 다시 만들어야 합니다(새 구독을 사용하거나 Azure AD Domain Services 인스턴스가 있는 디렉터리를 변경하여).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: 관리되는 도메인의 리소스를 찾을 수 없음

**경고 메시지:**

*관리되는 도메인에 사용되는 리소스가 삭제되었습니다. 이 리소스는 Azure AD Domain Services가 제대로 작동하기 위해 필요합니다.*

**해결 방법:**

Azure AD Domain Services는 제대로 작동하기 위해 배포하는 동안 공용 IP 주소, NIC 및 부하 분산 장치를 포함한 특정 리소스를 만듭니다. 명명된 어떠한 항목이 삭제된 경우에는 이로 인해 관리되는 도메인이 지원되지 않는 상태가 되며 도메인이 관리되는 것을 방지합니다. 이 경고는 Azure AD Domain Services 리소스를 편집할 수 있는 사용자가 필요한 리소스를 삭제할 때 발생합니다. 다음 단계에서는 관리되는 도메인을 복원하는 방법을 간략하게 설명합니다.

1. Azure AD Domain Services 상태 페이지로 이동합니다.
   1.    Azure Portal에서 [Azure AD Domain Services 페이지](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)로 이동합니다.
   2.    왼쪽 탐색에서 **상태**를 클릭합니다.
2. 경고가 4시간 미만인지 확인합니다.
   1.    상태 페이지에서 **AADDS109** ID를 사용하여 경고 클릭
   2.    경고에는 처음 발견된 시간에 대한 타임스탬프가 있습니다. 해당 타임스탬프가 4시간 미만인 경우 Azure AD Domain Services가 삭제된 리소스를 다시 만들 수 있습니다.
3. 경고가 4시간을 초과하면 관리되는 도메인은 복구할 수 없는 상태입니다. Azure AD Domain Services를 삭제하고 다시 만들어야 합니다.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: 관리되는 도메인에 연결된 서브넷이 꽉 찼음

**경고 메시지:**

*Azure AD Domain Services의 배포에 선택된 서브넷이 꽉 차서 만들어야 하는 추가 도메인 컨트롤러에 사용할 수 있는 공간이 없습니다.*

**해결 방법:**

이 오류는 복구할 수 없습니다. 이 문제를 해결하려면 [기존의 관리되는 도메인을 삭제](delete-aadds.md)하고 [관리되는 도메인을 다시 만듭니다.](create-instance.md)

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: 권한 없는 서비스 주체

**경고 메시지:**

*Azure AD Domain Services가 도메인을 서비스하는 데 사용하는 서비스 주체는 Azure 구독에서 리소스를 관리할 권한이 없습니다. 서비스 주체는 관리되는 도메인을 서비스하기 위한 사용 권한을 획득해야 합니다.*

**해결 방법:**

해당 서비스 주체는 관리되는 도메인에서 리소스를 만들고 관리할 수 있는 액세스 권한이 필요합니다. 이제 누군가 서비스 주체 액세스를 거부했으므로 리소스를 관리할 수 없습니다. 서비스 주체에 대한 액세스 권한을 부여하는 단계를 따릅니다.

1. [RBAC 제어 및 Azure Portal에서 애플리케이션에 대한 액세스 권한을 부여하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)에 대한 참조
2. ```abba844e-bc0e-44b0-947a-dc74e5d09022``` ID가 있는 서비스 주체에 대한 액세스를 검토하고 전에 거부된 액세스 권한을 부여합니다.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: 관리되는 도메인의 IP 주소가 부족함

**경고 메시지:**

*이 도메인에서 가상 네트워크의 서브넷에 충분한 IP 주소가 없을 수도 있음을 확인했습니다. Azure AD Domain Services에는 두 개의 이상의 사용 가능한 IP 주소가 활성화된 서브넷 내에 있어야 합니다. 최소 3-5개 예비 IP 주소가 서브넷 내에 있는 것이 좋습니다. 이는 다른 가상 머신이 서브넷 내에 배포되어 사용 가능한 IP 주소 수가 소진되거나 서브넷에서 사용 가능한 IP 주소 수가 제한되는 경우에 발생할 수 있습니다.*

**해결 방법:**

1. 테넌트에서 관리되는 도메인을 삭제합니다.
2. 서브넷에 대한 IP 주소 범위 수정
   1. [Azure Portal의 가상 네트워크 페이지](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)로 이동합니다.
   2. Azure AD Domain Services에 사용하려는 가상 네트워크를 선택합니다.
   3. 설정 아래의 **주소 공간**을 클릭합니다.
   4. 기존 주소 범위를 클릭하고 편집하거나 다른 주소 범위를 추가하여 주소 범위를 업데이트합니다. 변경 내용을 저장합니다.
   5. 왼쪽 탐색 창에서 **서브넷**을 클릭합니다.
   6. 테이블에서 편집하려는 서브넷을 클릭합니다.
   7. 주소 범위를 업데이트하고 변경 내용을 저장합니다.
3. [Azure AD Domain Services를 사용하여 시작 가이드](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)에 따라 관리되는 도메인을 다시 만듭니다. 개인 IP 주소 범위를 갖는 가상 네트워크를 선택해야 합니다.
4. 가상 머신을 새 도메인에 가입하려면 [이 가이드](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)를 따릅니다.
5. 2시간 후에 도메인 상태를 확인하여 단계를 올바르게 완료했는지 검사합니다.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: 리소스를 복구할 수 없음

**경고 메시지:**

*Azure AD Domain Services에서 사용되는 리소스가 예기치 않은 상태로 발견되었으며 복구할 수 없습니다.*

**해결 방법:**

이 오류는 복구할 수 없습니다. 이를 해결하려면 [기존의 관리되는 도메인을 삭제](delete-aadds.md)하고 [관리되는 도메인을 다시 만들어야](create-instance.md) 합니다.

## <a name="aadds114-subnet-invalid"></a>AADDS114: 잘못된 서브넷

**경고 메시지:**

*Azure AD Domain Services의 배포를 위해 선택한 서브넷이 유효하지 않아 사용할 수 없습니다.*

**해결 방법:**

이 오류는 복구할 수 없습니다. 이를 해결하려면 [기존의 관리되는 도메인을 삭제](delete-aadds.md)하고 [관리되는 도메인을 다시 만들어야](create-instance.md) 합니다.

## <a name="aadds115-resources-are-locked"></a>AADDS115: 리소스가 잠김

**경고 메시지:**

*대상 범위가 잠겨 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.*

**해결 방법:**

1.  네트워크 리소스에서 Resource Manager 작업 로그를 검토합니다(여기에는 어떤 잠금으로 인해 수정이 금지되는지에 대한 정보가 포함되어야 함).
2.  Azure AD Domain Services 서비스 주체가 작동할 수 있도록 리소스에 대한 잠금을 제거합니다.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: 리소스를 사용할 수 없음

**경고 메시지:**

*정책 제한으로 인해 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.*

**해결 방법:**

1.  관리형 도메인에 대한 네트워크 리소스에서 Resource Manager 작업 로그를 검토합니다.
2.  AAD-DS 서비스 주체가 작동할 수 있도록 리소스에서 정책 제한을 약화시킵니다.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: 한 동안 동기화가 완료되지 않음

**경고 메시지:**

*관리되는 도메인은 [date]에 Azure AD와 마지막으로 동기화되었습니다. 사용자가 관리되는 도메인에서 로그인할 수 없거나 그룹 멤버 자격이 Azure AD와 동기화되지 않을 수 있습니다.*

**해결 방법:**

관리되는 도메인의 구성에서 문제를 나타낼 수 있는 모든 경고에 대한 [도메인의 상태를 확인](check-health.md)합니다. 경우에 따라 구성 관련 문제는 관리되는 도메인을 동기화하는 Microsoft의 기능을 차단할 수 있습니다. 경고를 해결할 수 있는 경우 두 시간 동안 대기하고 동기화가 완료되었는지를 다시 확인합니다.

관리되는 도메인에서 동기화가 중지되는 몇 가지 일반적인 원인은 다음과 같습니다.
- 관리되는 도메인에서 네트워크 연결이 차단된 경우. 네트워크의 문제를 확인하는 방법에 대한 자세한 내용은 [네트워크 보안 그룹 문제 해결](alert-nsg.md) 방법과 [Azure AD Domain Services의 네트워크 요구 사항](network-considerations.md)을 읽어보세요.
-  암호 동기화가 설정되지 않았거나 완료되지 않은 경우. 암호 동기화를 설정하려면 [이 문서](active-directory-ds-getting-started-password-sync.md)를 읽어보세요.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: 한 동안 백업이 수행되지 않음

**경고 메시지:**

*관리되는 도메인은 마지막으로 [date]에 백업되었습니다.*

**해결 방법:**

관리되는 도메인의 구성에서 문제를 나타낼 수 있는 모든 경고에 대한 [도메인의 상태를 확인](check-health.md)합니다. 경우에 따라 구성 관련 문제는 관리되는 도메인을 백업하는 Microsoft의 기능을 차단할 수 있습니다. 경고를 해결할 수 있는 경우 두 시간 동안 대기하고 백업이 완료되었는지 다시 확인합니다.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: 비활성화된 구독으로 인한 일시 중단

**경고 메시지:**

*해당 도메인과 연결된 Azure 구독이 활성 상태가 아니기 때문에 관리되는 도메인은 일시 중단됩니다.*

**해결 방법:**

> [!WARNING]
> 관리되는 도메인이 장시간 동안 일시 중단될 경우 삭제될 위험이 있습니다. 가능한 한 빠르게 일시 중단을 해결하는 것이 좋습니다. 자세한 내용은 [이 문서](suspension.md)를 참조하세요.

서비스를 복원하려면 관리되는 도메인에 연결된 [Azure 구독을 갱신](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)합니다.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: 잘못된 구성으로 인한 일시 중단

**경고 메시지:**

*관리되는 도메인은 잘못된 구성으로 인해 일시 중단됩니다. 서비스는 오랜 시간 동안 관리되는 도메인의 도메인 컨트롤러를 관리하거나, 패치하거나, 업데이트할 수 없었습니다.*

**해결 방법:**

> [!WARNING]
> 관리되는 도메인이 장시간 동안 일시 중단될 경우 삭제될 위험이 있습니다. 가능한 한 빠르게 일시 중단을 해결하는 것이 좋습니다. 자세한 내용은 [이 문서](suspension.md)를 참조하세요.

관리되는 도메인의 구성에서 문제를 나타낼 수 있는 모든 경고에 대한 [도메인의 상태를 확인](check-health.md)합니다. 이러한 경고를 해결할 수 있는 경우 수행합니다. 이후에 구독을 다시 활성화하려면 지원에 문의하세요.


## <a name="contact-us"></a>문의처
[지원이 필요하거나 피드백을 공유하려면](contact-us.md)Azure Active Directory Domain Services 제품 팀에 문의하세요.
