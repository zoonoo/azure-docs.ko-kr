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
ms.sourcegitcommit: 6c8d7dc7d070ce65cbe637359bf7933eac68ce23
ms.openlocfilehash: ae87c3487abd194c524d067a2a9b7bb9f8df8ac7
ms.lasthandoff: 02/24/2017


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 외부 공유 및 Azure Active Directory B2B 공동 작업

Office 365의 외부 공유(OneDrive, SharePoint Online, 통합 그룹 등)와 Azure AD(Azure Active Directory) B2B 공동 작업은 기술적으로 동일합니다. 통합 그룹의 게스트를 비롯한 모든 외부 공유(OneDrive/SharePoint Online 제외)는 이미 공유에 Azure AD B2B 공동 작업 초대 API를 사용하고 있습니다.

OneDrive/SharePoint Online에는 별도 초대 관리자가 있습니다. OneDrive/SharePoint Online의 외부 공유 지원은 Azure AD에서 지원을 개발하기 전에 시작되었습니다. 시간이 지남에 따라 OneDrive/SharePoint Online 외부 공유의 기능도 늘어나고 수많은 사용자들이 이 제품의 기본 제공 공유 패턴을 사용하고 있습니다. 현재 Microsoft는 모든 제품의 프로세스를 통합하고 Azure AD가 구현하는 혁신을 도입할 수 있도록 Azure AD B2B 초대 API를 추가하기 위한 작업을 OneDrive/SharePoint Online에서 진행하고 있습니다. 작업이 완료되기 전에는 OneDrive/SharePoint Online 외부 공유의 작동 방식과 Azure AD B2B 공동 작업의 작동 방식 간에 미묘한 차이가 있습니다.

- 사용자가 초대를 교환하면 OneDrive/SharePoint Online은 사용자를 디렉터리에 추가합니다. 따라서 교환 전에는 Azure AD 포털에 사용자가 보이지 않습니다. 그 사이에 다른 사이트에서 사용자를 초대하면 새 초대가 생성됩니다. 그러나 Azure AD B2B 공동 작업을 사용하면 초대와 동시에 사용자가 추가되므로 모든 위치에 사용자가 표시됩니다.

- OneDrive/SharePoint Online의 교환 환경은 Azure AD B2B 공동 작업의 환경과 다릅니다. 사용자가 초대를 교환하면 환경이 비슷하게 보입니다.

- Azure AD B2B 공동 작업의 초대된 사용자는 OneDrive/SharePoint Online 공유 대화 상자에서 선택할 수 있습니다. OneDrive/SharePoint Online의 초대된 사용자는 초대를 상환한 후 Azure AD에도 표시됩니다.

- 관리되는 관리자 제어 방식으로 Azure AD B2B 공동 작업과 함께 OneDrive/SharePoint Online의 외부 공유를 사용하려면 OneDrive/SharePoint Online 외부 공유 설정을 **디렉터리에 이미 있는 외부 사용자와의 공유만 허용**으로 설정합니다. 사용자는 외부에서 공유되는 사이트로 이동하여 관리자가 추가한 외부 공동 관리자 중에서 선택할 수 있습니다. 관리자는 B2B 공동 작업 초대 API를 통해 외부 공동 작업자를 추가할 수 있습니다.

![OneDrive/SharePoint Online 외부 공유 설정](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

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
* [B2B 공동 작업 현재 제한](active-directory-b2b-current-limitations.md)

