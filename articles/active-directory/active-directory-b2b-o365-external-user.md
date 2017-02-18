---
title: "Office 365 외부 공유 및 Azure Active Directory B2B 공동 작업 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업에 대한 클레임 매핑 참조"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 716d19be09085db91c17d7ed76422e05d164d137


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 외부 공유 및 Azure Active Directory B2B 공동 작업

Office&365;의 외부 공유(OneDrive, SharePoint Online, 통합 그룹 등) 기능과 Azure AD(Azure Active Directory) B2B 공동 작업을 사용하는 것은 기술적으로 동일합니다. **통합 그룹의 게스트**를 비롯한 모든 외부 공유(OneDrive/SharePoint Online 제외)는 이미 공유를 위해 Azure AD B2B 공동 작업 초대 API를 사용하고 있습니다.

ODSP(OneDrive/SharePoint Online)는 Azure AD 패브릭의 일부로 지원되기 전부터 외부 공유에 대한 ODSP 지원이 시작되었으므로 별도의 초대 관리자 기능을 포함합니다. 시간이 지남에 따라 ODSP 외부 공유의 기능도 늘어나고 수많은 사용자들이 이 제품의 기본 제공 공유 패턴을 사용하고 있습니다. Microsoft는 Azure AD B2B 초대 API와 관련해서 현재 ODSP 개발 부서와 협력하면서 모든 종단 간 이점을 통합하여 Azure AD가 구현하는 환경을 개선하기 위해 노력하고 있습니다. 물론, ODSP 외부 공유의 작동 방식과 Azure AD B2B의 작동 방식 간에는 미묘한 차이가 있습니다.

1. ODSP는 자신의 초대를 상환한 사용하는 해당 디렉터리에 추가합니다. 따라서 사용자가 상환할 때까지 실제로는 Azure AD 포털에 해당 사용자가 보이지 않습니다. 사용자가 다른 사이트에서 초대를 받으면 새 초대가 생성됩니다. 그러나 우리는 B2B 공동 작업을 사용할 때 해당 사용자를 초대에 즉시 추가하므로 어디에서든지 볼 수 있는 것입니다.

2. ODSP의 상환 환경은 B2B 공동 작업의 환경과 다르게 보입니다.

3. 그러나 사용자가 초대를 상환한 경우에는 비슷해 보입니다.

4. B2B 공동 작업의 초대된 사용자는 ODSP 공유 대화 상자에서 선택할 수 있습니다. ODSP의 초대된 사용자는 초대를 상환한 후에도 Azure AD에 표시됩니다.

5. 관리되는 제어 방식으로 B2B 공동 작업과 함께 ODSP의 외부 공유를 사용하려면 ODSP 외부 공유 설정을 **디렉토리에 이미 있는 외부 사용자와의 공유만 허용**으로 설정합니다. 사용자는 외부에서 공유되는 사이트로 이동하고 관리자가 추가한 외부 공동 관리자 중에서 선택할 수 있습니다. 관리자는 B2B 공동 작업 초대 API를 통해 외부 공동 작업자를 추가할 수 있습니다.

![ODSP 외부 공유 설정](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 공동 작업 사용자 속성](active-directory-b2b-user-properties.md)
* [역할에 B2B 공동 작업 사용자 추가](active-directory-b2b-add-guest-to-role.md)
* [B2bB 공동 작업 초대 위임](active-directory-b2b-delegate-invitations.md)
* [동적 그룹 및 B2B 공동 작업](active-directory-b2b-dynamic-groups.md)
* [B2B 공동 작업 코드 및 PowerShell 샘플](active-directory-b2b-code-samples.md)
* [B2B 공동 작업용 SaaS 앱 구성](active-directory-b2b-configure-saas-apps.md)
* [B2B 공동 작업 사용자 토큰](active-directory-b2b-user-token.md)
* [B2B 공동 작업 사용자 클레임 매핑](active-directory-b2b-claims-mapping.md)
* [B2B 공동 작업 현재 제한](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


