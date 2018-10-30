---
title: Microsoft ID 플랫폼을 사용하여 등록된 응용 프로그램 제거 | Azure
description: Microsoft ID 플랫폼을 사용하여 등록된 응용 프로그램을 제거하는 방법을 살펴봅니다.
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
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: c10aa28dc83d96a99da40b7d95d8e7051108d4c0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988868"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform-preview"></a>Microsoft ID 플랫폼을 사용하여 등록된 응용 프로그램 제거(미리 보기)

Microsoft ID 플랫폼에 응용 프로그램을 등록한 Enterprise 개발자 및 SaaS(Software-as-a-Service) 공급자는 응용 프로그램 등록을 제거해야 할 수 있습니다.

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

* [사용자 또는 해당 조직이 작성한 응용프로그램 제거](#remove-an-application-authored-by-your-organization)
* [다른 조직이 작성한 응용프로그램 제거](#remove-an-application-authoried-by-another-organization)

## <a name="prerequisites"></a>필수 조건

시작하려면 다음과 같은 필수 구성을 완료했는지 확인합니다.

* 응용 프로그램이 등록된 테넌트가 있습니다. [Microsoft ID 플랫폼을 사용하여 응용 프로그램을 등록하는 방법](quickstart-register-app.md)을 살펴봅니다.
* Azure Portal에서 앱 등록 미리 보기 환경을 옵트인합니다. 이 빠른 시작의 단계는 새 UI에 해당하며 미리 보기 환경을 옵트인한 경우에만 작동합니다.

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>사용자 또는 해당 조직이 작성한 응용프로그램 제거

사용자 또는 해당 조직이 등록한 응용 프로그램은 테넌트에서 응용 프로그램 개체와 서비스 주체 개체 모두로 표시됩니다. 자세한 내용은 [응용 프로그램 개체 및 서비스 사용자 개체](active-directory-application-objects.md)를 참조하세요.

### <a name="to-remove-an-application"></a>응용 프로그램을 제거하려면

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
2. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
3. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록(미리 보기)** 을 차례로 선택합니다. 구성하려는 응용 프로그램을 찾아 선택합니다. 앱을 선택하면 응용 프로그램의 **개요** 페이지가 나타납니다.
4. **개요** 페이지에서 **삭제**를 선택합니다.
5. **예**를 선택하여 앱 삭제를 확인합니다.

  > [!NOTE]
  > 응용 프로그램을 삭제하려면 사용자가 응용 프로그램의 소유자 목록에 있거나 관리 권한이 있어야 합니다.

## <a name="remove-an-application-authored-by-another-organization"></a>다른 조직이 작성한 응용프로그램 제거

테넌트의 컨텍스트에서 **앱 등록**을 볼 경우 **모든 앱** 탭에 표시되는 응용 프로그램의 하위 집합이 다른 테넌트의 것이고 동의 프로세스 중 해당 테넌트에 등록된 것입니다. 구체적으로 말하면 해당하는 응용 프로그램 개체 없이 테넌트의 서비스 주체 개체에 의해서만 제공됩니다. 응용 프로그램 및 서비스 주체 개체의 차이에 대한 자세한 내용은 [Azure AD의 응용 프로그램 및 서비스 주체 개체](active-directory-application-objects.md)를 참조하세요.

(동의를 표시한 후에) 자사의 디렉토리에 대한 응용 프로그램 액세스 권한을 제거하려면 회사 관리자는 해당 서비스 주체를 제거해야 합니다. 관리자에게는 글로벌 관리자 액세스 권한이 있어야 하고 Azure Portal을 통해 액세스 권한을 제거하거나 [Azure AD PowerShell Cmdlet](http://go.microsoft.com/fwlink/?LinkId=294151)을 사용하여 응용 프로그램을 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다른 관련 앱 관리 빠른 시작에 대해 알아봅니다.

* [Microsoft ID 플랫폼을 사용하여 응용 프로그램 등록](quickstart-register-app.md)
* [웹 API에 액세스하는 클라이언트 응용 프로그램 구성](quickstart-configure-app-access-web-apis.md)
* [웹 API를 공개하는 응용 프로그램 구성](quickstart-configure-app-expose-web-apis.md)
* [응용 프로그램에서 지원되는 계정 수정](quickstart-modify-supported-accounts.md)
