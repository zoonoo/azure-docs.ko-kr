---
title: LinkedIn 서비스 조직-Azure Active Directory에 대 한 동의 | Microsoft Docs
description: Azure Active Directory에서 Microsoft 앱용 LinkedIn 통합을 사용하거나 사용하지 않도록 설정하는 방법에 대해 설명합니다.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199701"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>Azure Active Directory 조직에 대 한 LinkedIn 통합에 동의

이 문서에서는 Azure Active Directory (Azure AD) 관리 센터에서 조직의 LinkedIn 통합을 사용 하지 않도록 설정 하거나 사용 하는 방법을 알아보십시오.

> [!IMPORTANT]
> LinkedIn 통합 설정은 현재 롤아웃하는 Azure AD 조직에 있습니다. 조직에 롤아웃 된, 기본적으로 활성화 됩니다.
> 
> 예외:
> * Microsoft Cloud for US Government, Microsoft Cloud Germany 또는 중국에서 21Vianet이 운영하는 Azure 및 Office 365를 사용하는 고객은 이 설정을 사용할 수 없습니다.
> * 독일에서 프로비전된 테넌트에 대해서는 설정이 기본적으로 꺼져 있습니다. Microsoft Cloud Germany를 사용하는 고객은 이 설정을 사용할 수 없습니다.
> * 프랑스에서 프로비전된 테넌트에 대해서는 설정이 기본적으로 꺼져 있습니다.
>
> 통합 사용 하도록 설정한 경우에 작동 *고* 사용자 본인을 대신해 회사 데이터에 액세스 하는 앱에 동의한 후 합니다. 사용자 동의 설정에 대 한 자세한 내용은 [응용 프로그램에 대 한 사용자의 액세스를 제거 하는 방법](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)합니다.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Azure Portal에서 사용자에 대한 LinkedIn 통합 사용 설정 또는 해제

전체 테넌트 또는 테넌트에서 선택한 사용자에 대해서만 LinkedIn 통합을 사용하거나 사용하지 않도록 설정할 수 있습니다.

1. Azure AD 테넌트의 전역 관리자인 계정으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에 로그인합니다.
2. **사용자**를 선택합니다.
3. **사용자** 블레이드에서 **사용자 설정**을 선택합니다.
4. **LinkedIn 계정 연결**에서:

   * 선택 **예** 일부 Microsoft 앱 내에서 LinkedIn 연락처에 액세스 하는 자신의 계정을 연결 하는 조직의 모든 사용자에 대 한 동의.
   * 선택 **선택한** 만 선택한 사용자가 조직에 연결할 일부 Microsoft 앱 내에서 LinkedIn 연락처에 액세스 하는 계정에 대 한 동의.
   * 선택 **No** 일부 Microsoft 앱 내에서 LinkedIn 연락처에 액세스 하는 계정에 연결할 사용자 조직에 대 한 동의 철회 하 합니다.
    ![조직에서 LinkedIn 통합 사용](./media/linkedin-integration/linkedin-integration.png)
5. 완료되면 **저장**을 선택하여 설정을 저장합니다.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>그룹 정책에서 사용자에 대한 LinkedIn 통합 사용 설정 또는 해제

1. [Office 2016 관리 템플릿 파일(ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) 다운로드
2. **ADMX** 파일을 추출하고 중앙 저장소에 복사합니다.
3. 그룹 정책 관리를 엽니다.
4. 사용자 구성관리 템플릿Microsoft Office 2016기타Office 애플리케이션에 LinkedIn 기능 표시 설정을 사용하여 그룹 정책 개체를 만듭니다.
5. **사용** 또는 **사용 안 함**을 선택합니다.
  
   시스템 상태 | 결과
   ------ | ------
   **사용** | Office 2016 옵션의 **Office 응용 프로그램에 LinkedIn 기능 표시** 설정이 사용으로 설정됩니다. 조직 내 사용자가 Office 애플리케이션에서 LinkedIn 기능을 사용할 수 있습니다.
   **사용 안 함** | Office 2016 옵션의 **Office 응용 프로그램에 LinkedIn 기능 표시** 설정이 사용 안 함으로 설정되며, 최종 사용자가 이 설정을 변경할 수 없습니다. 조직 내 사용자가 Office 2016 애플리케이션에서 LinkedIn 기능을 사용할 수 있습니다.

이 그룹 정책은 로컬 컴퓨터용 Office 2016 앱에만 영향을 줍니다. 사용자가 Office 2016 앱에서 LinkedIn을 해제하더라도 Office 365 전체의 카드 프로필에 LinkedIn 기능이 표시됩니다.

## <a name="learn-more"></a>자세한 정보

* [조직에 LinkedIn 통합](linkedin-user-consent.md)

* [Microsoft 앱의 LinkedIn 정보 및 기능](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 도움말 센터](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>다음 단계

다음 링크를 사용하여 Azure Portal에서 현재 LinkedIn 통합 설정을 살펴봅니다.

[Azure Portal에서 현재 LinkedIn 통합 설정 보기](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
