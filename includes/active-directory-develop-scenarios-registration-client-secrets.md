---
title: 포함 파일
description: 기밀 클라이언트 시나리오 방문 페이지 (디먼, 웹 앱, web API)에 대 한 포함 파일
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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102837"
---
## <a name="register-secrets-or-certificates"></a>비밀 또는 인증서 등록

모든 기밀 클라이언트 응용 프로그램의 경우에는 비밀 또는 인증서를 등록 해야 합니다. [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) 의 대화형 경험을 통해 또는 명령줄 도구 (예: PowerShell)를 사용 하 여 응용 프로그램 암호를 등록할 수 있습니다.

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>응용 프로그램 등록 포털을 사용 하 여 클라이언트 암호 등록

클라이언트 자격 증명의 관리는 응용 프로그램의 **인증서 & 암호** 페이지에서 발생 합니다.

![인증서 & 암호 페이지](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Azure Portal의 앱 등록에서 **새 클라이언트 암호** 를 선택 하 여 *클라이언트 암호* 를 만듭니다. 클라이언트 암호를 만들 때 **인증서 & 암호** 창에서 이동 하기 전에 암호의 문자열을 기록해 _야_ 합니다. 비밀의 문자열은 다시 표시 되지 않습니다.
- 응용 프로그램을 등록 하는 동안 **인증서 업로드** 단추를 사용 하 여 인증서를 업로드 합니다. Azure AD는 응용 프로그램에 직접 등록 된 인증서만 지원 하며 인증서 체인을 따르지 않습니다.

자세한 내용은 빠른 시작 [: 웹 api에 액세스 하는 클라이언트 응용 프로그램 구성 | 응용 프로그램에 자격 증명을 추가](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)합니다.

### <a name="register-client-secrets-by-using-powershell"></a>PowerShell을 사용 하 여 클라이언트 암호 등록

또는 명령줄 도구를 사용 하 여 Azure AD에 응용 프로그램을 등록할 수 있습니다. [Dotnetcore-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 샘플은 Azure AD 응용 프로그램을 사용 하 여 응용 프로그램 비밀 또는 인증서를 등록 하는 방법을 보여 줍니다.

- 응용 프로그램 암호를 등록 하는 방법에 대 한 자세한 내용은 [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)를 참조 하세요.
- 응용 프로그램에 인증서를 등록 하는 방법에 대 한 자세한 내용은 [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)를 참조 하세요.
