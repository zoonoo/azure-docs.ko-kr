---
title: Azure AD 도메인 서비스의 일반적인 경고 및 해결 방법 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스의 상태 상태의 일부로 생성된 일반적인 경고를 해결하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c83caf31e25ae2212ed120e77e017ac3849898e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612902"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>알려진 문제: Azure Active Directory 도메인 서비스의 일반적인 경고 및 해결 방법

응용 프로그램에 대한 ID 및 인증의 핵심 부분으로 Azure Active Directory 도메인 서비스(Azure AD DS)에 문제가 있는 경우가 있습니다. 문제가 발생하면 몇 가지 일반적인 경고 및 관련 문제 해결 단계가 있어 작업을 다시 실행하는 데 도움이 됩니다. 언제든지 추가 문제 해결 지원을 위해 [Azure 지원 요청을 열][azure-support] 수도 있습니다.

이 문서에서는 Azure AD DS의 일반적인 경고에 대한 문제 해결 정보를 제공합니다.

## <a name="aadds100-missing-directory"></a>AADDS100: 누락된 디렉터리

### <a name="alert-message"></a>경고 메시지

*관리 되는 도메인과 연결 된 Azure AD 디렉터리 삭제 되었을 수 있습니다. 관리되는 도메인이 더 이상 지원되는 구성에 없습니다. Microsoft는 관리되는 도메인을 모니터링, 관리, 패치 및 동기화할 수 없습니다.*

### <a name="resolution"></a>해결 방법

이 오류는 일반적으로 Azure 구독이 새 Azure AD 디렉터리로 이동하고 Azure AD DS와 연결된 이전 Azure AD 디렉터리에서 삭제될 때 발생합니다.

이 오류는 복구할 수 없습니다. 경고를 해결하려면 [기존 Azure AD DS 관리 도메인을 삭제하고](delete-aadds.md) 새 디렉터리에서 다시 만듭니다. Azure AD DS 관리 도메인을 삭제하는 데 문제가 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C가 이 디렉터리에서 실행 중임

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services를 Azure AD B2B 디렉터리에서 사용 가능하게 설정할 수 없습니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS는 Azure AD 디렉터리와 자동으로 동기화됩니다. Azure AD 디렉터리B2C에 대해 구성된 경우 Azure AD DS를 배포하고 동기화할 수 없습니다.

Azure AD DS를 사용하려면 다음 단계를 사용하여 Azure AD B2C 디렉터리 이외의 관리 되는 도메인을 다시 만들어야 합니다.

1. 기존 [Azure AD 디렉터리에서 Azure AD DS 관리 도메인을 삭제합니다.](delete-aadds.md)
1. Azure AD B2C 디렉터리가 아닌 새 Azure AD 디렉터리를 만듭니다.
1. [대체 Azure AD DS 관리 도메인을 만듭니다.](tutorial-create-instance.md)

Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: 주소가 공용 IP 범위에 있음

### <a name="alert-message"></a>경고 메시지

*Azure AD 도메인 서비스를 사용하도록 설정한 가상 네트워크의 IP 주소 범위는 공용 IP 범위에 있습니다. Azure AD 도메인 서비스는 개인 IP 주소 범위가 있는 가상 네트워크에서 활성화되어야 합니다. 이 구성은 관리되는 도메인을 모니터링, 관리, 패치 및 동기화하는 Microsoft의 기능에 영향을 미칩니다.*

### <a name="resolution"></a>해결 방법

시작하기 전에 개인 IP [v4 주소 공간을](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)이해해야 합니다.

가상 네트워크 내에서 VM은 서브넷에 대해 구성된 것과 동일한 IP 주소 범위의 Azure 리소스에 대한 요청을 할 수 있습니다. 서브넷에 대한 공용 IP 주소 범위를 구성하는 경우 가상 네트워크 내에서 라우팅된 요청이 의도한 웹 리소스에 도달하지 못할 수 있습니다. 이 구성으로 인해 Azure AD DS에서 예기치 않은 오류가 발생할 수 있습니다.

