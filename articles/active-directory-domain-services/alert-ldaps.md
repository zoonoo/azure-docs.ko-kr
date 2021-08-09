---
title: Azure AD Domain Services에서 보안 LDAP 경고 해결 | Microsoft Docs
description: Azure Active Directory Domain Services에서 보안 LDAP로 일반적인 경고 문제를 해결하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15c1f3a1731edf7b45061646d43688b4aacc6104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620310"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>알려진 문제: Azure Active Directory Domain Services의 보안 LDAP 경고

LDAP(Lightweight Directory Access Protocol)를 사용하여 Azure AD DS(Azure Active Directory Domain Services)와 통신하는 애플리케이션 및 서비스는 [보안 LDAP를 사용하도록 구성](tutorial-configure-ldaps.md)할 수 있습니다. 보안 LDAP가 제대로 작동하려면 적절한 인증서 및 필수 네트워크 포트를 열어야 합니다.

이 문서는 Azure AD DS에서 보안 LDAP 액세스를 사용하여 일반적인 경고를 이해하고 해결하는 데 도움이 됩니다.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: 보안 LDAP 네트워크 구성

### <a name="alert-message"></a>경고 메시지

‘관리되는 도메인에 인터넷을 통한 보안 LDAP를 사용할 수 있도록 설정되어 있습니다. 그러나 네트워크 보안 그룹을 사용하여 포트 636에 대한 액세스는 잠겨 있지 않습니다. 이로 인해 관리되는 도메인의 사용자 계정이 무차별 암호 대입 공격에 노출될 수 있습니다.’

### <a name="resolution"></a>해결 방법

보안 LDAP를 사용하도록 설정하는 경우 특정 IP 주소에 대한 인바운드 LDAPS 액세스를 제한하는 추가 규칙을 만드는 것이 좋습니다. 해당 규칙은 무차별 암호 대입 공격으로부터 관리되는 도메인을 보호합니다. 보안 LDAP에 대한 TCP 포트 636 액세스를 제한하도록 네트워크 보안 그룹을 업데이트하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **네트워크 보안 그룹** 을 검색하여 선택합니다.
1. *AADDS-contoso.com-NSG* 와 같이 관리되는 도메인과 연결된 네트워크 보안 그룹을 선택한 다음, **인바운드 보안 규칙** 을 선택합니다.
1. **+ 추가** 를 선택하여 TCP 포트 636에 대한 규칙을 만듭니다. 필요한 경우 창에서 **고급** 을 선택하여 규칙을 만듭니다.
1. **원본** 의 경우 드롭다운 메뉴에서 ‘IP 주소’를 선택합니다. 보안 LDAP 트래픽에 대한 액세스 권한을 부여하려는 원본 IP 주소를 입력합니다.
1. **대상** 으로 ‘임의’를 선택한 다음, **대상 포트 범위** 에 *636* 을 입력합니다.
1. **프로토콜** 을 *TCP* 로 설정하고 **작업** 을 ‘허용’으로 설정합니다.
1. 규칙의 우선 순위를 지정하고 *RestrictLDAPS* 와 같은 이름을 입력합니다.
1. **추가** 를 선택하여 규칙을 만듭니다.

관리되는 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

> [!TIP]
> Azure AD DS를 원활하게 실행하는 데 필요한 규칙은 TCP 포트 636뿐만이 아닙니다. 자세한 내용은 [Azure AD DS 네트워크 보안 그룹 및 필수 포트](network-considerations.md#network-security-groups-and-required-ports)를 참고하세요.

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: 보안 LDAP 인증서 만료

### <a name="alert-message"></a>경고 메시지

*관리되는 도메인에 대한 보안 LDAP 인증서는 [date]]에 만료됩니다.*

### <a name="resolution"></a>해결 방법

[보안 LDAP용 인증서를 생성](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)하는 단계에 따라 대체 보안 LDAP 인증서를 만듭니다. 대체 인증서를 Azure AD DS에 적용하고 보안 LDAP를 사용하여 연결하는 모든 클라이언트에 인증서를 배포합니다.

## <a name="next-steps"></a>다음 단계

문제가 여전히 발생하는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다][azure-support].

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
