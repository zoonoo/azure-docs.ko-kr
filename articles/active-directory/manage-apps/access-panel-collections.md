---
title: Azure Active Directory에서 내 앱 포털에 대한 컬렉션 만들기 | 마이크로 소프트 문서
description: 내 앱 컬렉션을 사용하여 최종 사용자가 내 앱 환경을 더 간단하게 사용할 수 있도록 내 앱 페이지를 사용자 지정합니다. 별도의 탭을 통해 응용 프로그램을 그룹으로 구성합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120090"
---
# <a name="create-collections-on-the-my-apps-portal"></a>내 앱 포털에서 컬렉션 만들기

사용자는 내 앱 포털을 사용하여 액세스 권한이 있는 클라우드 기반 응용 프로그램을 보고 시작할 수 있습니다. 기본적으로 사용자가 액세스할 수 있는 모든 응용 프로그램은 단일 페이지에 함께 나열됩니다. 사용자를 위해 이 페이지를 더 잘 구성하려면 Azure AD Premium P1 또는 P2 라이선스가 있는 경우 컬렉션을 설정할 수 있습니다. 컬렉션을 사용하면 관련된 응용 프로그램(예: 작업 역할, 작업 또는 프로젝트)을 함께 그룹화하고 별도의 탭에 표시할 수 있습니다. 컬렉션은 기본적으로 사용자가 이미 액세스할 수 있는 응용 프로그램에 필터를 적용하므로 사용자는 컬렉션에 할당된 응용 프로그램만 볼 수 있습니다.

> [!NOTE]
> 이 문서에서는 관리자가 컬렉션을 활성화하고 만드는 방법을 설명합니다. 최종 사용자가 내 앱 포털 및 컬렉션을 사용하는 방법에 대한 자세한 내용은 [액세스 및 컬렉션 사용](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)을 참조하십시오.

## <a name="enable-the-latest-my-apps-features"></a>최신 내 앱 기능 사용

