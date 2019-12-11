---
title: Azure AD의 사용자 환경에서 응용 프로그램 숨기기
description: Azure Active Directory access 패널 또는 Office 365 시작 관리자의 사용자 환경에서 응용 프로그램을 숨기는 방법입니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: be29f51771e24c67a8cd99a81e6a69be830dacb8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970638"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Azure Active Directory에서 최종 사용자의 응용 프로그램 숨기기

최종 사용자의 MyApps 패널 또는 Office 365 시작 관리자에서 응용 프로그램을 숨기는 방법에 대 한 지침입니다. 응용 프로그램을 숨기면 사용자에 게 응용 프로그램에 대 한 권한이 계속 부여 됩니다. 

## <a name="prerequisites"></a>Előfeltételek

MyApps 패널 및 Office 365 시작 관리자에서 응용 프로그램을 숨기려면 응용 프로그램 관리자 권한이 필요 합니다.

모든 Office 365 응용 프로그램을 숨기려면 전역 관리자 권한이 필요 합니다.


## <a name="hide-an-application-from-the-end-user"></a>최종 사용자에 게 응용 프로그램 숨기기
MyApps 패널 및 Office 365 응용 프로그램 시작 관리자에서 응용 프로그램을 숨기려면 다음 단계를 사용 합니다.

1.  디렉터리에 대 한 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
2.  Válassza az **Azure Active Directory** elemet.
3.  **엔터프라이즈 응용 프로그램**을 선택 합니다. **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 블레이드가 열립니다.
4.  **응용 프로그램 종류**아래에서 **엔터프라이즈 응용 프로그램**을 선택 합니다 (아직 선택 하지 않은 경우).
5.  숨기려는 응용 프로그램을 검색 하 고 응용 프로그램을 클릭 합니다.  응용 프로그램의 개요가 열립니다.
6.  Kattintson a **Tulajdonságok** elemre. 
7.  **사용자에 게 표시 되나요?** 질문에서 **아니요**를 클릭 합니다.
8.  Kattintson a **Save** (Mentés) gombra.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD PowerShell을 사용 하 여 응용 프로그램 숨기기

MyApps 패널에서 응용 프로그램을 숨기려면 응용 프로그램의 서비스 주체에 HideApp 태그를 수동으로 추가할 수 있습니다. 다음 [AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) 명령을 실행 하 여 **사용자에 게 표시 되** 는 응용 프로그램을 설정 합니다. 속성을 **아니요**로 설정 합니다. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags.Add("HideApp")
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>MyApps 패널에서 Office 365 응용 프로그램 숨기기

다음 단계를 사용 하 여 MyApps 패널에서 모든 Office 365 응용 프로그램을 숨깁니다. 응용 프로그램은 Office 365 포털에 계속 표시 됩니다.

1.  디렉터리에 대 한 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
2.  Válassza az **Azure Active Directory** elemet.
3.  Válassza a **Felhasználói beállítások** elemet.
4.  **엔터프라이즈 응용 프로그램**에서 **최종 사용자가 시작 하는 방법 관리를 클릭 하 고 응용 프로그램을 봅니다.**
5.  **사용자가 office 365 포털에서 office 365 앱만 볼 수 있도록 하려면** **예**를 클릭 합니다.
6.  Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>Következő lépések
* [모든 내 그룹 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](remove-user-or-group-access-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](change-name-or-logo-portal.md)

