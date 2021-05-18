---
title: Azure AD Domain Services의 일반적 경고 및 해결 방법 | Microsoft Docs
description: Azure Active Directory Domain Services 상태의 일부로 생성된 일반적인 경고를 해결하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 4caf804a274956556d6e9ca396c8f08594b11a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092882"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>알려진 문제: Azure Active Directory Domain Services의 일반적 경고 및 해결 방법

애플리케이션 ID 및 인증의 중심적 부분인 Azure Active Directory Domain Services(Azure AD DS)에 문제가 있는 경우가 있습니다. 문제가 발생하는 경우 몇 가지 일반적 경고와 다시 작동하도록 하는 데 도움이 되는 관련 문제 해결 단계가 있습니다. 언제든 추가 문제 해결 지원을 위해 [Azure 지원 요청을 열][azure-support] 수도 있습니다.

이 문서에서는 Azure AD DS의 일반적 경고에 대한 문제 해결 정보를 제공합니다.

## <a name="aadds100-missing-directory"></a>AADDS100: 누락된 디렉터리

### <a name="alert-message"></a>경고 메시지

관리되는 도메인과 연결된 Azure AD 디렉터리가 삭제되었을 수 있습니다. 관리되는 도메인이 더 이상 지원되는 구성에 없습니다. Microsoft는 관리되는 도메인을 모니터링, 관리, 패치 및 동기화할 수 없습니다.

### <a name="resolution"></a>해결 방법

이 오류는 일반적으로 Azure 구독을 새 Azure AD 디렉터리로 이동하고 Azure AD DS와 연결된 이전 Azure AD 디렉터리를 삭제한 경우에 발생합니다.

이 오류는 복구할 수 없습니다. 이 경고를 해결하려면 [기존의 관리되는 도메인을 삭제](delete-aadds.md)하고 새 디렉터리에 다시 만듭니다. 관리되는 도메인을 삭제하는 데 문제가 있으면 추가 문제 해결 지원을 위해 [Azure 지원 요청을 여세요][azure-support].

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C가 이 디렉터리에서 실행 중임

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services를 Azure AD B2B 디렉터리에서 사용 가능하게 설정할 수 없습니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS는 Azure AD 디렉터리와 자동으로 동기화됩니다. Azure AD 디렉터리가 B2C용으로 구성된 경우 Azure AD DS를 배포 및 동기화할 수 없습니다.

Azure AD DS를 사용하려면 다음 단계를 사용하여 Azure AD B2C가 아닌 디렉터리에서 관리되는 도메인을 다시 만들어야 합니다.

1. 기존 Azure AD 디렉터리에서 [관리되는 도메인을 삭제](delete-aadds.md)합니다.
1. Azure AD B2C 디렉터리가 아닌 새 디렉터리를 만듭니다.
1. [관리되는 대체 도메인을 만듭니다](tutorial-create-instance.md).

관리되는 도메인의 상태는 2시간 내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: 주소가 공용 IP 범위에 있음

### <a name="alert-message"></a>경고 메시지

Azure AD Domain Services를 사용하도록 설정한 가상 네트워크의 IP 주소 범위가 공용 IP 범위에 있습니다. Azure AD Domain Services는 개인 IP 주소 범위가 있는 가상 네트워크에 사용하도록 설정해야 합니다. 이 구성은 Microsoft가 관리되는 도메인을 모니터링, 관리, 패치 및 동기화할 수 있는 기능에 영향을 줍니다.

### <a name="resolution"></a>해결 방법

시작하기 전에 [개인 IP v4 주소 공간](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)을 이해해야 합니다.

가상 네트워크 내에서 VM은 서브넷에 구성된 것과 동일한 IP 주소 범위에 있는 Azure 리소스에 요청을 수행할 수 있습니다. 서브넷에 공용 IP 주소 범위를 구성하는 경우 가상 네트워크 내에서 라우팅되는 요청이 의도한 웹 리소스에 도달하지 못할 수 있습니다. 이 구성으로 인해 Azure AD DS에서 예측할 수 없는 오류가 발생할 수 있습니다.

