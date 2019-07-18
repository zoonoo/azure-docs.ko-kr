---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182379"
---
## <a name="register-your-application"></a>애플리케이션 등록

두 가지 방법 중 하나로 애플리케이션을 등록할 수 있습니다.

### <a name="option-1-express-mode"></a>옵션 1: 기본 모드

다음을 수행하여 애플리케이션을 신속하게 등록할 수 있습니다.
1. [Azure Portal - 애플리케이션 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)으로 이동합니다.
1. 애플리케이션 이름을 입력하고 **등록**을 선택합니다.
1. 지침에 따라 클릭 한 번으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.

### <a name="option-2-advanced-mode"></a>옵션 2: 고급 모드

애플리케이션을 등록하고 애플리케이션 등록 정보를 솔루션에 추가하려면 다음을 수행합니다.
1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **새 등록**을 선택합니다.
   - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `Win-App-calling-MsGraph`)을 입력합니다.
   - **지원되는 계정 유형** 섹션에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)** 을 선택합니다.
   - **등록**을 선택하여 애플리케이션을 만듭니다.
1. 앱의 페이지 목록에서 **인증**을 선택합니다.
   1. **리디렉션 URI** 섹션의 리디렉션 URI 목록에서 다음을 수행합니다.
   1. **TYPE** 열에서 **공용 클라이언트(모바일 및 데스크톱)** 를 선택합니다.
   1. **리디렉션 URI** 열에 `urn:ietf:wg:oauth:2.0:oob`를 입력합니다.
1. **저장**을 선택합니다.
1. Visual Studio로 이동하고, *App.xaml.cs* 파일을 열고, `Enter_the_Application_Id_here`을 방금 등록하고 복사한 애플리케이션 ID로 바꿉니다.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
