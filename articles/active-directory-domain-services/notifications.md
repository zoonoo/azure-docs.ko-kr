---
title: Azure AD Domain Services 이메일 알림 | Microsoft Docs
description: Azure Active Directory Domain Services 관리되는 도메인의 문제를 경고하도록 이메일 알림을 구성하는 방법에 대해 알아봅니다
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4242092af86f8bc06ef9c9eba068ce07725de01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619167"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 문제에 대한 이메일 알림 구성

Azure 플랫폼에서 Azure Active Directory Domain Services(Azure AD DS) 관리형 도메인의 상태를 모니터링합니다. Azure Portal의 상태 페이지에는 관리형 도메인에 대한 경고가 표시됩니다. 문제에 대한 응답이 적시에 이루어지는지 확인하기 위해 Azure AD DS 관리형 도메인에서 검색되는 즉시 상태 경고를 보고하도록 메일 알림을 구성할 수 있습니다.

이 문서에서는 관리되는 도메인에 대한 이메일 알림을 받는 사람을 구성하는 방법을 보여줍니다.

## <a name="email-notification-overview"></a>메일 알림 개요

관리형 도메인에 대한 문제를 경고하려면 메일 알림을 구성할 수 있습니다. 이메일 알림을 통해 경고가 발생한 관리되는 도메인을 지정하며, 감지 시간과 링크를 Azure Portal의 상태 페이지에 제공합니다. 그런 다음 제공된 문제 해결 조언을 따라 문제를 해결할 수 있습니다.

다음 메일 알림 예시는 관리형 도메인에 대한 위험 경고 또는 경고가 생성되었음을 보여줍니다.

![이메일 알림 예제](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> 메시지의 링크를 클릭하기 전에 항상 확인된 Microsoft 발신자의 메일을 받았는지 확인합니다. 메일 알림은 항상 `azure-noreply@microsoft.com` 주소에서 제공됩니다.

### <a name="why-would-i-receive-email-notifications"></a>이메일 알림을 수신하는 이유

Azure AD DS는 관리형 도메인에 대한 중요 업데이트에 대한 메일 알림을 보냅니다. 이러한 알림은 서비스에 영향을 주는 긴급한 문제에 대해서만 제공되므로 즉시 해결해야 합니다. 각 메일 알림은 관리형 도메인의 경고에 의해 트리거됩니다. 이러한 경고는 Azure Portal에도 표시되며 [Azure AD DS 상태 페이지][check-health]에서 볼 수 있습니다.

Azure AD DS는 광고, 업데이트 또는 판매 용도로 메일을 보내지 않습니다.

### <a name="when-will-i-receive-email-notifications"></a>이메일 알림을 수신하는 경우

관리형 도메인에서 [새 경고][troubleshoot-alerts]가 발견되면 즉시 알림이 전송됩니다. 경고가 해결되지 않으면 4일마다 추가 메일 알림이 미리 알림으로 전송됩니다.

### <a name="who-should-receive-the-email-notifications"></a>이메일 알림을 수신하는 대상

Azure AD DS에 대한 메일 수신자 목록은 관리형 도메인을 관리하고 변경할 수 있는 사용자로 구성되어야 합니다. 이 메일 목록을 경고 및 문제에 대한 "첫 번째 응답자"로 간주합니다.

이메일 알림을 받을 수 있는 이메일을 최대 5개까지 더 추가할 수 있습니다. 이메일 알림을 받는 사람이 5명 이상이면, 메일 그룹을 생성하고 이를 알림 목록에 추가합니다.

Azure AD 디렉터리의 모든 *전역 관리자* 와 *AAD DC 관리자* 그룹의 모든 구성원에게 메일 알림을 받도록 선택할 수도 있습니다. Azure AD DS는 전역 관리자 및 AAD DC 관리자 목록을 포함하여 최대 100개의 이메일 주소로만 알림을 보냅니다.

## <a name="configure-email-notifications"></a>전자 메일 알림 구성

기존 메일 알림 수신자를 검토하거나 수신자를 더 추가하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다.
1. 관리형 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. Azure AD DS 리소스 창의 왼쪽에서 **알림 설정** 을 선택합니다. 기존에 메일 알림을 수신한 사람이 표시됩니다.
1. 메일을 추가하려면 추가 수신자 테이블에 메일 주소를 입력합니다.
1. 완료되면 상단 탐색에서 **저장** 을 선택합니다.

> [!WARNING]
> 알림 설정을 변경하는 경우, 사용자 본인만이 아닌 관리되는 도메인의 전체 알림 설정이 업데이트됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>경고 이메일 알림을 받았지만 Azure Portal에 로그온하면 경고가 없습니다. 무슨 일이 일어났나요?

경고가 해결되면 Azure Portal에서 경고가 지워집니다. 이메일 알림을 받는 사용자가 관리되는 도메인에 대한 경고를 해결하거나 Azure 플랫폼에서 경고를 자동 해결하는 경우가 가장 많습니다.

### <a name="why-can-i-not-edit-the-notification-settings"></a>알림 설정을 편집할 수 없는 이유

Azure Portal의 알림 설정 페이지에 액세스할 수 없는 경우 관리형 도메인을 편집할 권한이 없는 것입니다. 전역 관리자에게 문의하여 Azure AD DS 리소스를 편집하거나 수신자 목록에서 제거할 수 있는 권한을 얻으세요.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>내 이메일 주소를 제공했지만 이메일 알림이 수신되지 않는 것 같습니다. 그 이유는

이메일의 스팸 또는 정크 폴더에서 알림이 있는지 확인하고 `azure-noreply@microsoft.com`을 보낸 사람을 허용해야 합니다.

## <a name="next-steps"></a>다음 단계

보고될 수 있는 일부 문제를 해결하는 방법에 대한 자세한 내용은 [관리되는 도메인 경고 해결][troubleshoot-alerts]을 참조하세요.

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
