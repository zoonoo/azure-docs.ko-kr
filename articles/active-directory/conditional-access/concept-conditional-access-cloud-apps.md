---
title: 조건부 액세스 정책의 클라우드 앱 또는 작업 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 클라우드 앱 또는 작업이란 무엇입니까?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69bdd2d6825427597e9030a03aae7d219361ba25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671939"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>조건부 액세스: 클라우드 앱 또는 작업

클라우드 앱 또는 작업은 조건부 액세스 정책의 핵심 신호입니다. 조건부 액세스 정책을 사용하면 관리자가 특정 응용 프로그램 이나 작업에 컨트롤을 할당할 수 있습니다.

- 관리자는 기본 제공 Microsoft 응용 프로그램과 응용 프로그램 [프록시를](../manage-apps/what-is-application-proxy.md)통해 게시된 갤러리, 비갤러리 및 응용 프로그램을 포함한 [모든 Azure AD 통합 응용 프로그램을](../manage-apps/what-is-application-management.md) 포함하는 응용 프로그램 목록에서 선택할 수 있습니다.
- 관리자는 클라우드 응용 프로그램이 아니라 사용자 작업을 기반으로 정책을 정의하도록 선택할 수 있습니다. 지원되는 유일한 작업은 보안 정보 등록(미리 보기)뿐이며 조건부 액세스가 [결합된 보안 정보 등록 환경](../authentication/howto-registration-mfa-sspr-combined.md)주위에 컨트롤을 적용할 수 있도록 합니다.

![조건부 액세스 정책 정의 및 클라우드 앱 지정](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 클라우드 애플리케이션

기존 Microsoft 클라우드 응용 프로그램의 대부분은 선택할 수 있는 응용 프로그램 목록에 포함되어 있습니다. 

관리자는 Microsoft의 다음 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다. Office 365(미리 보기) 및 Microsoft Azure 관리와 같은 일부 앱에는 여러 관련 하위 앱 또는 서비스가 포함됩니다. 다음 목록은 완전하지 않으며 변경될 수 있습니다.

- [사무실 365(미리 보기)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database 및 Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- 마이크로소프트 애플리케이션 인사이트 분석
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 관리](#microsoft-azure-management)
- 마이크로소프트 Azure 구독 관리
- Microsoft Cloud App Security
- 마이크로소프트 상거래 도구 액세스 제어 포털
- 마이크로소프트 상거래 도구 인증 서비스
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 등록](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- 빙에서 마이크로소프트 검색
- Microsoft StaffHub
- Microsoft Stream
- Microsoft 팀
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- 오피스 스웨이
- Outlook Groups
- Power BI 서비스
- Project Online
- 비즈니스 온라인용 Skype
- 가상 사설망(VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>사무실 365(미리 보기)

Office 365는 Exchange, SharePoint 및 Microsoft Teams와 같은 클라우드 기반 생산성 및 공동 작업 서비스를 제공합니다. Office 365 클라우드 서비스는 원활하고 협업적인 경험을 보장하기 위해 깊이 통합되어 있습니다. 이러한 통합은 Microsoft Teams와 같은 일부 앱이 SharePoint 또는 Exchange와 같은 다른 앱에 종속되어 있으므로 정책을 만들 때 혼동을 일으킬 수 있습니다.

Office 365(미리 보기) 앱을 사용하면 이러한 서비스를 한 번에 모두 대상으로 지정할 수 있습니다. 개별 클라우드 앱을 대상으로 하는 대신 새 Office 365(미리 보기) 앱을 사용하는 것이 좋습니다. 이 응용 프로그램 그룹을 대상으로 지정하면 일관성 없는 정책 및 종속성으로 인해 발생할 수 있는 문제를 방지할 수 있습니다.

관리자는 Office 365(미리 보기) 앱을 포함하고 정책에서 선택한 특정 앱을 제외하여 원하는 경우 정책에서 특정 앱을 제외하도록 선택할 수 있습니다.

Office 365(미리 보기) 클라이언트 앱에 포함된 주요 응용 프로그램:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft 팀
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - 사무실 365 검색 서비스
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - 비즈니스 온라인용 Skype
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 관리

Microsoft Azure 관리 응용 프로그램에는 여러 기본 서비스가 포함되어 있습니다. 

   - Azure portal
   - Azure 리소스 관리자 공급자
   - 클래식 배포 모델 API
   - Azure PowerShell
   - 비주얼 스튜디오 구독 관리자 포털
   - Azure DevOps
   - Azure 데이터 팩토리 포털

> [!NOTE]
> Microsoft Azure 관리 응용 프로그램은 Azure 리소스 관리자 API를 호출하는 Azure PowerShell에 적용됩니다. Microsoft Graph를 호출하는 Azure AD PowerShell에는 적용되지 않습니다.

## <a name="other-applications"></a>다른 애플리케이션

관리자는 Microsoft 앱 외에도 Azure AD 등록 응용 프로그램을 조건부 액세스 정책에 추가할 수 있습니다. 이러한 애플리케이션에는 다음이 포함될 수 있습니다. 

- [Azure AD 응용 프로그램 프록시를](../manage-apps/what-is-application-proxy.md) 통해 게시된 응용 프로그램
- [갤러리에서 추가된 응용 프로그램](../manage-apps/add-application-portal.md)
- [갤러리에 없는 사용자 지정 응용 프로그램](../manage-apps/add-non-gallery-app.md)
- [앱 전송 컨트롤러 및 네트워크를 통해 게시된 레거시 응용 프로그램](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>사용자 작업

사용자 작업은 사용자가 수행할 수 있는 작업입니다. 현재 지원되는 유일한 작업은 **등록 보안 정보(미리 보기)로,** 통합 등록을 사용하도록 설정된 사용자가 보안 정보를 등록하려고 할 때 조건부 액세스 정책을 적용할 수 있습니다. 자세한 내용은 문서에서 찾을 수 있습니다., [결합 된 보안 정보 등록 (미리 보기)](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 조건](concept-conditional-access-conditions.md)

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)
- [클라이언트 응용 프로그램 종속성](service-dependencies.md)
