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
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1130885cc3168582935264ffaad9fd7a8ba3c60b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920252"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Azure Active Directory에서 LinkedIn 계정 연결 통합

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

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Azure portal에서 LinkedIn 계정 연결을 사용 하도록 설정

조직에서 선택한 사용자 에게만 전체 조직에서 액세스 하려는 사용자만 LinkedIn 계정 연결을 설정할 수 있습니다.

1. 에 로그인 합니다 [Azure AD 관리 센터](https://aad.portal.azure.com/) Azure AD 조직에 대 한 전역 관리자 계정으로 합니다.
1. **사용자**를 선택합니다.
1. **사용자** 블레이드에서 **사용자 설정**을 선택합니다.
1. 아래 **LinkedIn 계정 연결**, 사용자가 일부 Microsoft 앱 내에서 자신의 LinkedIn 연결에 액세스 하려면 해당 계정에 연결할 수 있습니다. 데이터가 없는 사용자가 자신의 계정을 연결에 동의할 때까지 공유 됩니다.

    * 선택 **예** 조직의 모든 사용자에 대 한 서비스를 사용 하도록 설정 하려면
    * 선택 **선택한** 조직에서 선택한 사용자 그룹만 사용 하도록 설정 하려면
    * 선택 **No** 조직의 모든 사용자의 동의 철회 하려면

    ![조직에서 LinkedIn 계정 연결 통합](./media/linkedin-integration/linkedin-integration.png)

1. 완료 되 면 선택 **저장할** 여 설정을 저장 합니다.

> [!Important]
> LinkedIn 통합 사용 되지 않습니다 완벽 하 게 사용자에 게 동의 해당 계정에 연결할 때까지. 데이터가 없는 사용자에 게 계정 연결을 사용 하도록 설정 하면 공유 됩니다.

### <a name="assign-selected-users-with-a-group"></a>선택한 사용자 그룹을 사용 하 여 할당
여러 개별 사용자 대신 단일 그룹에 대 한 LinkedIn 및 Microsoft 계정 연결 기능을 설정할 수 있도록 사용자 그룹을 선택 하는 옵션을 사용 하 여 사용자의 목록을 지정 하는 '선택' 옵션을 바꾸었습니다. 선택한 개별 사용자에 대해 사용 하도록 설정 하는 LinkedIn 계정 연결에 없는 경우에 아무 작업도 수행할 필요가 없습니다. 이전에 선택한 개별 사용자에 대해 LinkedIn 계정 연결을 설정한 경우 다음을 수행 해야 합니다.

1. 개별 사용자의 현재 목록을 가져옵니다.
1. 현재 활성화 된 개별 사용자를 그룹으로 이동
1. Azure AD 관리 센터에서 설정 하는 LinkedIn 계정 연결에서 선택한 그룹으로 이전 그룹을 사용 합니다.

> [!NOTE]
> 그룹에 현재 선택 된 개별 사용자를 이동 하지 않습니다, 경우에 여전히 Microsoft 앱에서 LinkedIn 정보를 볼 수 있습니다.

### <a name="get-the-current-list-of-selected-users"></a>선택한 사용자의 현재 목록을 가져옵니다.

1. 관리자 계정으로 Microsoft 365에 로그인 합니다.
1. https://linkedinselectedusermigration.azurewebsites.net/로 이동합니다. LinkedIn 계정 연결에 대해 선택한 사용자의 목록이 표시 됩니다.
1. 목록을 CSV 파일로 내보냅니다.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>현재 선택된 된 개별 사용자를 그룹으로 이동

1. PowerShell 시작
1. 실행 하 여 Azure AD 모듈 설치 `Install-Module AzureAD`
1. 다음 스크립트를 실행합니다.

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

2 단계에서 그룹을 사용 하 여 Azure AD 관리 센터에서 설정 하는 LinkedIn 계정 연결에서 선택한 그룹으로, 참조 [Azure portal에서 사용 하도록 설정 하는 LinkedIn 계정 연결](#enable-linkedin-account-connections-in-the-azure-portal)합니다.

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>그룹 정책을 사용 하 여 LinkedIn 계정 연결을 사용 하도록 설정

1. [Office 2016 관리 템플릿 파일(ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) 다운로드
1. **ADMX** 파일을 추출하고 중앙 저장소에 복사합니다.
1. 그룹 정책 관리를 엽니다.
1. 사용자 구성관리 템플릿Microsoft Office 2016기타Office 애플리케이션에 LinkedIn 기능 표시 설정을 사용하여 그룹 정책 개체를 만듭니다.
1. **사용** 또는 **사용 안 함**을 선택합니다.
  
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
