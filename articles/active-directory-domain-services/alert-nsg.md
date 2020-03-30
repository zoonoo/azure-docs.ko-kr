---
title: Azure AD DS에서 네트워크 보안 그룹 경고 해결 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스에 대한 네트워크 보안 그룹 구성 경고 문제를 해결하고 해결하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257998"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>알려진 문제: Azure Active Directory 도메인 서비스의 네트워크 구성 경고

응용 프로그램 및 서비스가 Azure Active Directory 도메인 서비스(Azure AD DS)와 올바르게 통신할 수 있도록 하려면 트래픽이 흐를 수 있도록 특정 네트워크 포트를 열어야 합니다. Azure에서는 네트워크 보안 그룹을 사용하여 트래픽 흐름을 제어합니다. Azure AD DS 관리 도메인의 상태는 필요한 네트워크 보안 그룹 규칙이 없는 경우 경고를 표시합니다.

이 문서에서는 네트워크 보안 그룹 구성 문제에 대한 일반적인 경고를 이해하고 해결하는 데 도움이 됩니다.

## <a name="alert-aadds104-network-error"></a>경고 AADDS104: 네트워크 오류

### <a name="alert-message"></a>경고 메시지

*Microsoft는 이 관리되는 도메인의 도메인 컨트롤러에 연결할 수 없습니다. 가상 네트워크에 구성된 NSG(네트워크 보안 그룹)가 관리되는 도메인에 대한 액세스를 차단하는 경우 이러한 일이 발생할 수 있습니다. 또 다른 가능한 이유는 인터넷에서 들어오는 트래픽을 차단하는 사용자 정의 경로가 있는 경우입니다.*

잘못된 네트워크 보안 그룹 규칙은 Azure AD DS의 네트워크 오류의 가장 일반적인 원인입니다. 가상 네트워크의 네트워크 보안 그룹은 특정 포트 및 프로토콜에 대한 액세스를 허용해야 합니다. 이러한 포트가 차단되면 Azure 플랫폼은 관리되는 도메인을 모니터링하거나 업데이트할 수 없습니다. Azure AD 디렉터리 및 Azure AD DS 관리 도메인 간의 동기화도 영향을 받습니다. 서비스 중단을 방지하기 위해 기본 포트를 열어 두십시오.

## <a name="default-security-rules"></a>기본 보안 규칙

Azure AD DS 관리 도메인의 네트워크 보안 그룹에는 다음과 같은 기본 인바운드 및 아웃바운드 보안 규칙이 적용됩니다. 이러한 규칙은 Azure AD DS를 안전하게 유지하고 Azure 플랫폼이 관리되는 도메인을 모니터링, 관리 및 업데이트할 수 있도록 합니다. [보안 LDAP를 구성하는][configure-ldaps]경우 인바운드 트래픽을 허용하는 추가 규칙이 있을 수도 있습니다.

### <a name="inbound-security-rules"></a>인바운드 보안 규칙

| 우선 순위 | 이름 | 포트 | 프로토콜 | 원본 | 대상 | 작업 |
|----------|------|------|----------|--------|-------------|--------|
| 101      | 허용동기화AzureAD | 443 | TCP | AzureActive 디렉터리도메인 서비스 | 모두 | Allow |
| 201      | 허용RD | 3389 | TCP | 코프넷소 | 모두 | Allow |
| 301      | 허용PSRemoting | 5986| TCP | AzureActive 디렉터리도메인 서비스 | 모두 | Allow |
| 65000    | 올브네인바운드 | 모두 | 모두 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | 모두 | 모두 | AzureLoadBalancer | 모두 | Allow |
| 65500    | DenyAllInBound | 모두 | 모두 | 모두 | 모두 | 거부 |

### <a name="outbound-security-rules"></a>아웃바운드 보안 규칙

| 우선 순위 | 이름 | 포트 | 프로토콜 | 원본 | 대상 | 작업 |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | 올브넷아웃바운드 | 모두 | 모두 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | 허용Azure로드밸런스러아웃바운드 | 모두 | 모두 |  모두 | 인터넷 | Allow |
| 65500    | DenyAllOutBound | 모두 | 모두 | 모두 | 모두 | 거부 |

>[!NOTE]
> Azure AD DS는 가상 네트워크에서 무제한 아웃바운드 액세스가 필요합니다. 가상 네트워크에 대한 아웃바운드 액세스를 제한하는 추가 규칙을 만들지 않는 것이 좋습니다.

## <a name="verify-and-edit-existing-security-rules"></a>기존 보안 규칙 확인 및 편집

기존 보안 규칙을 확인하고 기본 포트가 열려 있는지 확인하려면 다음 단계를 완료하십시오.

1. Azure 포털에서 네트워크 보안 **그룹을**검색하고 선택합니다.
1. *AADDS-contoso.com-NSG와*같이 관리되는 도메인과 연결된 네트워크 보안 그룹을 선택합니다.
1. **개요** 페이지에 기존 인바운드 및 아웃바운드 보안 규칙이 표시됩니다.

    인바운드 및 아웃바운드 규칙을 검토하고 이전 섹션의 필수 규칙 목록과 비교합니다. 필요한 경우 필요한 트래픽을 차단하는 사용자 지정 규칙을 선택하고 삭제합니다. 필요한 규칙이 없는 경우 다음 섹션에 규칙을 추가합니다.

    필요한 트래픽을 허용하는 규칙을 추가하거나 삭제하면 Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

### <a name="add-a-security-rule"></a>보안 규칙 추가

누락된 보안 규칙을 추가하려면 다음 단계를 완료하십시오.

1. Azure 포털에서 네트워크 보안 **그룹을**검색하고 선택합니다.
1. *AADDS-contoso.com-NSG와*같이 관리되는 도메인과 연결된 네트워크 보안 그룹을 선택합니다.
1. 왼쪽 패널의 **설정에서** 추가해야 하는 규칙에 따라 *인바운드 보안 규칙* 또는 *아웃바운드 보안 규칙을* 클릭합니다.
1. **추가를**선택한 다음 포트, 프로토콜, 방향 등을 기반으로 필요한 규칙을 만듭니다. 준비가 되면 **확인을**선택합니다.

보안 규칙을 추가하고 목록에 표시하는 데 몇 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계

그래도 문제가 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
