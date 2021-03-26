---
title: 조건부 액세스 정책에서 클라우드 앱 또는 작업 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 클라우드 앱 또는 작업 이란 무엇인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c8024a2083d09fcbd53a37f0d391c4589748eea
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605079"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>조건부 액세스: 클라우드 앱 또는 작업

클라우드 앱 또는 작업은 조건부 액세스 정책의 주요 신호에 속합니다. 관리자는 조건부 액세스 정책을 사용하여 특정 애플리케이션 또는 작업에 컨트롤을 할당할 수 있습니다.

- 관리자는 기본 제공되는 Microsoft 애플리케이션과 모든 [Azure AD 통합 애플리케이션](../manage-apps/what-is-application-management.md)(갤러리, 비갤러리 및 [애플리케이션 프록시](../manage-apps/what-is-application-proxy.md)를 통해 게시된 애플리케이션 등)을 포함한 애플리케이션 목록에서 선택할 수 있습니다.
- 관리자는 클라우드 애플리케이션이 아닌, 사용자 작업을 기반으로 하여 정책을 정의하도록 선택할 수 있습니다. 유일하게 지원되는 작업은 보안 정보 등록(미리 보기)입니다. 여기서는 조건부 액세스를 사용하여 [결합된 보안 정보 등록 환경](../authentication/howto-registration-mfa-sspr-combined.md)을 제어할 수 있습니다.

![조건부 액세스 정책을 정의하고 클라우드 앱을 지정](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 클라우드 애플리케이션

기존 Microsoft 클라우드 애플리케이션 중 상당수는 선택할 수 있는 애플리케이션 목록에 포함되어 있습니다. 

관리자들은 Microsoft의 다음 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다. Office 365 및 Microsoft Azure 관리와 같은 일부 앱은 관련 된 여러 자식 앱 또는 서비스를 포함 합니다. 계속 해 서 더 많은 앱을 추가 하므로 다음 목록은 완전 하지 않으며 변경 될 수 있습니다.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database 및 Azure Synapse 분석](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights 분석
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 관리](#microsoft-azure-management)
- Microsoft Azure 구독 관리
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control 포털
- Microsoft Commerce Tools 인증 서비스
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 등록](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Bing에서 Microsoft Search
- Microsoft StaffHub
- Microsoft Stream
- Microsoft 팀
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI 서비스
- Project Online
- 비즈니스 온라인용 Skype
- VPN(가상 사설망)
- Windows Defender ATP

조건부 액세스에 사용할 수 있는 응용 프로그램이 온 보 딩 및 유효성 검사 프로세스를 통과 했습니다. 이는 백 엔드 서비스 이며 정책을 직접 적용 하지 않는 모든 Microsoft 앱을 포함 하지 않습니다. 누락 된 응용 프로그램을 찾고 있는 경우 특정 응용 프로그램 팀에 문의 하거나 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=167259)에 대해 요청을 수행할 수 있습니다.

### <a name="office-365"></a>Office 365

Microsoft 365은 Exchange, SharePoint 및 Microsoft 팀과 같은 클라우드 기반 생산성 및 공동 작업 서비스를 제공 합니다. Microsoft 365 클라우드 서비스는 원활한 공동 작업 환경을 보장 하기 위해 긴밀 하 게 통합 됩니다. Microsoft 팀과 같은 일부 앱이 SharePoint 또는 Exchange와 같은 다른 앱에 종속되어 있기 때문에 이러한 통합은 정책을 만들 때 혼동을 일으킬 수 있습니다.

Office 365 앱을 사용 하면 이러한 서비스를 한 번에 모두 대상으로 지정할 수 있습니다. [서비스 종속성](service-dependencies.md)문제를 방지 하기 위해 개별 클라우드 앱을 대상으로 지정 하는 대신 새 Office 365 앱을 사용 하는 것이 좋습니다. 이 애플리케이션 그룹을 대상으로 지정하면 일관되지 않은 정책 및 종속성으로 인해 발생할 수 있는 문제들을 방지하는 데 도움이 됩니다.

관리자는 Office 365 앱을 포함 하 고 정책에서 선택한 특정 앱을 제외 하 여 정책에서 특정 앱을 제외 하도록 선택할 수 있습니다.

Office 365 클라이언트 앱에 포함 된 주요 응용 프로그램:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft 팀
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 Search Service
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - 비즈니스 온라인용 Skype
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 관리

Microsoft Azure 관리 애플리케이션에는 여러 가지 기본 서비스가 포함되어 있습니다. 

   - Azure portal
   - Azure Resource Manager 공급자
   - 클래식 배포 모델 API
   - Azure PowerShell
   - Azure CLI
   - Visual Studio 구독 관리자 포털
   - Azure DevOps
   - Azure Data Factory 포털

