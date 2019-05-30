---
title: 'Azure Active Directory Domain Services: 알림 설정 | Microsoft Docs'
description: Azure AD Domain Services의 알림 설정
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: fab680e8e26584e6beeaf4bdb205c721e1b0b91e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246961"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Azure AD Domain Services의 알림 설정

관리형 도메인에서 상태 경고가 검색되는 즉시 Azure AD Domain Services의 알림을 업데이트할 수 있습니다.  

이 기능은 클래식 가상 네트워크에 없는 관리형 도메인에서만 사용할 수 있습니다.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Azure AD Domain Services 이메일 알림 설정을 확인하는 방법

1. Azure Portal에서 [Azure AD Domain Services 페이지](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)로 이동합니다.
2. 테이블에서 관리형 도메인 선택
3. 왼쪽 탐색에서 **알림 설정**을 선택합니다.

페이지에 Azure AD Domain Services 관련 이메일 알림의 모든 이메일 받는 사람이 표시됩니다.

## <a name="what-does-an-email-notification-look-like"></a>표시되는 이메일 알림 모습

다음 그림은 이메일 알림 예제입니다.

![이메일 알림 예제](./media/active-directory-domain-services-alerts/email-alert.png)

이메일은 감지 시간과 Azure Portal의 Azure AD Domain Services 상태 페이지 링크를 제공할 뿐만 아니라 경고가 발생한 관리형 도메인을 지정합니다.

> [!WARNING]
> 항상 이메일의 링크를 클릭하기 전에 확인된 Microsoft 보낸 사람이 보낸 이메일인지 확인합니다. 이메일은 항상 이메일 azure-noreply@microsoft.com에서 제공됩니다.


## <a name="why-would-i-receive-email-notifications"></a>이메일 알림을 수신하는 이유

Azure AD Domain Services는 도메인 관련 중요 업데이트에 대한 이메일 알림을 보냅니다.  이러한 알림은 서비스에 영향을 주는 긴급한 문제에 대해서만 제공되므로 즉시 해결해야 합니다. 각 이메일 알림은 관리형 도메인의 경고에 의해 트리거됩니다. 이러한 경고는 Azure Portal에도 표시되며 [Azure AD Domain Services 상태 페이지](check-health.md)에서 볼 수 있습니다.

Azure AD Domain Services는 보급, 업데이트 또는 판매 목적으로는 이 목록으로 이메일을 보내지 않습니다.

## <a name="when-will-i-receive-email-notifications"></a>이메일 알림을 수신하는 경우

관리형 도메인에서 [새 경고](troubleshoot-alerts.md)가 감지되면 알림이 즉시 전송됩니다. 경고가 해결되지 않으면 4일 간격으로 이메일 미리 알림이 전송됩니다.

## <a name="who-should-receive-the-email-notifications"></a>이메일 알림을 수신하는 대상


 Azure AD Domain Services에 대한 이메일 받는 사람 목록을 관리형 도메인을 관리하고 변경할 수 있는 사람으로 구성하는 것이 좋습니다. 이 이메일 목록을 발견된 문제의 "첫 번째 응답자"로 간주할 수 있습니다. 추가하려는 이메일이 5개가 넘는 경우, 대신 알림 목록에 추가할 배포 목록을 만드는 것이 좋습니다.

Azure AD Domain Services에 대한 알림 이메일을 5개까지 추가할 수 있습니다. 또한 디렉터리의 모든 전역 관리자와 그룹 ‘AAD DC 관리자’의 모든 멤버가 Azure AD Domain Services 이메일 알림을 받도록 선택할 수도 있습니다. Azure AD Domain Services는 전역 관리자 및 AAD DC 관리자 목록을 포함하여 최대 100개의 이메일 주소로만 알림을 보냅니다.


## <a name="how-to-add-an-additional-email-recipient"></a>메일 받는 사람을 더 추가하는 방법

> [!WARNING]
> 알림 설정을 변경하는 경우 사용자 본인만이 아닌 관리형 도메인의 전체 알림 설정이 변경됩니다.

1. Azure Portal에서 [Azure AD Domain Services 페이지](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)로 이동합니다.
2. 관리형 도메인을 클릭합니다.
3. 왼쪽 탐색에서 **알림 설정**을 클릭합니다.
4. 이메일을 추가하려면 추가 받는 사람 테이블에 이메일 주소를 입력합니다.
5. 위 탐색 모음에서 “저장”을 클릭합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>경고 이메일 알림을 받았지만 Azure Portal에 로그온하면 경고가 없습니다. 어떻게 된 건가요?

경고가 해결되면 Azure Portal에서 경고가 사라집니다. 가장 가능성이 높은 원인은 이메일 알림을 받은 누군가가 관리형 도메인에서 경고를 해결했거나 Azure AD Domain Services에서 자동으로 해결된 것입니다.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>알림 설정을 편집할 수 없는 이유

Azure Portal의 알림 설정 페이지에 액세스할 수 없는 경우 Azure AD Domain Services를 편집할 권한이 없는 것입니다. 전역 관리자에게 Azure AD Domain Services 리소스를 편집하기 위한 권한을 얻거나 받는 사람 목록에서 제거할 것을 요청해야 합니다.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>내 이메일 주소를 제공했지만 이메일 알림이 수신되지 않는 것 같습니다. 그 이유는

이메일의 스팸 또는 정크 폴더에서 알림이 있는지 확인하고 보낸 사람을 허용 목록에 추가해야 합니다(azure-noreply@microsoft.com).

## <a name="next-steps"></a>다음 단계
- [관리되는 도메인에 대한 경고 해결](troubleshoot-alerts.md)
- [Azure AD Domain Services에 대해 자세히 알아보기](overview.md)
- [제품팀에 문의](contact-us.md)

## <a name="contact-us"></a>문의처
[지원이 필요하거나 피드백을 공유하려면](contact-us.md)Azure Active Directory Domain Services 제품 팀에 문의하세요.
