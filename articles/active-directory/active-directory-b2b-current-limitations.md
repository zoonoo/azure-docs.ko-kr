---
title: "Azure Active Directory B2B 공동 작업의 제한 사항 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업 미리 보기의 현재 제한 사항"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0e71a840d4f503779131ee4a21fe6063d33185f1
ms.openlocfilehash: cbefca2d45a332cd57cfea49dfeaa300426d5502
ms.lasthandoff: 02/24/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 공동 작업의 제한 사항
Azure AD(Azure Active Directory) B2B 공동 작업에는 이 문서에 설명된 제한 사항이 적용됩니다.

## <a name="invitation-apis-are-in-preview"></a>초대 API는 미리 보기
API 노출 영역은 앞으로 예상되는 방향입니다. 그러나 모든 시험판 버전이 그렇듯이, 이 API는 미리 보기 네임스페이스 계약을 따릅니다. 이 API를 GA(일반 공급) 릴리스의 지정된 버전 번호로 이동할 예정입니다.

## <a name="possible-double-multi-factor-authentication"></a>가능한 이중 Multi-Factor Authentication
파트너가 이미 Azure Multi-factor Authentication 정책을 운영하고 있으면 이 중복성이 발생할 수 있습니다. 초대 조직에서 B2B 공동 작업 Multi-Factor Authentication이 수행 및 관리됩니다. 이러한 인증은 모든 ID에 해당되며 B2B 공동 작업의 초대 대상자에 대한 인증 강도를 제어할 수 있으므로 바람직하다고 할 수 있습니다.

그러나 파트너가 이미 Multi-Factor Authentication을 설정하여 적용 중인 경우 파트너의 사용자는 소속된 조직에서 인증을 한 번 수행한 후 여러분의 조직에서 다시 한 번 인증을 수행해야 할 수도 있습니다.

향후 릴리스에서는 파트너의 Multi-Factor Authentication을 신뢰하도록 선택하면 이중 인증 문제를 방지할 수 있는 정책을 도입할 예정입니다.

## <a name="instant-on"></a>인스턴트 온
B2B 공동 작업 흐름에서 해당 디렉터리에 사용자를 추가하고 초대 상환, 앱 할당 등에서 사용자를 동적으로 업데이트합니다. 업데이트 및 쓰기는 일반적으로 한 디렉터리 인스턴스에서 발생하며 모든 인스턴스에 걸쳐 복제해야 합니다. 복제를 완료하는 데 투입할 수 있는 시간이 한정되어 있기 때문에 종종 인증 문제가 발생할 수 있다는 사실이 관찰되었습니다. GA 릴리스 전에 이러한 문제를 최소화 또는 해결하기 위해 현재 노력 중입니다. 그 사이에 이러한 문제가 발생할 가능성은 낮지만 문제가 발생할 경우 새로 고침 또는 다시 시도를 통해 문제를 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 공동 작업 사용자 속성](active-directory-b2b-user-properties.md)
* [역할에 B2B 공동 작업 사용자 추가](active-directory-b2b-add-guest-to-role.md)
* [B2B 공동 작업 초대 위임](active-directory-b2b-delegate-invitations.md)
* [동적 그룹 및 B2B 공동 작업](active-directory-b2b-dynamic-groups.md)
* [B2B 공동 작업 코드 및 PowerShell 샘플](active-directory-b2b-code-samples.md)
* [B2B 공동 작업을 위한 SaaS 앱 구성](active-directory-b2b-configure-saas-apps.md)
* [B2B 공동 작업 사용자 토큰](active-directory-b2b-user-token.md)
* [B2B 공동 작업 사용자 클레임 매핑](active-directory-b2b-claims-mapping.md)
* [Office 365 외부 공유](active-directory-b2b-o365-external-user.md)