> [!NOTE]
> Microsoft Azure 관리 애플리케이션은 Azure PowerShell에 적용되며 이는 Azure Resource Manager API를 호출합니다. Microsoft Graph를 호출하는 Azure AD PowerShell에는 적용되지 않습니다.

## <a name="other-applications"></a>다른 애플리케이션

Microsoft 앱 외에도 관리자는 Azure AD에 등록된 애플리케이션을 조건부 액세스 정책에 추가할 수 있습니다. 이러한 애플리케이션에는 다음이 포함될 수 있습니다. 

- [Azure AD 애플리케이션 프록시](../manage-apps/what-is-application-proxy.md)를 통해 게시된 애플리케이션
- [갤러리에서 추가된 애플리케이션](../manage-apps/add-application-portal.md)
- [갤러리에 없는 사용자 지정 애플리케이션](../manage-apps/view-applications-portal.md)
- [앱 제공 컨트롤러 및 네트워크를 통해 게시된 레거시 애플리케이션](../manage-apps/secure-hybrid-access.md)
- [암호 기반 Single Sign-On](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md) 를 사용 하는 응용 프로그램

> [!NOTE]
> 조건부 액세스 정책은 서비스에 액세스 하기 위한 요구 사항을 설정 하므로 클라이언트 (공용/네이티브) 응용 프로그램에 적용할 수 없습니다. 다시 말해, 정책은 클라이언트(퍼블릭/네이티브) 애플리케이션에 직접 설정되지 않으며 다만 클라이언트가 서비스를 호출할 때 적용됩니다. 예를 들어, SharePoint 서비스에서 설정된 정책은 SharePoint를 호출하는 클라이언트에 적용됩니다. Exchange에 설정된 정책은 Outlook 클라이언트를 사용하여 이메일에 액세스하려고 할 때 적용됩니다. 따라서 클라우드 앱 선택기에서 클라이언트(퍼블릭/네이티브) 애플리케이션을 사용할 수 없으며, 테넌트에 등록된 클라이언트(퍼블릭/네이티브) 애플리케이션의 애플리케이션 설정에서 조건부 액세스 옵션을 사용할 수 없습니다. 

## <a name="user-actions"></a>사용자 작업

사용자 작업은 사용자가 수행할 수 있는 작업입니다. 현재 조건부 액세스에서는 두 가지 사용자 동작을 지원 합니다. 

- **보안 정보 등록**:이 사용자 작업은 결합 된 등록을 사용 하도록 설정 된 사용자가 보안 정보를 등록 하려고 할 때 조건부 액세스 정책을 적용할 수 있도록 합니다. 자세한 내용은 [결합된 보안 정보 등록](../authentication/concept-registration-mfa-sspr-combined.md) 문서에서 확인할 수 있습니다.

- **장치 등록 또는 연결 (미리 보기)**:이 사용자 작업을 통해 관리자는 사용자가 Azure AD에 장치를 [등록](../devices/concept-azure-ad-register.md) 하거나 [조인할](../devices/concept-azure-ad-join.md) 때 조건부 액세스 정책을 적용할 수 있습니다. 이 사용자 작업에는 두 가지 주요 고려 사항이 있습니다. 
   - `Require multi-factor authentication` 이 사용자 작업에서 유일 하 게 사용할 수 있는 액세스 제어는 사용 하지 않도록 설정 되어 있습니다. 이 제한은 Azure AD 장치 등록에 종속 되거나 Azure AD 장치 등록에는 적용 되지 않는 액세스 제어와의 충돌을 방지 합니다. 
   - 이 사용자 동작을 사용 하 여 조건부 액세스 정책을 사용 하도록 설정한 경우 **Azure Active Directory**  >  **장치**  >  **장치 설정을**  -  `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` **아니요** 로 설정 해야 합니다. 그렇지 않으면이 사용자 동작을 사용 하는 조건부 액세스 정책이 제대로 적용 되지 않습니다. 이 장치 설정에 대 한 자세한 내용은 [장치 설정 구성](../devices/device-management-azure-portal.md#configure-device-settings)에서 찾을 수 있습니다. 이 사용자 작업은 장치 설정에 테 넌 트 차원의 정책을 사용 하는 대신 특정 사용자 및 그룹에 대 한 장치를 등록 하거나 조인 하기 위해 다단계 인증을 요구 하는 유연성을 제공 합니다. 
   
## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 조건](concept-conditional-access-conditions.md)

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)
- [클라이언트 애플리케이션 종속성](service-dependencies.md)