> [!NOTE]
> 가상 네트워크에서 구성된 인터넷의 IP 주소 범위가 있는 경우 이 경고는 무시될 수 있습니다. 그러나 Azure AD 도메인 서비스는 예측할 수 없는 오류가 발생할 수 있으므로 이 구성으로 [SLA에](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)커밋할 수 없습니다.

이 경고를 해결하려면 기존 Azure AD DS 관리 도메인을 삭제하고 개인 IP 주소 범위가 있는 가상 네트워크에서 다시 만듭니다. 이 프로세스는 Azure AD DS 관리 도메인을 사용할 수 없고 O또는 서비스 계정과 같이 만든 사용자 지정 리소스가 손실되기 때문에 중단됩니다.

1. 디렉터리에서 [Azure AD DS 관리 도메인을 삭제합니다.](delete-aadds.md)
1. 가상 네트워크 IP 주소 범위를 업데이트하려면 Azure 포털에서 *가상 네트워크를* 검색하고 선택합니다. 공용 IP 주소 범위가 잘못 설정된 Azure AD DS의 가상 네트워크를 선택합니다.
1. **설정에서** *주소 공간을*선택합니다.
1. 기존 주소 범위를 선택하고 편집하거나 주소 범위를 추가하여 주소 범위를 업데이트합니다. 새 IP 주소 범위가 개인 IP 범위에 있는지 확인합니다. 준비가 되면 변경 내용을 **저장합니다.**
1. 왼쪽 탐색에서 **서브넷을 선택합니다.**
1. 편집할 서브넷을 선택하거나 추가 서브넷을 만듭니다.
1. 개인 IP 주소 범위를 업데이트하거나 지정한 다음 변경 내용을 **저장합니다.**
1. [대체 Azure AD DS 관리 도메인을 만듭니다.](tutorial-create-instance.md) 개인 IP 주소 범위가 있는 업데이트된 가상 네트워크 서브넷을 선택해야 합니다.

Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Azure 구독을 찾을 수 없음

### <a name="alert-message"></a>경고 메시지

*관리 되는 도메인과 연결 된 Azure 구독이 삭제 되었습니다.  Azure AD 도메인 서비스는 제대로 작동을 계속하려면 활성 구독이 필요합니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS에는 활성 구독이 필요하며 다른 구독으로 이동할 수 없습니다. Azure AD DS 관리 도메인이 연결된 Azure 구독이 삭제된 경우 Azure 구독 및 Azure AD DS 관리 도메인을 다시 만들어야 합니다.

1. [Azure 구독을 만듭니다.](../cost-management-billing/manage/create-subscription.md)
1. 기존 [Azure AD 디렉터리에서 Azure AD DS 관리 도메인을 삭제합니다.](delete-aadds.md)
1. [대체 Azure AD DS 관리 도메인을 만듭니다.](tutorial-create-instance.md)

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Azure 구독이 비활성화됨

### <a name="alert-message"></a>경고 메시지

*관리되는 도메인과 연결된 Azure 구독이 활성화되어 있지 않습니다.  Azure AD 도메인 서비스는 제대로 작동을 계속하려면 활성 구독이 필요합니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS에는 활성 구독이 필요합니다. Azure AD DS 관리 도메인이 연결된 Azure 구독이 활성화되어 있지 않은 경우 구독을 다시 활성화하려면 해당 구독을 갱신해야 합니다.