1. Azure [**포털을**](https://portal.azure.com/) 열고 사용자 관리자 또는 글로벌 관리자로 로그인합니다.

2. Azure **Active 디렉터리** > **사용자 설정으로**이동합니다.

3. **사용자 기능 미리 보기에서**사용자 기능 미리 보기 설정 **관리를**선택합니다.

4. **사용자가 내 앱에 대한 미리 보기 기능을 사용할 수**있습니다 에서 다음 옵션 중 하나를 선택합니다.
   * **선택됨** - 특정 그룹에 대한 피쳐를 활성화합니다. 그룹 **선택** 옵션을 사용하여 피처를 활성화할 그룹을 선택합니다.  
   * **모두** - 모든 사용자에 대한 기능을 활성화합니다.

> [!NOTE]
> 내 앱 포털을 열려면 사용자는 `https://myapps.microsoft.com` 에 대한 링크 또는 사용자 `https://myapps.microsoft.com/contoso.com`지정된 링크를 사용할 수 있습니다. 새 내 앱 환경을 사용하도록 설정하면 **업데이트된 내 응용 프로그램 환경이 내** 앱 페이지 상단에 표시되고 사용자는 새 환경을 보려면 **시도를** 선택할 수 있습니다. 새 환경 사용을 중지하려면 페이지 상단의 **새 환경** 배너에서 **예를** 선택할 수 있습니다.

## <a name="create-a-collection"></a>컬렉션 만들기

컬렉션을 만들려면 Azure AD 프리미엄 P1 또는 P2 라이선스가 있어야 합니다.

1. Azure [**포털을**](https://portal.azure.com/) 열고 Azure AD 프리미엄 P1 또는 P2 라이선스를 사용 하 여 관리자로 로그인 합니다.

2. Azure **Active 디렉터리** > **엔터프라이즈 응용 프로그램으로 이동합니다.**

3. **에서 관리** **모음을**선택 합니다.

4. **새 컬렉션을**선택합니다. 새 **컬렉션** 페이지에서 컬렉션의 **이름을** 입력합니다(이름에 "컬렉션"을 사용하지 않는 것이 좋습니다. 그런 다음 **설명을**입력합니다.

   ![새 컬렉션 페이지](media/acces-panel-collections/new-collection.png)

5. 응용 **프로그램 탭을** 선택합니다. **+ 응용 프로그램 추가**를 선택한 다음 응용 프로그램 **추가** 페이지에서 컬렉션에 추가할 모든 응용 프로그램을 선택하거나 **검색** 상자를 사용하여 응용 프로그램을 찾습니다.

   ![컬렉션에 응용 프로그램 추가](media/acces-panel-collections/add-applications.png)

6. 응용 프로그램 추가가 완료되면 **에 추가를**선택합니다. 선택한 응용 프로그램 목록이 나타납니다. 위쪽 화살표를 사용하여 목록에서 응용 프로그램의 순서를 변경할 수 있습니다. 응용 프로그램을 아래로 이동하거나 컬렉션에서 삭제하려면 **더 보기** 메뉴(...)를 선택합니다.**...**

7. **소유자** 탭을 선택합니다. **+ 사용자 및 그룹 추가**를 선택한 다음 사용자 및 그룹 **추가** 페이지에서 소유권을 할당할 사용자 또는 그룹을 선택합니다. 사용자와 그룹 선택을 마치면 **선택**을 선택합니다.

9. 사용자 **및 그룹** 탭을 선택합니다. **+ 사용자 및 그룹 추가**를 선택한 다음 사용자 및 그룹 **추가** 페이지에서 컬렉션을 할당할 사용자 또는 그룹을 선택합니다. 또는 **검색** 상자를 사용하여 사용자 또는 그룹을 찾습니다. 사용자와 그룹 선택을 마치면 **선택**을 선택합니다.

   ![사용자 및 그룹 추가](media/acces-panel-collections/add-users-and-groups.png)

11. **검토 + 만들기를**선택합니다. 새 컬렉션의 속성이 나타납니다.


## <a name="view-audit-logs"></a>감사 로그 보기

감사 로그는 컬렉션 생성 최종 사용자 작업을 포함하여 내 앱 컬렉션 작업을 기록합니다. 내 앱에서 다음 이벤트가 생성됩니다.

* 컬렉션 만들기
* 컬렉션 편집
* 컬렉션 삭제
* 응용 프로그램 실행(최종 사용자)
* 셀프 서비스 응용 프로그램 추가(최종 사용자)
* 셀프 서비스 응용 프로그램 삭제(최종 사용자)

활동 섹션에서 **Azure Active Directory** > **엔터프라이즈 응용 프로그램** > **감사 로그를** 선택하여 Azure [포털에서](https://portal.azure.com) 감사 로그에 액세스할 수 있습니다. **서비스의**경우 **내 앱을**선택합니다.

## <a name="get-support-for-my-account-pages"></a>내 계정 페이지에 대한 지원 받기

내 앱 페이지에서 사용자는 내 **계정** > **보기를** 선택하여 계정 설정을 열 수 있습니다. Azure AD **내 계정** 페이지에서 사용자는 보안 정보, 장치, 암호 등을 관리할 수 있습니다. 또한 Office 계정 설정에 액세스할 수도 있습니다.

Azure AD 계정 페이지 또는 Office 계정 페이지의 문제에 대한 지원 요청을 제출해야 하는 경우 요청이 올바르게 라우팅되도록 다음 단계를 따르십시오. 

* **Azure AD "내 계정"** 페이지에 문제가 있는 경우 Azure 포털 내에서 지원 요청을 엽니다. Azure **포털** > **Azure Active Directory** > 새 지원**요청으로**이동합니다.

* **Office "내 계정"** 페이지에 문제가 있는 경우 Microsoft 365 관리 센터 내에서 지원 요청을 엽니다. Microsoft **365 관리 센터** > **지원으로**이동합니다. 

## <a name="next-steps"></a>다음 단계
[Azure Active Directory에서 애플리케이션에 대한 최종 사용자 환경](end-user-experiences.md)