> [!NOTE]
> 가상 네트워크에서 구성된 인터넷의 IP 주소 범위가 있는 경우 이 경고는 무시될 수 있습니다. 그러나 Azure AD Domain Services는 예측할 수 없는 오류를 일으킬 수 있으므로 이 구성을 사용하여 [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)에 커밋할 수 없습니다.

이 경고를 해결하려면 기존의 관리되는 도메인을 삭제하고 개인 IP 주소 범위가 있는 가상 네트워크에 다시 만들어야 합니다. 관리되는 도메인을 사용할 수 없고 OU 또는 서비스 계정 등 만든 사용자 지정 리소스가 손실되므로 이 프로세스는 작업을 중단시킵니다.

1. 디렉터리에서 [관리되는 도메인을 삭제](delete-aadds.md)합니다.
1. 가상 네트워크 IP 주소 범위를 업데이트하려면 Azure Portal에서 가상 네트워크를 검색하고 선택합니다. 공용 IP 주소 범위가 잘못 설정되어 있는 Azure AD DS의 가상 네트워크를 선택합니다.
1. **설정** 에서 주소 공간을 선택합니다.
1. 기존 주소 범위를 선택하여 편집하거나 다른 주소 범위를 추가하여 주소 범위를 업데이트합니다. 새 주소 범위가 개인 IP 범위에 있어야 합니다. 준비가 되면 변경 내용을 **저장** 합니다.
1. 왼쪽 탐색 창에서 **서브넷** 을 선택합니다.
1. 편집하려는 서브넷을 선택하거나 추가 서브넷을 만듭니다.
1. 개인 IP 주소 범위를 업데이트하거나 지정한 다음 변경 내용을 **저장** 합니다.
1. [관리되는 대체 도메인을 만듭니다](tutorial-create-instance.md). 개인 IP 주소 범위로 업데이트된 가상 네트워크 서브넷을 선택했는지 확인합니다.

관리되는 도메인의 상태는 2시간 내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Azure 구독을 찾을 수 없음

### <a name="alert-message"></a>경고 메시지

관리되는 도메인과 연결된 Azure 구독이 삭제되었습니다. Azure AD Domain Services가 계속 제대로 작동하려면 활성 구독이 필요합니다.

### <a name="resolution"></a>해결 방법

Azure AD DS에는 활성 구독이 필요하며 다른 구독으로 이동할 수 없습니다. 관리되는 도메인이 연결된 Azure 구독이 삭제되는 경우 Azure 구독 및 관리되는 도메인을 다시 만들어야 합니다.