1. [Azure 구독을 갱신합니다](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. 구독이 갱신되면 Azure AD DS 알림을 통해 관리되는 도메인을 다시 활성화할 수 있습니다.

관리되는 도메인을 다시 사용하도록 설정하면 Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: 구독 이동 디렉터리

### <a name="alert-message"></a>경고 메시지

*Azure AD 도메인 서비스에서 사용하는 구독이 다른 디렉터리로 이동되었습니다. Azure AD 도메인 서비스가 제대로 작동하려면 동일한 디렉터리에서 활성 구독이 있어야 합니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS에는 활성 구독이 필요하며 다른 구독으로 이동할 수 없습니다. Azure AD DS 관리 도메인이 연결된 Azure 구독이 이동된 경우 구독을 이전 디렉터리로 다시 이동하거나 기존 디렉터리에서 [관리되는 도메인을 삭제하고](delete-aadds.md) [선택한 구독에서 대체 Azure AD DS 관리 도메인을 만듭니다.](tutorial-create-instance.md)

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: 관리되는 도메인에 대한 리소스를 찾을 수 없음

### <a name="alert-message"></a>경고 메시지

*관리 되는 도메인에 사용 되는 리소스가 삭제 되었습니다. Azure AD 도메인 서비스가 제대로 작동하려면 이 리소스가 필요합니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS는 공용 IP 주소, 가상 네트워크 인터페이스 및 로드 밸런서와 같이 제대로 작동할 추가 리소스를 만듭니다. 이러한 리소스 중 어느 라도 삭제되면 관리되는 도메인이 지원되지 않는 상태에 있으며 도메인이 관리되지 않습니다. 이러한 리소스에 대한 자세한 내용은 [Azure AD DS에서 사용하는 네트워크 리소스를](network-considerations.md#network-resources-used-by-azure-ad-ds)참조하십시오.

이 경고는 이러한 필수 리소스 중 하나가 삭제될 때 생성됩니다. 리소스가 4시간 이내에 삭제된 경우 Azure 플랫폼에서 삭제된 리소스를 자동으로 다시 만들 수 있습니다. 다음 단계에서는 상태 상태 및 리소스 삭제를 위한 타임스탬프를 확인하는 방법을 간략하게 설명합니다.

1. Azure 포털에서 **도메인 서비스를**검색하고 선택합니다. *aaddscontoso.com*와 같은 Azure AD DS 관리 도메인을 선택합니다.
1. 왼쪽 탐색에서 **상태를**선택합니다.
1. 상태 페이지에서 ID *AADDS109를*사용할 수 있는 경고를 선택합니다.
1. 경고에는 처음 발견된 시간에 대한 타임스탬프가 있습니다. 해당 타임스탬프가 4시간 미만이면 Azure 플랫폼에서 리소스를 자동으로 다시 만들고 자체적으로 경고를 해결할 수 있습니다.

    경고가 4시간 이상 지난 경우 Azure AD DS 관리 도메인은 복구할 수 없는 상태입니다. [Azure AD DS 관리 도메인을 삭제한](delete-aadds.md) 다음 [대체 관리되는 도메인을 만듭니다.](tutorial-create-instance.md)

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: 관리되는 도메인에 연결된 서브넷이 꽉 찼음

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services의 배포에 선택된 서브넷이 꽉 차서 만들어야 하는 추가 도메인 컨트롤러에 사용할 수 있는 공간이 없습니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS용 가상 네트워크 서브넷에는 자동으로 생성된 리소스에 충분한 IP 주소가 필요합니다. 이 IP 주소 공간에는 유지 관리 이벤트가 있는 경우 대체 리소스를 만들어야 합니다. 사용 가능한 IP 주소가 부족할 위험을 최소화하려면 자체 VM과 같은 추가 리소스를 Azure AD DS와 동일한 가상 네트워크 서브넷에 배포하지 마십시오.

이 오류는 복구할 수 없습니다. 경고를 해결하려면 [기존 Azure AD DS 관리 도메인을 삭제하고](delete-aadds.md) 다시 만듭니다. Azure AD DS 관리 도메인을 삭제하는 데 문제가 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: 서비스 주체가 무단으로

### <a name="alert-message"></a>경고 메시지

*Azure AD 도메인 서비스가 도메인을 서비스하는 데 사용하는 서비스 주체는 Azure 구독의 리소스를 관리할 권한이 없습니다. 서비스 주체는 관리되는 도메인을 서비스할 수 있는 권한을 얻어야 합니다.*

### <a name="resolution"></a>해결 방법

자동으로 생성된 일부 서비스 주체는 Azure AD DS 관리 도메인에 대한 리소스를 관리하고 만드는 데 사용됩니다. 이러한 서비스 주체 중 하나에 대한 액세스 권한이 변경되면 도메인이 리소스를 올바르게 관리할 수 없습니다. 다음 단계에서는 서비스 주체에게 액세스 권한을 이해하고 부여하는 방법을 보여 주며, 이 방법을 보여 준다.

1. 역할 [기반 액세스 제어 및 Azure 포털의 응용 프로그램에 대한 액세스 권한을 부여하는 방법에](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)대해 읽어보십시오.
2. ID *abba844e-bc0e-44b0-947a-dc74e5d09022가* 있는 서비스 주체가 이전 날짜에 거부된 액세스 권한을 부여하는 액세스를 검토합니다.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: 관리되는 도메인에 IP 주소가 충분하지 않음

### <a name="alert-message"></a>경고 메시지

*이 도메인의 가상 네트워크의 서브넷에 IP 주소가 충분하지 않을 수 있음을 확인했습니다. Azure AD 도메인 서비스에는 활성화된 서브넷 내에서 사용 가능한 IP 주소가 두 개 이상 필요합니다. 서브넷 내에 최소 3~5개의 예비 IP 주소를 두는 것이 좋습니다. 서브넷 내에 다른 가상 시스템이 배포되어 사용 가능한 IP 주소 수가 소진되거나 서브넷에서 사용 가능한 IP 주소 수에 제한이 있는 경우 이러한 문제가 발생할 수 있습니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS의 가상 네트워크 서브넷에는 자동으로 생성된 리소스에 충분한 IP 주소가 필요합니다. 이 IP 주소 공간에는 유지 관리 이벤트가 있는 경우 대체 리소스를 만들어야 합니다. 사용 가능한 IP 주소가 부족할 위험을 최소화하려면 자체 VM과 같은 추가 리소스를 Azure AD DS와 동일한 가상 네트워크 서브넷에 배포하지 마십시오.

이 경고를 해결하려면 기존 Azure AD DS 관리 도메인을 삭제하고 충분한 IP 주소 범위가 있는 가상 네트워크에서 다시 만듭니다. 이 프로세스는 Azure AD DS 관리 도메인을 사용할 수 없고 O또는 서비스 계정과 같이 만든 사용자 지정 리소스가 손실되기 때문에 중단됩니다.

1. 디렉터리에서 [Azure AD DS 관리 도메인을 삭제합니다.](delete-aadds.md)
1. 가상 네트워크 IP 주소 범위를 업데이트하려면 Azure 포털에서 *가상 네트워크를* 검색하고 선택합니다. 작은 IP 주소 범위가 있는 Azure AD DS의 가상 네트워크를 선택합니다.
1. **설정에서** *주소 공간을*선택합니다.
1. 기존 주소 범위를 선택하고 편집하거나 주소 범위를 추가하여 주소 범위를 업데이트합니다. 새 IP 주소 범위가 Azure AD DS 서브넷 범위에 충분히 큰지 확인합니다. 준비가 되면 변경 내용을 **저장합니다.**
1. 왼쪽 탐색에서 **서브넷을 선택합니다.**
1. 편집할 서브넷을 선택하거나 추가 서브넷을 만듭니다.
1. 충분한 IP 주소 범위를 업데이트하거나 지정한 다음 변경 내용을 **저장합니다.**
1. [대체 Azure AD DS 관리 도메인을 만듭니다.](tutorial-create-instance.md) 충분한 IP 주소 범위가 있는 업데이트된 가상 네트워크 서브넷을 선택해야 합니다.

Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: 리소스를 복구할 수 없음

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services에서 사용되는 리소스가 예기치 않은 상태로 발견되었으며 복구할 수 없습니다.*

### <a name="resolution"></a>해결 방법

이 오류는 복구할 수 없습니다. 경고를 해결하려면 [기존 Azure AD DS 관리 도메인을 삭제하고](delete-aadds.md) 다시 만듭니다. Azure AD DS 관리 도메인을 삭제하는 데 문제가 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

## <a name="aadds114-subnet-invalid"></a>AADDS114: 잘못된 서브넷

### <a name="alert-message"></a>경고 메시지

*Azure AD 도메인 서비스 배포에 대해 선택한 서브넷은 유효하지 않으며 사용할 수 없습니다.*

### <a name="resolution"></a>해결 방법

이 오류는 복구할 수 없습니다. 경고를 해결하려면 [기존 Azure AD DS 관리 도메인을 삭제하고](delete-aadds.md) 다시 만듭니다. Azure AD DS 관리 도메인을 삭제하는 데 문제가 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

## <a name="aadds115-resources-are-locked"></a>AADDS115: 리소스가 잠김

### <a name="alert-message"></a>경고 메시지

*대상 범위가 잠겨 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.*

### <a name="resolution"></a>해결 방법

변경 또는 삭제를 방지하기 위해 Azure 리소스에 리소스 잠금을 적용할 수 있습니다. Azure AD DS는 관리되는 서비스이기 때문에 Azure 플랫폼에는 구성을 변경할 수 있는 기능이 필요합니다. 일부 Azure AD DS 구성 요소에 리소스 잠금이 적용된 경우 Azure 플랫폼은 관리 작업을 수행할 수 없습니다.

Azure AD DS 구성 요소의 리소스 잠금을 확인하고 제거하려면 다음 단계를 완료하십시오.

1. 가상 네트워크, 네트워크 인터페이스 또는 공용 IP 주소와 같은 리소스 그룹의 각 Azure AD DS 네트워크 구성 요소에 대해 Azure 포털의 작업 로그를 확인합니다. 이러한 작업 로그는 작업이 실패한 이유와 리소스 잠금이 적용되는 위치를 나타내야 합니다.
1. 잠금이 적용되는 리소스를 선택한 다음 Locks 에서 **잠금을**선택하고 잠금을 선택합니다.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: 리소스를 사용할 수 없음

### <a name="alert-message"></a>경고 메시지

*정책 제한으로 인해 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.*

### <a name="resolution"></a>해결 방법

정책은 허용되는 구성 작업을 제어하는 Azure 리소스 및 리소스 그룹에 적용됩니다. Azure AD DS는 관리되는 서비스이기 때문에 Azure 플랫폼에는 구성을 변경할 수 있는 기능이 필요합니다. 일부 Azure AD DS 구성 요소에 정책이 적용되는 경우 Azure 플랫폼은 관리 작업을 수행하지 못할 수 있습니다.

Azure AD DS 구성 요소에서 적용된 정책을 확인하고 업데이트하려면 다음 단계를 완료하십시오.

1. 가상 네트워크, NIC 또는 공용 IP 주소와 같은 리소스 그룹의 각 Azure AD DS 네트워크 구성 요소에 대해 Azure 포털의 작업 로그를 확인합니다. 이러한 작업 로그는 작업이 실패한 이유와 제한정책이 적용되는 위치를 나타내야 합니다.
1. 정책이 적용되는 리소스를 선택한 다음 **정책**에서 정책을 선택하고 정책을 편집하여 덜 제한적입니다.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: 잠시 후에 동기화가 완료되지 않았습니다.

### <a name="alert-message"></a>경고 메시지

*관리되는 도메인이 [날짜]에 Azure AD와 마지막으로 동기화되었습니다. 사용자가 관리되는 도메인에서 로그인하지 못하거나 그룹 구성원이 Azure AD와 동기화되지 않을 수 있습니다.*

### <a name="resolution"></a>해결 방법

관리되는 도메인의 구성에 문제가 있음을 나타내는 경고가 있는지 [Azure AD DS 상태를 확인합니다.](check-health.md) 네트워크 구성 문제로 Azure AD의 동기화를 차단할 수 있습니다. 구성 문제를 나타내는 경고를 확인할 수 있는 경우 2시간 정도 기다린 후 다시 확인하여 동기화가 성공적으로 완료되었는지 확인합니다.

다음과 같은 일반적인 이유로 인해 Azure AD DS 관리 도메인에서 동기화가 중지됩니다.

* 필요한 네트워크 연결이 차단됩니다. Azure 가상 네트워크에서 문제 및 필요한 문제를 확인하는 방법에 대해 자세히 알아보려면 네트워크 보안 그룹 및 [Azure AD 도메인 서비스에 대한 네트워크 요구 사항을](network-considerations.md) [해결합니다.](alert-nsg.md)
*  Azure AD DS 관리 도메인이 배포될 때 암호 동기화가 설정되지 않았거나 성공적으로 완료되지 않았습니다. [온프레마에서](tutorial-configure-password-hash-sync.md)클라우드 전용 [사용자](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) 또는 하이브리드 사용자를 위한 암호 동기화를 설정할 수 있습니다.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: 잠시 후에 백업이 수행되지 않았습니다.

### <a name="alert-message"></a>경고 메시지

*관리되는 도메인은 마지막으로 [date]에 백업되었습니다.*

### <a name="resolution"></a>해결 방법

관리되는 도메인의 구성에 문제가 있음을 나타내는 경고는 [Azure AD DS 상태를 확인합니다.](check-health.md) 네트워크 구성 문제로 Azure 플랫폼이 백업을 성공적으로 처리하지 못하도록 차단할 수 있습니다. 구성 문제를 나타내는 경고를 확인할 수 있는 경우 2시간 정도 기다린 후 다시 확인하여 동기화가 성공적으로 완료되었는지 확인합니다.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: 비활성화된 구독으로 인한 일시 중단

### <a name="alert-message"></a>경고 메시지

*해당 도메인과 연결된 Azure 구독이 활성 상태가 아니기 때문에 관리되는 도메인은 일시 중단됩니다.*

### <a name="resolution"></a>해결 방법

> [!WARNING]
> Azure AD DS 관리 도메인이 장기간 일시 중단되면 삭제될 위험이 있습니다. 가능한 한 빨리 정지 이유를 해결합니다. 자세한 내용은 [Azure AD DS의 일시 중단 상태 이해하기를](suspension.md)참조하십시오.

Azure AD DS에는 활성 구독이 필요합니다. Azure AD DS 관리 도메인이 연결된 Azure 구독이 활성화되어 있지 않은 경우 구독을 다시 활성화하려면 해당 구독을 갱신해야 합니다.

1. [Azure 구독을 갱신합니다](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. 구독이 갱신되면 Azure AD DS 알림을 통해 관리되는 도메인을 다시 활성화할 수 있습니다.

관리되는 도메인을 다시 사용하도록 설정하면 Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: 잘못된 구성으로 인한 일시 중단

### <a name="alert-message"></a>경고 메시지

*잘못된 구성으로 인해 관리되는 도메인이 일시 중단됩니다. 서비스가 오랫동안 관리되는 도메인의 도메인 컨트롤러를 관리, 패치 또는 업데이트할 수 없습니다.*

### <a name="resolution"></a>해결 방법

> [!WARNING]
> Azure AD DS 관리 도메인이 장기간 일시 중단되면 삭제될 위험이 있습니다. 가능한 한 빨리 정지 이유를 해결합니다. 자세한 내용은 [Azure AD DS의 일시 중단 상태 이해하기를](suspension.md)참조하십시오.

관리되는 도메인의 구성에 문제가 있음을 나타내는 경고는 [Azure AD DS 상태를 확인합니다.](check-health.md) 구성 문제를 나타내는 경고를 해결할 수 있는 경우 2시간 정도 기다린 후 다시 확인하여 동기화가 완료되었는지 확인합니다. 준비가 되면 Azure AD DS 관리 도메인을 다시 사용하도록 사용하도록 Azure [지원 요청을 엽니다.][azure-support]

## <a name="next-steps"></a>다음 단계

그래도 문제가 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
