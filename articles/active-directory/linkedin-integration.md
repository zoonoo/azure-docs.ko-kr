---
title: "Azure Active Directory에서 Office 응용 프로그램용 LinkedIn 통합을 사용하거나 사용하지 않도록 설정하는 방법 | Microsoft Docs"
description: "Azure Active Directory에서 Microsoft 앱용 LinkedIn 통합을 사용하거나 사용하지 않도록 설정하는 방법에 대해 설명합니다."
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/30/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 5ebc44d0ef6200baeacf4f1f8c4371e2d1eed9db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Office 응용 프로그램용 LinkedIn 통합
이 문서에서는 Azure AD(Azure Active Directory)에서 LinkedIn 통합이 제공되는 사용자를 제한하는 방법을 설명합니다. LinkedIn 통합은 테넌트에 추가될 때 기본적으로 활성화되며, 사용자가 일부 Microsoft 앱 내에서 공용 LinkedIn 데이터에 액세스할 수 있게 해줍니다. 각 사용자가 독립적으로 회사 또는 학교 계정을 LinkedIn 계정에 연결할 수 있습니다.

> [!IMPORTANT]
> LinkedIn 통합은 모든 Azure AD 테넌트에 동시에 배포되지 않습니다. Azure 테넌트에 롤아웃된 후 LinkedIn 통합이 기본적으로 사용됩니다. LinkedIn 통합은 지역, 독립 및 정부 테넌트에 사용할 수 없습니다. 

## <a name="linkedin-integration-from-the-user-perspective"></a>사용자 관점에서 보는 LinkedIn 통합
조직 내 사용자가 자신의 LinkedIn 계정을 회사 또는 학교 계정에 연결하면 [LinkedIn은 조직에서 제공하는 Microsoft 앱 및 서비스에 사용할 데이터를 제공할 수 있습니다](https://www.linkedin.com/help/linkedin/answer/84077). [사용자가 계정 연결을 끊을 수 있으며](https://www.linkedin.com/help/linkedin/answer/85097), 연결을 끊으면 LinkedIn이 Microsoft와 데이터를 공유하는 권한이 제거됩니다. LinkedIn 통합은 공개적으로 사용할 수 있는 LinkedIn 프로필 정보를 사용합니다. LinkedIn 개인 정보 설정을 사용하여 Microsoft 앱에서 프로필을 볼 수 있는지 여부를 포함하여 [LinkedIn 프로필을 보는 방법을 제어할 수 있습니다](https://www.linkedin.com/help/linkedin/answer/83).

## <a name="privacy-considerations"></a>개인정보처리방침 고려 사항
Azure AD에서 LinkedIn 통합을 사용하도록 설정하면 Microsoft 앱 및 서비스에서 사용자의 LinkedIn 정보 중 일부에 액세스할 수 있습니다. Azure AD에서 LinkedIn 통합 사용 시 개인정보 취급 방침에 대한 자세한 내용은 [Microsoft 개인정보처리방침](https://privacy.microsoft.com/privacystatement/)을 참조하세요. 

## <a name="manage-linkedin-integration"></a>LinkedIn 통합 관리
Azure AD에서 엔터프라이즈에 대한 LinkedIn 통합은 기본적으로 사용됩니다. LinkedIn 통합을 사용하면 조직 내 모든 사용자가 Microsoft 서비스 내에서 LinkedIn 기능을 사용할 수 있습니다(예: Outlook에서 LinkedIn 프로필 보기). LinkedIn 통합을 사용하지 않도록 설정하면 Microsoft 앱 및 서비스에서 LinkedIn 기능이 제거되고 Microsoft 서비스를 통한 LinkedIn과 조직 간 데이터 공유가 중지됩니다.

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>Azure Portal에서 조직의 LinkedIn 사용 설정 또는 해제

1. Azure AD 테넌트의 전역 관리자인 계정으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에 로그인합니다.
2. **사용자 및 그룹**을 선택합니다.
3. **사용자 및 그룹** 블레이드에서 **사용자 설정**을 선택합니다.
4. **LinkedIn 통합**에서 **예** 또는 **아니요**를 선택하여 LinkedIn 통합을 사용 또는 사용하지 않도록 설정합니다.
   ![LinkedIn 통합 사용](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>그룹 정책을 사용하여 조직의 Office 2016 앱에 LinkedIn을 사용하도록 설정 또는 해제

1. [Office 2016 관리 템플릿 파일(ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) 다운로드
2. **ADMX** 파일을 추출하고 **중앙 리포지토리**에 복사합니다.
3. 그룹 정책 관리를 엽니다.
4. **사용자 구성** > **관리 템플릿** > **Microsoft Office 2016** > **기타** > **LinkedIn 통합 허용** 설정을 사용하여 그룹 정책 개체를 만듭니다.
5. **사용** 또는 **사용 안 함**을 선택합니다.
  * 이 정책을 **사용**으로 설정하면 Office 2016 옵션 대화 상자에 있는 **Office 응용 프로그램에서 LinkedIn 기능 표시** 설정이 사용됩니다. 즉, 조직의 사용자가 Office 응용 프로그램에서 LinkedIn 기능을 사용할 수 있습니다.
  * 이 정책을 **사용 안 함**으로 설정하면 Office 2016 옵션 대화 상자에 있는 **Office 응용 프로그램에서 LinkedIn 기능 표시** 설정이 사용 안 함 상태로 설정되며, 최종 사용자가 이 설정을 변경할 수 없습니다. 조직 내 사용자가 Office 2016 응용 프로그램에서 LinkedIn 기능을 사용할 수 있습니다. 

이 그룹 정책은 로컬 컴퓨터용 Office 2016 앱에만 영향을 줍니다. 사용자가 Office 2016 앱에서 LinkedIn을 해제하더라도 Office 365 전체의 카드 프로필에 LinkedIn 기능이 표시됩니다. 

### <a name="learn-more"></a>자세한 정보 
* [Microsoft 앱의 LinkedIn 정보 및 기능](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 도움말 센터](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>다음 단계
다음 링크를 사용하여 Azure Portal에서 현재 LinkedIn 통합 설정을 살펴봅니다.

[LinkedIn 통합 구성](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 