---
title: Azure AD 도메인 서비스에 대한 이메일 알림 | 마이크로소프트 문서'
description: Azure Active Directory 도메인 서비스 관리 도메인의 문제에 대해 경고하도록 전자 메일 알림을 구성하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 5507579338ad0d87bc6223b56283fe7ed46af7d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613290"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Azure Active Directory 도메인 서비스의 문제에 대한 전자 메일 알림 구성

Azure Active Directory 도메인 서비스(Azure AD DS) 관리 도메인의 상태는 Azure 플랫폼에서 모니터링됩니다. Azure 포털의 상태 상태 페이지에는 관리되는 도메인에 대한 경고가 표시됩니다. 문제가 적시에 응답되도록 하기 위해 Azure AD DS 관리 도메인에서 문제가 감지되는 즉시 상태 경고를 보고하도록 전자 메일 알림을 구성할 수 있습니다.

이 문서에서는 Azure AD DS 관리 도메인에 대한 전자 메일 알림 받는 사람을 구성하는 방법을 보여 주며 있습니다.

## <a name="email-notification-overview"></a>이메일 알림 개요

Azure AD DS 관리 도메인에 대한 문제를 경고하려면 전자 메일 알림을 구성할 수 있습니다. 이러한 전자 메일 알림은 경고가 있는 Azure AD DS 관리 도메인을 지정하고 Azure 포털의 상태 페이지에 대한 검색 시간과 링크를 제공합니다. 그런 다음 제공된 문제 해결 조언을 따라 문제를 해결할 수 있습니다.

다음 예제 전자 메일 알림은 Azure AD DS 관리 도메인에서 중요한 경고 또는 경고가 생성되었다는 것을 나타냅니다.

![이메일 알림 예제](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> 메시지의 링크를 클릭하기 전에 항상 전자 메일이 확인된 Microsoft 보낸 사람이 보낸 메일인지 확인합니다. 이메일 알림은 항상 `azure-noreply@microsoft.com` 주소에서 옵니다.

### <a name="why-would-i-receive-email-notifications"></a>이메일 알림을 수신하는 이유

Azure AD DS는 관리되는 도메인에 대한 중요한 업데이트에 대한 전자 메일 알림을 보냅니다. 이러한 알림은 서비스에 영향을 주는 긴급 한 문제에 대해서만 사용 되며 즉시 해결 해야 합니다. 각 전자 메일 알림은 Azure AD DS 관리 도메인의 경고에 의해 트리거됩니다. 경고는 Azure 포털에도 표시되며 [Azure AD DS 상태 페이지에서][check-health]볼 수 있습니다.

Azure AD DS는 광고, 업데이트 또는 판매 목적으로 전자 메일을 보내지 않습니다.

### <a name="when-will-i-receive-email-notifications"></a>이메일 알림을 수신하는 경우

Azure AD DS 관리 도메인에서 [새 경고가][troubleshoot-alerts] 발견되면 알림이 즉시 전송됩니다. 경고가 해결되지 않으면 4일마다 추가 이메일 알림이 미리 알림으로 전송됩니다.

### <a name="who-should-receive-the-email-notifications"></a>이메일 알림을 수신하는 대상

Azure AD DS에 대한 전자 메일 받는 사람 목록은 관리되는 도메인을 관리하고 변경할 수 있는 사용자로 구성되어야 합니다. 이 이메일 목록은 모든 경고 및 문제에 대한 "첫 번째 응답자"로 생각되어야 합니다.

이메일 알림을 위해 수신자를 최대 5명까지 추가할 수 있습니다. 이메일 알림에 대해 5명 이상의 수신자를 원할 경우 메일 그룹을 만들고 대신 알림 목록에 추가합니다.

Azure AD 디렉터리및 *AAD DC* *관리자* 그룹의 모든 구성원이 전자 메일 알림을 받도록 선택할 수도 있습니다. Azure AD DS는 글로벌 관리자 및 AAD DC 관리자 목록을 포함하여 최대 100개의 전자 메일 주소에만 알림을 보냅니다.

## <a name="configure-email-notifications"></a>전자 메일 알림 구성

기존 이메일 알림 수신자를 검토하거나 추가 받는 사람을 추가하려면 다음 단계를 완료합니다.

1. Azure 포털에서 **Azure AD 도메인 서비스를**검색하고 선택합니다.
1. *aaddscontoso.com*와 같은 Azure AD DS 관리 도메인을 선택합니다.
1. Azure AD DS 리소스 창의 왼쪽에서 **알림 설정을**선택합니다. 전자 메일 알림에 대한 기존 받는 사람이 표시됩니다.
1. 전자 메일 수신자를 추가하려면 추가 받는 사람 테이블에 전자 메일 주소를 입력합니다.
1. 완료되면 맨 위 탐색에서 **저장을** 선택합니다.

> [!WARNING]
> 알림 설정을 변경하면 사용자뿐만 아니라 전체 Azure AD DS 관리 도메인에 대한 알림 설정이 업데이트됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>경고 이메일 알림을 받았지만 Azure Portal에 로그온하면 경고가 없습니다. 어떻게 된 건가요?

경고가 해결되면 Azure 포털에서 경고가 지워집니다. 가장 가능성이 높은 이유는 전자 메일 알림을 받는 다른 사용자가 Azure AD DS 관리 도메인에서 경고를 해결했거나 Azure 플랫폼에서 자동으로 해결되었기 때문입니다.

### <a name="why-can-i-not-edit-the-notification-settings"></a>알림 설정을 편집할 수 없는 이유

Azure 포털의 알림 설정 페이지에 액세스할 수 없는 경우 Azure AD DS 관리 도메인을 편집할 수 있는 권한이 없습니다. Azure AD DS 리소스를 편집할 수 있는 권한을 얻거나 받는 사람 목록에서 제거하려면 글로벌 관리자에게 문의해야 합니다.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>내 이메일 주소를 제공했지만 이메일 알림이 수신되지 않는 것 같습니다. 그 이유는

이메일에서 스팸 또는 정크 폴더에 알림이 있는지 확인하고 을 `azure-noreply@microsoft.com`보낸 사람에게 허용해야 합니다.

## <a name="next-steps"></a>다음 단계

보고될 수 있는 일부 문제 해결에 대한 자세한 내용은 [Azure AD DS 관리 도메인의 경고 해결을][troubleshoot-alerts]참조하십시오.

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
