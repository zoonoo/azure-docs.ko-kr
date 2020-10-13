---
title: '빠른 시작: Microsoft ID 플랫폼에서 등록된 앱 제거 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Microsoft ID 플랫폼에 등록된 애플리케이션을 제거하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 0a0150112602cd34168f64132785faf1f8c33f62
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91612390"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>빠른 시작: Microsoft ID 플랫폼에 등록된 애플리케이션 제거

Microsoft ID 플랫폼에 애플리케이션을 등록한 Enterprise 개발자 및 SaaS(Software-as-a-Service) 공급자는 애플리케이션 등록을 제거해야 할 수 있습니다.

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

* 사용자 또는 해당 조직이 작성한 애플리케이션 제거
* 다른 조직이 작성한 애플리케이션 제거

## <a name="prerequisites"></a>사전 요구 사항

* [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>사용자 또는 해당 조직이 작성한 애플리케이션 제거

사용자 또는 해당 조직이 등록한 애플리케이션은 테넌트에서 애플리케이션 개체와 서비스 주체 개체 모두로 표시됩니다. 자세한 내용은 [애플리케이션 개체 및 서비스 사용자 개체](./app-objects-and-service-principals.md)를 참조하세요.

애플리케이션을 삭제하려면 사용자가 애플리케이션의 소유자 목록에 있거나 관리 권한이 있어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택한 다음, **앱 등록**을 선택합니다. 구성하려는 애플리케이션을 찾아 선택합니다. 앱을 선택하면 애플리케이션의 **개요** 페이지가 나타납니다.
1. **개요** 페이지에서 **삭제**를 선택합니다.
1. **예**를 선택하여 앱 삭제를 확인합니다.

## <a name="remove-an-application-authored-by-another-organization"></a>다른 조직이 작성한 애플리케이션 제거

테넌트의 컨텍스트에서 **앱 등록**을 볼 경우 **모든 앱** 탭에 표시되는 애플리케이션의 하위 집합이 다른 테넌트의 것이고 동의 프로세스 중 해당 테넌트에 등록된 것입니다. 구체적으로 말하면 해당하는 애플리케이션 개체 없이 테넌트의 서비스 주체 개체에 의해서만 제공됩니다. 애플리케이션 및 서비스 주체 개체의 차이에 대한 자세한 내용은 [Azure AD의 애플리케이션 및 서비스 주체 개체](./app-objects-and-service-principals.md)를 참조하세요.

(동의를 표시한 후에) 자사의 디렉토리에 대한 애플리케이션 액세스 권한을 제거하려면 회사 관리자는 해당 서비스 주체를 제거해야 합니다. 관리자에게는 글로벌 관리자 액세스 권한이 있어야 하고 Azure Portal을 통해 액세스 권한을 제거하거나 [Azure AD PowerShell Cmdlet](https://go.microsoft.com/fwlink/?LinkId=294151)을 사용하여 애플리케이션을 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼의 애플리케이션 및 서비스 주체 개체에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)