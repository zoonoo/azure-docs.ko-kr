---
title: PowerShell 샘플 - Azure Active Directory 테넌트의 필수 날짜 이후에 만료되는 비밀 및 인증서가 있는 앱을 내보냅니다.
description: Azure Active Directory 테넌트의 지정된 앱에 대해 필수 날짜 후에 만료되는 비밀 및 인증서가 있는 모든 앱을 내보내는 PowerShell 예제입니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 9c0e5508830343561833785fbce31f547a8a7428
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149684"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>필수 날짜 이후에 만료되는 암호 및 인증서가 있는 앱 내보내기

이 PowerShell 스크립트 예제에서는 지정된 앱에 대해 필수 날짜 이후에 만료되는 모든 앱 등록 비밀 및 인증서를 CSV 파일의 디렉터리에서 비대화형으로 내보냅니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 비대화형으로 작동합니다. 이를 사용하는 관리자는 자체 앱 ID, 애플리케이션 비밀, 테넌트 이름, 앱 자격 증명 만료 기간 및 CSV를 내보낼 경로를 사용하여 "#변경할 매개 변수" 섹션의 값을 변경해야 합니다.
이 스크립트는 [Client_Credential Oauth 흐름](../../develop/v2-oauth2-client-creds-grant-flow.md)을 사용합니다. "RefreshToken" 함수는 관리자가 수정한 매개 변수의 값을 기반으로 액세스 토큰을 빌드합니다.

"Add-Member" 명령은 CSV 파일에서 열을 만드는 역할을 담당합니다.

| 명령 | 메모 |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1) | HTTP 및 HTTPS 요청을 웹 페이지나 웹 서비스로 보냅니다. 응답을 구문 분석하고 링크, 이미지 및 기타 중요한 HTML 요소 컬렉션을 반환합니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 관리에 대한 다른 PowerShell 예제는 [애플리케이션 관리에 대한 Azure AD PowerShell 예제](../app-management-powershell-samples.md)를 참조하세요.
