---
title: Azure Active Directory에서 애플리케이션 제거
description: Azure AD(Active Directory)에서 애플리케이션을 제거하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: ddc04d32f76515cb93d2cde3fe4eccaab7e4c291
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245361"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>빠른 시작: Azure Active Directory에서 애플리케이션 제거

Azure AD(Azure Active Directory)에 애플리케이션을 등록한 Enterprise 개발자 및 SaaS(Software-as-a-Service) 공급자는 해당 Azure AD 테넌트에서 애플리케이션 등록을 제거해야 할 수 있습니다.

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

* [조직이 작성한 응용프로그램 제거](#removing-an-application-authored-by-your-organization)
* [다른 조직이 권한을 부여한 다중 테넌트 응용프로그램 제거](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>필수 조건

먼저 애플리케이션이 등록된 Azure AD 테넌트가 필요합니다.

* 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](quickstart-create-new-tenant.md).
* 테넌트에 앱을 추가하고 등록하는 방법을 알아보려면 [Azure AD에 애플리케이션 추가](quickstart-v1-integrate-apps-with-azure-ad.md)를 참조하세요.

## <a name="removing-an-application-authored-by-your-organization"></a>조직이 작성한 애플리케이션 제거

조직에서 등록한 애플리케이션은 테넌트의 기본 **앱 등록** 페이지에 있는 **내 앱** 필터 아래에 표시됩니다. 이러한 애플리케이션은 Azure Portal을 통해 수동으로 또는 PowerShell 또는 Microsoft Graph API를 통해 프로그래밍 방식으로 등록한 애플리케이션입니다. 구체적으로 말해, 애플리케이션은 테넌트에서 애플리케이션 개체와 서비스 주체 개체 둘 다로 나타냅니다. 이러한 개체에 대한 자세한 내용은 [애플리케이션 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>디렉터리에서 단일 테넌트 애플리케이션을 제거하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 계정이 둘 이상의 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽의 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록**을 차례로 선택한 다음, 구성하려는 애플리케이션을 찾아 선택합니다.
    그러면 애플리케이션의 기본 등록 페이지로 이동되고, 애플리케이션에 대한 **설정** 페이지가 열립니다.
1. 애플리케이션의 기본 등록 페이지에서 **삭제**를 선택합니다.
1. **예**를 선택하여 응용 프로그램 삭제를 확인합니다.

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>홈 디렉터리에서 다중 테넌트 애플리케이션을 제거하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 계정이 둘 이상의 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽의 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록**을 차례로 선택한 다음, 구성하려는 애플리케이션을 찾아 선택합니다.
    그러면 애플리케이션의 기본 등록 페이지로 이동되고, 애플리케이션에 대한 **설정** 페이지가 열립니다.
1. **설정** 페이지에서 **속성**을 선택하고 **다중 테넌트** 스위치를 **아니요**로 변경하여 먼저 응용 프로그램을 단일 테넌트로 변경한 다음, **저장**을 선택합니다.
    애플리케이션의 서비스 주체 개체는 이미 동의한 모든 조직의 테넌트에 남아 있습니다.
1. 애플리케이션의 기본 등록 페이지에서 **삭제**를 선택합니다.
1. **예**를 선택하여 응용 프로그램 삭제를 확인합니다.

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>다른 조직이 권한을 부여한 다중 테넌트 애플리케이션 제거

테넌트의 기본 **앱 등록** 페이지에서 **모든 앱** 필터(**내 앱** 등록 제외) 아래에 표시되는 애플리케이션 하위 집합은 다중 테넌트 애플리케이션입니다.

기술적인 측면에서 이러한 다중 테넌트 애플리케이션은 다른 테넌트에서 비롯되어 동의 프로세스 중에 테넌트에 등록되었습니다. 구체적으로 말하면 해당하는 애플리케이션 개체 없이 테넌트의 서비스 주체 개체에 의해서만 제공됩니다. 애플리케이션 개체와 서비스 주체 개체의 차이점에 대한 자세한 내용은 [Azure AD의 애플리케이션 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

(동의를 표시한 후에) 자사 디렉토리에 대한 다중 테넌트 애플리케이션의 액세스 권한을 제거하려면 회사 관리자가 애플리케이션의 서비스 주체를 제거해야 합니다. 관리자에게 전역 관리자 액세스 권한이 있어야 하고, Azure Portal 또는 [Azure AD PowerShell Cmdlet](https://go.microsoft.com/fwlink/?LinkId=294151)을 사용하여 해당 권한을 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD v1.0 엔드포인트를 사용하는 앱에 대한 다른 관련된 앱 관리 빠른 시작에 대해 알아봅니다.

- [Azure AD에 응용 프로그램 추가](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Azure AD의 응용 프로그램 업데이트](quickstart-v1-update-azure-ad-app.md)
