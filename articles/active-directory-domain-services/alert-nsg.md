---
title: Azure AD DS에서 네트워크 보안 그룹 경고 해결 | Microsoft Docs
description: Azure Active Directory Domain Services에 대한 네트워크 보안 그룹 구성 경고 문제를 해결하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: 5b48d326efad889adbcf25d487ee27b8200f558f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693921"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>알려진 문제: Azure Active Directory Domain Services의 네트워크 구성 경고

애플리케이션 및 서비스가 관리되는 도메인 Azure AD DS(Azure Active Directory Domain Services)와 올바르게 통신할 수 있도록 하려면 트래픽이 흐를 수 있도록 특정 네트워크 포트가 열려 있어야 합니다. Azure에서는 네트워크 보안 그룹을 사용하여 트래픽 흐름을 제어합니다. Azure AD DS 관리되는 도메인의 상태는 필요한 네트워크 보안 그룹 규칙이 적용되지 않은 경우 경고를 표시합니다.

이 문서는 네트워크 보안 그룹 구성 문제에 대한 일반적인 경고를 이해하고 해결하는 데 도움이 됩니다.

## <a name="alert-aadds104-network-error"></a>경고 AADDS104: 네트워크 오류

### <a name="alert-message"></a>경고 메시지

*Microsoft는 이 관리되는 도메인의 도메인 컨트롤러에 연결할 수 없습니다. 가상 네트워크에서 구성된 NSG(네트워크 보안 그룹)가 관리되는 도메인에 대한 액세스를 차단하는 경우 이 문제가 발생할 수 있습니다. 또 다른 가능한 원인은 인터넷에서 들어오는 트래픽을 차단하는 사용자 정의된 경로가 있는 경우입니다.*

잘못된 네트워크 보안 그룹 규칙은 Azure AD DS에 대한 네트워크 오류의 가장 일반적인 원인입니다. 가상 네트워크에 대한 네트워크 보안 그룹은 특정 포트 및 프로토콜에 대한 액세스를 허용해야 합니다. 이러한 포트가 차단되면 Azure 플랫폼에서 관리되는 도메인을 모니터링하거나 업데이트할 수 없습니다. Azure AD 디렉터리와 Azure AD DS 간의 동기화도 영향을 받습니다. 서비스 중단을 방지하기 위해 기본 포트를 열어 두어야 합니다.

## <a name="default-security-rules"></a>기본 보안 규칙

다음 기본 인바운드 및 아웃바운드 보안 규칙은 관리되는 도메인에 대한 네트워크 보안 그룹에 적용됩니다. 이러한 규칙은 Azure AD DS를 안전하게 유지하고 Azure 플랫폼에서 관리되는 도메인을 모니터링, 관리 및 업데이트할 수 있도록 합니다.

### <a name="inbound-security-rules"></a>인바운드 보안 규칙

| 우선 순위 | Name | 포트 | 프로토콜 | 원본 | 대상 | 작업 |
|----------|------|------|----------|--------|-------------|--------|
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | 모두 | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | 모두 | <sup>1</sup> 거부 |
| 65000    | AllVnetInBound | 모두 | 모두 | VirtualNetwork | VirtualNetwork | 허용 |
| 65001    | AllowAzureLoadBalancerInBound | 모두 | 모두 | AzureLoadBalancer | 모두 | 허용 |
| 65500    | DenyAllInBound | 모두 | 모두 | 모두 | 모두 | 거부 |


<sup>1</sup> 디버깅의 경우 선택 사항입니다. 고급 문제 해결에 필요한 경우 허용합니다.

> [!NOTE]
> [보안 LDAP를 구성][configure-ldaps]하는 경우 인바운드 트래픽을 허용하는 추가 규칙이 있을 수 있습니다. 이 추가 규칙은 올바른 LDAPS 통신에 필요합니다.

### <a name="outbound-security-rules"></a>아웃바운드 보안 규칙

| 우선 순위 | Name | 포트 | 프로토콜 | 원본 | 대상 | 작업 |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | 모두 | 모두 | VirtualNetwork | VirtualNetwork | 허용 |
| 65001    | AllowAzureLoadBalancerOutBound | 모두 | 모두 |  모두 | 인터넷 | 허용 |
| 65500    | DenyAllOutBound | 모두 | 모두 | 모두 | 모두 | 거부 |

>[!NOTE]
> Azure AD DS에는 가상 네트워크에의 무제한 아웃바운드 액세스가 필요합니다. 가상 네트워크에 대한 아웃바운드 액세스를 제한하는 추가 규칙은 만들지 않는 것이 좋습니다.

## <a name="verify-and-edit-existing-security-rules"></a>기존 보안 규칙 확인 및 편집

기존 보안 규칙을 확인하고 기본 포트가 열려 있는지 확인하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **네트워크 보안 그룹** 을 검색하여 선택합니다.
1. *AADDS-contoso.com-NSG* 와 같이 관리되는 도메인과 연결된 네트워크 보안 그룹을 선택합니다.
1. **개요** 페이지에 기존 인바운드 및 아웃바운드 보안 규칙이 표시됩니다.

    인바운드 및 아웃바운드 규칙을 검토하고 이전 섹션의 필수 규칙 목록과 비교합니다. 필요한 경우 필요한 트래픽을 차단하는 사용자 지정 규칙을 선택하고 삭제합니다. 필요한 규칙이 누락된 경우 다음 섹션에서 규칙을 추가합니다.

    필요한 트래픽을 허용하는 규칙을 추가하거나 삭제한 후 관리되는 도메인의 상태는 2시간 내에 자동으로 업데이트되며 경고를 제거합니다.

### <a name="add-a-security-rule"></a>보안 규칙 추가

누락된 보안 규칙을 추가하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **네트워크 보안 그룹** 을 검색하여 선택합니다.
1. *AADDS-contoso.com-NSG* 와 같이 관리되는 도메인과 연결된 네트워크 보안 그룹을 선택합니다.
1. 왼쪽 패널의 **설정** 에서 추가해야 하는 규칙에 따라 *인바운드 보안 규칙* 또는 *아웃바운드 보안 규칙* 을 클릭합니다.
1. **추가** 를 선택한 다음 포트, 프로토콜, 방향 등을 기준으로 필요한 규칙을 만듭니다. 준비가 되면 **확인** 을 선택합니다.

보안 규칙이 추가되고 목록에 표시되는 데까지는 몇 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계

문제가 여전히 발생하는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다][azure-support].

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
