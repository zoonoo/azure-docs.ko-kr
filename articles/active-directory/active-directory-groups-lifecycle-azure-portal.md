---
title: "Azure Active Directory에서 Office 365 그룹 만료 미리 보기 | Microsoft Docs"
description: "Azure Active Directory에서 Office 365 그룹에 대한 만료를 설정하는 방법(미리 보기)"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8a43df84fd050d7b4bd8d937b8c55e744cb805d3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2017
---
# <a name="configure-office-365-groups-expiration-preview"></a>Office 365 그룹 만료 구성(미리 보기)

이제 선택하는 모든 Office 365 그룹에 대한 만료를 설정하여 Office 365 그룹의 수명 주기를 관리할 수 있습니다. 이 만료가 설정되면 해당 그룹의 소유자는 그룹이 여전히 필요한 경우 해당 그룹을 갱신할지 묻는 메시지를 받습니다. 갱신되지 않은 Office 365 그룹은 삭제됩니다. 삭제된 Office 365 그룹은 그룹 소유자 또는 관리자에 의해 30일 이내로 복원될 수 있습니다.  


> [!NOTE]
> Office 365 그룹에 대해서만 만료를 설정할 수 있습니다.
>
> O365 그룹에 대한 만료 설정에는 할당된 Azure AD Premium 라이선스가 필요합니다.
>   - 테넌트에 대한 만료 설정을 구성하는 관리자
>   - 이 설정에 대해 선택한 그룹의 모든 멤버

## <a name="set-office-365-groups-expiration"></a>Office 365 그룹 만료 설정

1. Azure AD 테넌트에서 전역 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)를 엽니다.

2. Azure AD를 열고 **사용자 및 그룹**을 선택합니다.

3. **그룹 설정**을 선택한 다음 **만료**를 선택하여 만료 설정을 엽니다.
  
  ![만료 블레이드](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. **만료** 블레이드에서 다음을 수행할 수 있습니다.

  * 일 단위로 그룹 수명을 설정합니다. 미리 설정된 값 중 하나 또는 사용자 지정 값을 선택할 수 있습니다(31일 이상이어야 함). 
  * 그룹에 소유자가 없는 경우 갱신 및 만료 알림이 전송되어야 하는 전자 메일 주소를 지정합니다. 
  * 만료되는 Office 365 그룹을 선택합니다. **모든** Office 365 그룹에 대한 만료를 설정할 수 있고, Office 365 그룹 중에서 선택하거나 모든 그룹에 대해 만료를 비활성화하도록 선택하지 **않을** 수 있습니다.
  * 완료되면 **저장**을 선택하여 설정을 저장합니다.

PowerShell 통해 Office 365 그룹에 대한 만료를 구성하는 Microsoft PowerShell 모듈을 다운로드하고 설치하는 방법에 대한 지침은 [Azure Active Directory V2 PowerShell 모듈 - 공개 미리 보기 릴리스 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)을 참조하세요.

이와 같은 전자 메일 알림은 그룹의 만료 30일, 15일 및 1일 전에 Office 365 그룹 소유자에게 전송됩니다.

![만료 전자 메일 알림](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

그룹 소유자는 **그룹 갱신**을 선택하여 Office 365 그룹을 갱신하거나 **그룹으로 이동**을 선택하여 구성원과 그룹에 대한 기타 세부 정보를 볼 수 있습니다.

그룹이 만료되면 그룹은 만료 날짜 1일 후에 삭제됩니다. Office 365 그룹의 만료 및 후속 삭제에 대해 알려주는 이와 같은 전자 메일 알림은 Office 365 그룹 소유자에게 전송됩니다.

![그룹 삭제 전자 메일 알림](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

[Azure Active Directory에서 삭제된 Office 365 그룹 복원](https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal)에서 설명된 대로 **그룹 복원**을 선택하거나 PowerShell cmdlet을 사용하여 그룹을 복원할 수 있습니다.
    
복원하는 그룹에 문서, SharePoint 사이트 또는 기타 영구 개체가 포함된 경우 그룹 및 해당 내용을 완전히 복원하는 데 최대 24시간이 걸릴 수 있습니다.

> [!NOTE]
> * 만료 설정을 배포하는 경우 만료 기간보다 오래된 일부 그룹이 있을 수 있습니다. 이러한 그룹은 즉시 삭제되지 않지만 만료까지 30일로 설정됩니다. 하루 이내로 첫 번째 갱신 알림 전자 메일이 전송됩니다. 예를 들어 그룹 A가 400일 전에 만들어졌으며 만료 기간은 180일로 설정됩니다. 만료 설정을 적용하면 그룹 A는 소유자가 갱신하지 않는 한 삭제되기 전에 30일이 있습니다.
> * 동적 그룹이 삭제되고 복원되는 경우 새 그룹으로 표시되며 규칙에 따라 다시 채워집니다. 이 프로세스는 최대 24시간까지 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
이러한 문서는 Azure AD 그룹에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](active-directory-groups-view-azure-portal.md)
* [그룹의 설정 관리](active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 관리](active-directory-groups-members-azure-portal.md)
* [그룹의 멤버 자격 관리](active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](active-directory-groups-dynamic-membership-azure-portal.md)
