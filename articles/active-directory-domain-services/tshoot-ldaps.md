---
title: Azure AD Domain Services에서 보안 LDAP(LDAPS) 문제 해결 | Microsoft Docs
description: Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 문제 해결
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: c9732545e1759ea23d62c0a56379e3868ed40a0b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246631"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 문제 해결

## <a name="connection-issues"></a>연결 문제
보안 LDAP를 사용하여 관리되는 도메인에 연결하는 데 문제가 있는 경우

* 보안 LDAP 인증서의 발급자 체인을 클라이언트에서 신뢰할 수 있어야 합니다. 트러스트를 설정하려면 클라이언트의 신뢰할 수 있는 루트 인증서 저장소에 루트 인증 기관을 추가할 수 있습니다.
* LDAP 클라이언트(예: ldp.exe)가 IP 주소가 아닌 DNS 이름을 사용하여 보안 LDAP 엔드포인트에 연결되는지 확인합니다.
* LDAP 클라이언트가 연결할 DNS 이름을 확인합니다. 관리되는 도메인에서 보안 LDAP에 대한 공용 IP 주소를 확인해야 합니다.
* 관리되는 도메인의 보안 LDAP 인증서의 주체 또는 주체 대체 이름 특성에 DNS 이름이 있는지 확인합니다.
* 가상 네트워크에 대한 NSG 설정은 인터넷에서 포트 636에 대해 트래픽을 허용해야 합니다. 인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정한 경우에만 이 단계가 적용됩니다.


## <a name="need-help"></a>도움 필요 시
보안 LDAP를 사용하여 관리되는 도메인에 연결하는 데 계속 문제가 발생하면 [제품 팀에 도움을 요청](contact-us.md)하세요. 문제를 진단하는 데 도움이 되도록 다음 정보를 포함합니다.
* 연결을 만들고 실패하는 ldp.exe의 스크린샷
* Azure AD 테넌트 ID 및 관리되는 도메인의 DNS 도메인 이름
* 바인딩하려는 정확한 사용자 이름입니다.


## <a name="related-content"></a>관련 콘텐츠
* [Azure AD Domain Services - 시작 가이드](create-instance.md)
* [Azure AD Domain Services 도메인을 관리 합니다.](manage-domain.md)
* [LDAP query basics](https://technet.microsoft.com/library/aa996205.aspx)(LDAP 쿼리 기본 사항)
* [Azure AD Domain Services에 대 한 그룹 정책 관리](manage-group-policy.md)
* [네트워크 보안 그룹](../virtual-network/security-overview.md)
* [네트워크 보안 그룹 만들기](../virtual-network/tutorial-filter-network-traffic.md)
