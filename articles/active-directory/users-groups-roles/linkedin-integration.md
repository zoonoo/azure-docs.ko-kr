---
title: Azure Active Directory에서 LinkedIn 연결 통합 사용 | Microsoft Docs
description: Azure Active Directory에서 Microsoft 앱용 LinkedIn 계정 연결을 사용하거나 사용하지 않도록 설정하는 방법에 대해 설명합니다.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 0eaa2656313ecd9b64503051265dc16285f0a1f3
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44492875"
---
# <a name="linkedin-account-connections"></a>LinkedIn 계정 연결

이 문서에서는 Azure AD(Azure Active Directory) 관리 센터에서 테넌트에 대해 LinkedIn 계정 연결을 사용하거나 사용하지 않도록 설정하는 방법을 알아볼 수 있습니다.

> [!IMPORTANT]
> LinkedIn 계정 연결 설정은 현재 Azure AD 테넌트에 배포되고 있습니다. 테넌트에게 공개되면 기본적으로 사용하도록 설정됩니다. 
> 
> 예외:
> * Microsoft Cloud for US Government, Microsoft Cloud Germany 또는 중국에서 21Vianet이 운영하는 Azure 및 Office 365를 사용하는 고객은 이 설정을 사용할 수 없습니다.
> * 독일에서 프로비전된 테넌트에 대해서는 설정이 기본적으로 꺼져 있습니다. Microsoft Cloud Germany를 사용하는 고객은 이 설정을 사용할 수 없습니다.
> * 프랑스에서 프로비전된 테넌트에 대해서는 설정이 기본적으로 꺼져 있습니다.

> 통합은 사용하도록 설정했으며, 사용자가 자신을 대신해서 회사 데이터에 액세스하는 앱에 동의할 수 있도록 허용한 경우에만 작동합니다. 동의 설정에 대한 자세한 내용은 [응용 프로그램에 대한 사용자 액세스를 제거하는 방법](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)을 참조하세요.

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-in-the-azure-portal"></a>Azure Portal에서 테넌트에 대한 LinkedIn 계정 연결 사용 또는 사용 안 함

전체 테넌트에 대해 또는 테넌트에서 선택한 사용자에 대해서만 LinkedIn 계정 연결을 사용하거나 사용하지 않도록 설정할 수 있습니다.

1. Azure AD 테넌트의 전역 관리자인 계정으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에 로그인합니다.
2. **사용자**를 선택합니다.
3. **사용자** 블레이드에서 **사용자 설정**을 선택합니다.
4. **LinkedIn 계정 연결**에서:
  * 테넌트의 모든 사용자에 대해 LinkedIn 계정 연결을 사용하도록 설정하려면 **예**를 선택합니다.
  * 선택한 테넌트 사용자에 대해서만 LinkedIn 계정 연결을 사용하도록 설정하려면 **선택됨**을 선택합니다.
  * 모든 사용자에 대해 LinkedIn 계정 연결을 사용하지 않도록 설정하려면 **아니요**를 선택합니다. ![LinkedIn 계정 연결을 사용하도록 설정](./media/linkedin-integration/linkedin-integration.png)
5. 완료되면 **저장**을 선택하여 설정을 저장합니다.

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-using-group-policy"></a>그룹 정책을 사용하여 테넌트에 대한 LinkedIn 계정 연결 사용 또는 사용 안 함

1. [Office 2016 관리 템플릿 파일(ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) 다운로드
2. **ADMX** 파일을 추출하고 중앙 저장소에 복사합니다.
3. 그룹 정책 관리를 엽니다.
4. **사용자 구성** > **관리 템플릿** > **Microsoft Office 2016** > **기타** > **Office 응용 프로그램에서 LinkedIn 기능 표시** 설정을 사용하여 그룹 정책 개체를 만듭니다.
5. **사용** 또는 **사용 안 함**을 선택합니다.
  
 상태 | 결과
------ | ------
**사용** | Office 2016 옵션의 **Office 응용 프로그램에 LinkedIn 기능 표시** 설정이 사용으로 설정됩니다. 조직 내 사용자가 Office 응용 프로그램에서 LinkedIn 기능을 사용할 수 있습니다.
 **사용 안 함** | Office 2016 옵션의 **Office 응용 프로그램에 LinkedIn 기능 표시** 설정이 사용 안 함으로 설정되며, 최종 사용자가 이 설정을 변경할 수 없습니다. 조직 내 사용자가 Office 2016 애플리케이션에서 LinkedIn 기능을 사용할 수 있습니다.

이 그룹 정책은 로컬 컴퓨터용 Office 2016 앱에만 영향을 줍니다. 사용자가 Office 2016 앱에서 LinkedIn을 해제하더라도 Office 365 전체의 카드 프로필에 LinkedIn 기능이 표시됩니다.

## <a name="learn-more"></a>자세한 정보

* [조직에 LinkedIn 통합](linkedin-user-consent.md)

* [Microsoft 앱의 LinkedIn 정보 및 기능](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 도움말 센터](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>다음 단계
다음 링크를 사용하여 Azure Portal에서 현재 LinkedIn 계정 연결 설정을 살펴봅니다.

[LinkedIn 계정 연결 구성](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 