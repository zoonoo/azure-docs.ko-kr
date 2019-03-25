---
title: LinkedIn 계정 연결-Azure Active Directory에 대 한 관리자 동의 | Microsoft Docs
description: 사용 하도록 설정 하거나 Azure Active Directory에서 Microsoft 앱에서 LinkedIn 통합 계정 연결을 사용 하지 않도록 설정 하는 방법을 설명 합니다.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368129"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Azure Active Directory 조직에 대해 LinkedIn 계정 연결에 동의

일부 Microsoft 앱 내에서 자신의 LinkedIn 연결에 액세스 하려면 조직에서 사용자를 허용할 수 있습니다. 데이터가 없는 사용자가 자신의 계정을 연결에 동의할 때까지 공유 됩니다. Azure Active Directory (Azure AD)에서 조직에 통합할 수 있습니다 [관리 센터](https://aad.portal.azure.com)합니다.

> [!IMPORTANT]
> LinkedIn 계정 연결 설정을 현재 롤아웃하는 Azure AD 조직에 있습니다. 조직에 롤아웃 된, 기본적으로 활성화 됩니다.
> 
> 예외:
> * Microsoft Cloud for US Government, Microsoft Cloud Germany 또는 중국에서 21Vianet이 운영하는 Azure 및 Office 365를 사용하는 고객은 이 설정을 사용할 수 없습니다.
> * 독일에서 프로비전된 테넌트에 대해서는 설정이 기본적으로 꺼져 있습니다. Microsoft Cloud Germany를 사용하는 고객은 이 설정을 사용할 수 없습니다.
> * 프랑스에서 프로비전된 테넌트에 대해서는 설정이 기본적으로 꺼져 있습니다.
>
> LinkedIn 계정 연결 된 조직에 대해 사용 하도록 설정 하면 사용자 본인을 대신해 회사 데이터에 액세스 하는 앱에 동의한 후 계정 연결을 작동 합니다. 사용자 동의 설정에 대 한 자세한 내용은 [응용 프로그램에 대 한 사용자의 액세스를 제거 하는 방법](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)합니다.

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>LinkedIn 계정 연결을 사용 하도록 설정 하려면 Azure portal을 사용 합니다.

조직에서 선택한 사용자 에게만 전체 조직에서 액세스 하려는 사용자만 LinkedIn 계정 연결을 설정할 수 있습니다.

1. 에 로그인 합니다 [Azure AD 관리 센터](https://aad.portal.azure.com/) Azure AD 조직에 대 한 전역 관리자 계정으로 합니다.
2. **사용자**를 선택합니다.
3. **사용자** 블레이드에서 **사용자 설정**을 선택합니다.
4. 아래 **LinkedIn 계정 연결**, 사용자가 일부 Microsoft 앱 내에서 자신의 LinkedIn 연결에 액세스 하려면 해당 계정에 연결할 수 있습니다. 데이터가 없는 사용자가 자신의 계정을 연결에 동의할 때까지 공유 됩니다.

  * 선택 **예** 조직의 모든 사용자에 대 한 서비스에 동의
  * 선택 **선택한** 조직에서 선택 된 사용자만에 대 한 동의
  * 선택 **No** 조직의 사용자에 대 한 동의 철회 하려면

    ![조직에서 LinkedIn 계정 연결 통합](./media/linkedin-integration/linkedin-integration.png)

5. 완료 되 면 선택 **저장할** 여 설정을 저장 합니다.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>그룹 정책을 사용 하 여 LinkedIn 계정 연결을 사용 하도록 설정

1. [Office 2016 관리 템플릿 파일(ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) 다운로드
2. **ADMX** 파일을 추출하고 중앙 저장소에 복사합니다.
3. 그룹 정책 관리를 엽니다.
4. 사용자 구성관리 템플릿Microsoft Office 2016기타Office 애플리케이션에 LinkedIn 기능 표시 설정을 사용하여 그룹 정책 개체를 만듭니다.
5. **사용** 또는 **사용 안 함**을 선택합니다.
  
   시스템 상태 | 결과
   ------ | ------
   **사용** | Office 2016 옵션의 **Office 애플리케이션에 LinkedIn 기능 표시** 설정이 사용으로 설정됩니다. 조직의 사용자가 Office 2016 응용 프로그램에서 LinkedIn 기능을 사용할 수 있습니다.
   **사용 안 함** | Office 2016 옵션의 **Office 응용 프로그램에 LinkedIn 기능 표시** 설정이 사용 안 함으로 설정되며, 최종 사용자가 이 설정을 변경할 수 없습니다. 조직 내 사용자가 Office 2016 애플리케이션에서 LinkedIn 기능을 사용할 수 있습니다.

이 그룹 정책은 로컬 컴퓨터용 Office 2016 앱에만 영향을 줍니다. 사용자가 Office 2016 앱에서 LinkedIn을 해제, Office 365에서 LinkedIn 기능을 계속 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [사용자 동 및 LinkedIn에 대 한 공유 데이터](linkedin-user-consent.md)

* [Microsoft 앱의 LinkedIn 정보 및 기능](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 도움말 센터](https://www.linkedin.com/help/linkedin)

* [Azure Portal에서 현재 LinkedIn 통합 설정 보기](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
