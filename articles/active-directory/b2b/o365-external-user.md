---
title: Office 365 외부 공유 및 Azure Active Directory B2B 공동 작업 | Microsoft Docs
description: O365 및 Azure Active Directory B2B 공동 작업을 사용하여 외부 파트너와 리소스 공유에 대해 설명합니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7e0a65e4a5807cb9a6b39feecfd2d5b2643ea4a9
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330707"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 외부 공유 및 Azure Active Directory B2B 공동 작업

Office 365의 외부 공유(OneDrive, SharePoint Online, 통합 그룹 등) 기능과 Azure AD(Azure Active Directory) B2B 공동 작업 기능은 기술적으로 동일합니다. Office 365 그룹의 게스트를 비롯한 모든 외부 공유(OneDrive/SharePoint Online 제외)는 이미 공유에 Azure AD B2B 공동 작업 초대 API를 사용하고 있습니다.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Azure AD B2B가 SharePoint Online의 외부 공유와 어떻게 다른가?

OneDrive/SharePoint Online에는 별도 초대 관리자가 있습니다. OneDrive/SharePoint Online의 외부 공유 지원은 Azure AD에서 지원을 개발하기 전에 시작되었습니다. 시간이 지남에 따라 OneDrive/SharePoint Online 외부 공유의 기능도 늘어나고 수많은 사용자들이 이 제품의 기본 제공 공유 패턴을 사용하고 있습니다. 그렇지만 OneDrive/SharePoint Online 외부 공유의 작동 방식과 Azure AD B2B 공동 작업의 작동 방식 간에 미묘한 차이가 있습니다. [외부 공유 개요](https://docs.microsoft.com/sharepoint/external-sharing-overview)에서 OneDrive/SharePoint Online 외부 공유에 대해 더 자세히 알아볼 수 있습니다. 프로세스는 일반적으로 다음과 같은 면에서 Azure AD B2B와 다릅니다.

- 사용자가 초대를 교환하면 OneDrive/SharePoint Online은 사용자를 디렉터리에 추가합니다. 따라서 교환 전에는 Azure AD 포털에 사용자가 보이지 않습니다. 그 사이에 다른 사이트에서 사용자를 초대하면 새 초대가 생성됩니다. 그러나 Azure AD B2B 공동 작업을 사용하면 초대와 동시에 사용자가 추가되므로 모든 위치에 사용자가 표시됩니다.

- OneDrive/SharePoint Online의 교환 환경은 Azure AD B2B 공동 작업의 환경과 다릅니다. 사용자가 초대를 교환하면 환경이 비슷하게 보입니다.

- Azure AD B2B 공동 작업의 초대된 사용자는 OneDrive/SharePoint Online 공유 대화 상자에서 선택할 수 있습니다. OneDrive/SharePoint Online의 초대된 사용자는 초대를 상환한 후 Azure AD에도 표시됩니다.

- 라이선싱 요구 사항이 다릅니다. 각 유료 Azure AD 라이선스의 경우 최대 5명의 게스트 사용자가 유료 Azure AD 기능에 액세스할 수 있도록 할 수 있습니다. 라이선스에 대해 자세히 알아보려면 [Azure AD B2B 라이선스](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) 및 [SharePoint Online 외부 공유 개요의 “외부 사용자란?”](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-is-an-external-user)을 참조하세요.

Azure AD B2B 공동 작업 기능으로 OneDrive/SharePoint Online의 외부 공유를 관리하려면 OneDrive/SharePoint Online 외부 공유 설정을 **조직의 디렉터리에 이미 있는 외부 사용자와의 공유만 허용**으로 설정합니다. 사용자는 외부에서 공유되는 사이트로 이동하여 관리자가 추가한 외부 공동 관리자 중에서 선택할 수 있습니다. 관리자는 B2B 공동 작업 초대 API를 통해 외부 공동 작업자를 추가할 수 있습니다.


![OneDrive/SharePoint Online 외부 공유 설정](media/o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>다음 단계

* [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
* [역할에 B2B 공동 작업 사용자 추가](add-guest-to-role.md)
* [B2B 공동 작업 초대 위임](delegate-invitations.md)
* [동적 그룹 및 B2B 공동 작업](use-dynamic-groups.md)