1. [Azure 구독을 만듭니다](../cost-management-billing/manage/create-subscription.md).
1. 기존 Azure AD 디렉터리에서 [관리되는 도메인을 삭제](delete-aadds.md)합니다.
1. [관리되는 대체 도메인을 만듭니다](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Azure 구독이 비활성화됨

### <a name="alert-message"></a>경고 메시지

관리되는 도메인과 연결된 Azure 구독이 활성 상태가 아닙니다. Azure AD Domain Services가 계속 제대로 작동하려면 활성 구독이 필요합니다.

### <a name="resolution"></a>해결 방법

Azure AD DS에는 활성 구독이 필요합니다. 관리되는 도메인이 연결된 Azure 구독이 활성 상태가 아닌 경우 갱신하여 구독을 다시 활성화해야 합니다.

1. [Azure 구독을 갱신합니다](../cost-management-billing/manage/subscription-disabled.md).
2. 구독이 갱신되면 Azure AD DS 알림을 통해 관리되는 도메인을 다시 사용하도록 설정할 수 있습니다.

관리되는 도메인을 다시 사용하도록 설정하면 관리되는 도메인의 상태가 2시간 내에 자동으로 업데이트되고 경고가 제거됩니다.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: 구독 이동 디렉터리

### <a name="alert-message"></a>경고 메시지

Azure AD Domain Services에서 사용되는 구독이 다른 디렉터리로 이동되었습니다. Azure AD Domain Services가 제대로 작동하려면 활성 구독이 같은 디렉터리에 있어야 합니다.

### <a name="resolution"></a>해결 방법

Azure AD DS에는 활성 구독이 필요하며 다른 구독으로 이동할 수 없습니다. 관리되는 도메인이 연결된 Azure 구독이 이동된 경우 구독을 이전 디렉터리로 다시 이동하거나 기존 디렉터리에서 [관리되는 도메인을 삭제](delete-aadds.md)하고 [선택한 구독에 관리되는 대체 도메인을 만듭니다](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: 관리되는 도메인에 대한 리소스를 찾을 수 없음

### <a name="alert-message"></a>경고 메시지

관리되는 도메인에 사용되는 리소스가 삭제되었습니다. Azure AD Domain Services가 제대로 작동하려면 이 리소스가 필요합니다.

### <a name="resolution"></a>해결 방법

Azure AD DS는 제대로 작동하기 위해 공용 IP 주소, 가상 네트워크 인터페이스, 부하 분산 장치와 같은 추가 리소스를 만듭니다. 이러한 리소스 중 어느 하나라도 삭제되면 관리되는 도메인이 지원되지 않는 상태가 되며 도메인을 관리할 수 없습니다. 이러한 리소스에 대한 자세한 내용은 [Azure AD DS에서 사용되는 네트워크 리소스](network-considerations.md#network-resources-used-by-azure-ad-ds)를 참조하세요.

이 경고는 이러한 필수 리소스 중 하나가 삭제될 때 생성됩니다. 리소스가 4시간 이내에 삭제된 경우 삭제된 리소스를 Azure 플랫폼이 자동으로 다시 만들 수 있습니다. 다음 단계에서는 리소스 삭제의 상태 및 타임스탬프를 확인하는 방법을 간략하게 설명합니다.

1. Azure Portal에서 **Domain Service** 를 검색하여 선택합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽 탐색 창에서 **상태** 를 선택합니다.
1. 상태 창에서 ID가 *AADDS109* 인 경고를 선택합니다.
1. 이 경고에는 처음 발견된 시간의 타임스탬프가 있습니다. 4시간이 지나지 않은 타임스탬프인 경우 Azure 플랫폼이 자동으로 리소스를 다시 만들고 경고를 자체적으로 해결할 수도 있습니다.

    경고가 4시간을 초과하면 관리되는 도메인은 복구할 수 없는 상태입니다. [관리되는 도메인을 삭제](delete-aadds.md)한 다음 [관리되는 대체 도메인을 만듭니다](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: 관리되는 도메인에 연결된 서브넷이 꽉 찼음

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services의 배포에 선택된 서브넷이 꽉 차서 만들어야 하는 추가 도메인 컨트롤러에 사용할 수 있는 공간이 없습니다.*

### <a name="resolution"></a>해결 방법

Azure AD DS의 가상 네트워크 서브넷에는 자동으로 생성된 리소스에 충분한 IP 주소가 필요합니다. 유지 관리 이벤트가 있는 경우 이 IP 주소 공간에는 대체 리소스를 만들 필요성이 포함됩니다. 사용 가능한 IP 주소가 부족해질 위험을 최소화하려면 관리되는 도메인과 동일한 가상 네트워크 서브넷에 자체 VM 같은 추가 리소스를 배포하지 마세요.

이 오류는 복구할 수 없습니다. 이 경고를 해결하려면 [기존의 관리되는 도메인을 삭제](delete-aadds.md)하고 다시 만듭니다. 관리되는 도메인을 삭제하는 데 문제가 있으면 추가 문제 해결 지원을 위해 [Azure 지원 요청을 여세요][azure-support].

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: 권한 없는 서비스 주체

### <a name="alert-message"></a>경고 메시지

Azure AD Domain Services에서 도메인을 제공하는 데 사용하는 서비스 주체에 Azure 구독의 리소스를 관리할 수 있는 권한이 없습니다. 서비스 주체는 관리되는 도메인을 제공하기 위한 권한을 얻어야 합니다.

### <a name="resolution"></a>해결 방법

자동으로 생성된 일부 서비스 주체는 관리되는 도메인의 리소스를 관리하고 만드는 데 사용됩니다. 이러한 서비스 주체 중 하나의 액세스 권한이 변경되면 도메인이 리소스를 올바르게 관리할 수 없습니다. 다음 단계에서는 액세스 권한을 이해한 다음 서비스 주체에게 부여하는 방법을 보여 줍니다.

1. [Azure 역할 기반 액세스 제어 및 Azure Portal에서 애플리케이션에 대한 액세스 권한을 부여하는 방법](../role-based-access-control/role-assignments-portal.md)에 대해 알아보세요.
2. ID가 *abba844e-bc0e-44b0-947a-dc74e5d09022* 인 서비스 주체가 갖는 액세스 권한을 검토하고 이전에 거부된 액세스 권한을 부여합니다.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: 관리되는 도메인에 IP 주소가 충분하지 않음

### <a name="alert-message"></a>경고 메시지

이 도메인의 가상 네트워크 서브넷에 IP 주소가 부족한 것 같습니다. Azure AD Domain Services는 서브넷 내에서 사용하도록 설정된 2개 이상의 사용 가능한 IP 주소가 필요합니다. 서브넷 내에 3~5개의 여유 IP 주소를 가지고 있는 것이 좋습니다. 다른 가상 머신이 서브넷 내에 배포되어 사용 가능한 IP 주소 수가 모두 소진되거나, 서브넷에 사용 가능한 IP 주소 수에 제한이 있는 경우 이 문제가 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

Azure AD DS의 가상 네트워크 서브넷에는 자동으로 생성된 리소스에 충분한 IP 주소가 필요합니다. 유지 관리 이벤트가 있는 경우 이 IP 주소 공간에는 대체 리소스를 만들 필요성이 포함됩니다. 사용 가능한 IP 주소가 부족해질 위험을 최소화하려면 관리되는 도메인과 동일한 가상 네트워크 서브넷에 자체 VM 같은 추가 리소스를 배포하지 마세요.

이 경고를 해결하려면 기존의 관리되는 도메인을 삭제하고 IP 주소 범위가 충분히 큰 가상 네트워크에 다시 만들어야 합니다. 관리되는 도메인을 사용할 수 없고 OU 또는 서비스 계정 등 만든 사용자 지정 리소스가 손실되므로 이 프로세스는 작업을 중단시킵니다.

1. 디렉터리에서 [관리되는 도메인을 삭제](delete-aadds.md)합니다.
1. 가상 네트워크 IP 주소 범위를 업데이트하려면 Azure Portal에서 가상 네트워크를 검색하고 선택합니다. IP 주소 범위가 작은 관리되는 도메인의 가상 네트워크를 선택합니다.
1. **설정** 에서 주소 공간을 선택합니다.
1. 기존 주소 범위를 선택하여 편집하거나 다른 주소 범위를 추가하여 주소 범위를 업데이트합니다. 새 IP 주소 범위가 관리되는 도메인의 서브넷 범위에 충분히 큰지 확인합니다. 준비가 되면 변경 내용을 **저장** 합니다.
1. 왼쪽 탐색 창에서 **서브넷** 을 선택합니다.
1. 편집하려는 서브넷을 선택하거나 추가 서브넷을 만듭니다.
1. 충분히 큰 IP 주소 범위를 업데이트하거나 지정한 다음 변경 내용을 **저장** 합니다.
1. [관리되는 대체 도메인을 만듭니다](tutorial-create-instance.md). 충분히 큰 IP 주소 범위로 업데이트된 가상 네트워크 서브넷을 선택했는지 확인합니다.

관리되는 도메인의 상태는 2시간 내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: 리소스를 복구할 수 없음

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services에서 사용되는 리소스가 예기치 않은 상태로 발견되었으며 복구할 수 없습니다.*

### <a name="resolution"></a>해결 방법

이 오류는 복구할 수 없습니다. 이 경고를 해결하려면 [기존의 관리되는 도메인을 삭제](delete-aadds.md)하고 다시 만듭니다. 관리되는 도메인을 삭제하는 데 문제가 있으면 추가 문제 해결 지원을 위해 [Azure 지원 요청을 여세요][azure-support].

## <a name="aadds114-subnet-invalid"></a>AADDS114: 잘못된 서브넷

### <a name="alert-message"></a>경고 메시지

*Azure AD Domain Services의 배포를 위해 선택한 서브넷이 유효하지 않아 사용할 수 없습니다.*

### <a name="resolution"></a>해결 방법

이 오류는 복구할 수 없습니다. 이 경고를 해결하려면 [기존의 관리되는 도메인을 삭제](delete-aadds.md)하고 다시 만듭니다. 관리되는 도메인을 삭제하는 데 문제가 있으면 추가 문제 해결 지원을 위해 [Azure 지원 요청을 여세요][azure-support].

## <a name="aadds115-resources-are-locked"></a>AADDS115: 리소스가 잠김

### <a name="alert-message"></a>경고 메시지

*대상 범위가 잠겨 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.*

### <a name="resolution"></a>해결 방법

변경 또는 삭제를 방지하기 위해 Azure 리소스에 리소스 잠금이 적용될 수 있습니다. Azure AD DS는 관리되는 서비스이므로 Azure 플랫폼에는 구성 변경을 수행하는 기능이 필요합니다. 리소스 잠금이 일부 Azure AD DS 구성 요소에 적용되면 Azure 플랫폼은 해당 관리 작업을 수행할 수 없습니다.

Azure AD DS 구성 요소에 대한 리소스 잠금을 확인하고 제거하려면 다음 단계를 완료합니다.

1. 리소스 그룹에서 가상 네트워크, 네트워크 인터페이스, 공용 IP 주소와 같은 관리되는 도메인의 네트워크 구성 요소마다 Azure Portal에서 작업 로그를 확인합니다. 이러한 작업 로그에는 작업이 실패하는 이유와 리소스 잠금이 적용되는 위치가 표시됩니다.
1. 잠금이 적용되는 리소스를 선택한 다음 **잠금** 에서 잠금을 선택하고 제거합니다.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: 리소스를 사용할 수 없음

### <a name="alert-message"></a>경고 메시지

*정책 제한으로 인해 관리되는 도메인에 사용되는 하나 이상의 네트워크 리소스를 사용할 수 없습니다.*

### <a name="resolution"></a>해결 방법

Azure 리소스 및 리소스 그룹에는 허용되는 구성 작업을 제어하는 정책이 적용됩니다. Azure AD DS는 관리되는 서비스이므로 Azure 플랫폼에는 구성 변경을 수행하는 기능이 필요합니다. 정책이 일부 Azure AD DS 구성 요소에 적용되면 Azure 플랫폼은 해당 관리 작업을 수행할 수 없습니다.

Azure AD DS 구성 요소에서 적용된 정책을 확인하고 업데이트하려면 다음 단계를 완료합니다.

1. 리소스 그룹에서 가상 네트워크, NIC 또는 공용 IP 주소와 같은 관리되는 도메인의 네트워크 구성 요소마다 Azure Portal에서 작업 로그를 확인합니다. 이러한 작업 로그에는 작업이 실패하는 이유와 제한적 정책이 적용되는 위치가 표시됩니다.
1. 정책이 적용되는 리소스를 선택한 다음 **정책** 에서 정책을 선택하고 덜 제한적이도록 편집하세요.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: 잠시 후에 동기화가 완료되지 않았습니다.

### <a name="alert-message"></a>경고 메시지

관리되는 도메인이 [날짜]에 Azure AD와 마지막으로 동기화되었습니다. 사용자가 관리되는 도메인에서 로그인할 수 없거나 그룹 멤버 자격이 Azure AD와 동기화되지 않을 수 있습니다.

### <a name="resolution"></a>해결 방법

관리되는 도메인의 구성 문제를 나타내는 경고가 있는지 [Azure AD DS 상태를 확인](check-health.md)합니다. 네트워크 구성 문제로 인해 Azure AD에서의 동기화가 차단될 수 있습니다. 구성 문제를 나타내는 경고를 해결할 수 없는 경우 2시간 동안 기다린 다음 동기화가 성공적으로 완료되었는지 다시 확인합니다.

관리되는 도메인에서 동기화가 중지되는 일반적인 이유는 다음과 같습니다.

* 필요한 네트워크 연결이 차단되었습니다. Azure 가상 네트워크에 문제가 있는지 확인하고 필요한 사항을 확인하는 방법에 대한 자세한 내용은 [네트워크 보안 그룹 문제 해결](alert-nsg.md) 및 [Azure AD DS의 네트워크 요구 사항](network-considerations.md)을 참조하세요.
*  관리되는 도메인이 배포될 때 암호 동기화가 설정되지 않았거나 성공적으로 완료되지 않았습니다. [클라우드 전용 사용자](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) 또는 [온-프레미스에서 하이브리드 사용자](tutorial-configure-password-hash-sync.md)의 암호 동기화를 설정할 수 있습니다.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: 잠시 후에 백업이 수행되지 않았습니다.

### <a name="alert-message"></a>경고 메시지

*관리되는 도메인은 마지막으로 [date]에 백업되었습니다.*

### <a name="resolution"></a>해결 방법

관리되는 도메인의 구성 문제를 나타내는 경고가 있는지 [Azure AD DS 상태를 확인](check-health.md)합니다. 네트워크 구성 문제는 Azure 플랫폼이 백업을 수행하지 못하도록 차단할 수 있습니다. 구성 문제를 나타내는 경고를 해결할 수 없는 경우 2시간 동안 기다린 다음 동기화가 성공적으로 완료되었는지 다시 확인합니다.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: 비활성화된 구독으로 인한 일시 중단

### <a name="alert-message"></a>경고 메시지

*해당 도메인과 연결된 Azure 구독이 활성 상태가 아니기 때문에 관리되는 도메인은 일시 중단됩니다.*

### <a name="resolution"></a>해결 방법

> [!WARNING]
> 관리되는 도메인이 장시간 일시 중단되는 경우 삭제될 위험이 있습니다. 가능한 한 빨리 일시 중단의 원인을 해결하세요. 자세한 내용은 [Azure AD DS의 일시 중단된 상태 이해](suspension.md)를 참조하세요.

Azure AD DS에는 활성 구독이 필요합니다. 관리되는 도메인이 연결된 Azure 구독이 활성 상태가 아닌 경우 갱신하여 구독을 다시 활성화해야 합니다.

1. [Azure 구독을 갱신합니다](../cost-management-billing/manage/subscription-disabled.md).
2. 구독이 갱신되면 Azure AD DS 알림을 통해 관리되는 도메인을 다시 사용하도록 설정할 수 있습니다.

관리되는 도메인을 다시 사용하도록 설정하면 관리되는 도메인의 상태가 2시간 내에 자동으로 업데이트되고 경고가 제거됩니다.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: 잘못된 구성으로 인한 일시 중단

### <a name="alert-message"></a>경고 메시지

잘못된 구성으로 인해 관리되는 도메인이 일시 중단되었습니다. 오랜 시간 동안 서비스가 관리되는 도메인의 도메인 컨트롤러를 관리, 패치 또는 업데이트할 수 없었습니다.

### <a name="resolution"></a>해결 방법

> [!WARNING]
> 관리되는 도메인이 장시간 일시 중단되는 경우 삭제될 위험이 있습니다. 가능한 한 빨리 일시 중단의 원인을 해결하세요. 자세한 내용은 [Azure AD DS의 일시 중단된 상태 이해](suspension.md)를 참조하세요.

관리되는 도메인의 구성 문제를 나타내는 경고가 있는지 [Azure AD DS 상태를 확인](check-health.md)합니다. 구성 문제를 나타내는 경고를 해결할 수 없는 경우 2시간 동안 기다린 다음 동기화가 완료되었는지 다시 확인합니다. 준비가 되면 [Azure 지원 요청을 열어][azure-support] 관리되는 도메인을 다시 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

문제가 여전히 발생하는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다][azure-support].

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md