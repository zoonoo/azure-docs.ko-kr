---
title: 포함 파일
description: 기밀 클라이언트 시나리오 방문 페이지 (디먼, 웹 앱, 웹 API)에 대 한 파일 포함
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
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182385"
---
## <a name="registration-of-secrets-or-certificates"></a>암호 또는 인증서 등록

와 같은 기밀 클라이언트 응용 프로그램의 경우 해야 암호 또는 인증서를 등록 합니다. 대화형 환경을 통해 응용 프로그램에서 비밀을 등록할 수 있습니다 합니다 [Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), 또는 명령줄 도구 (예: PowerShell)를 사용 하 여

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>응용 프로그램 등록 포털을 사용 하 여 등록 클라이언트 암호

클라이언트 자격 증명의 관리를 진행 합니다 **인증서 및 비밀** 응용 프로그램에 대 한 페이지:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- 응용 프로그램 암호 (또한 명명 된 클라이언트 암호)는 기밀 클라이언트 응용 프로그램 등록 시 Azure AD를 통해 생성 됩니다. 선택 하면 발생 하는이 세대 **새 클라이언트 암호**합니다. 이 시점에서 복사 해야 합니다 암호 문자열 사용에 대 한 클립보드에 앱을 선택 하기 전에 **저장할**합니다. 이 문자열은 더 이상 표시 되지 않습니다.
- 사용 하 여 응용 프로그램 등록 인증서가 업로드 되는 **인증서 업로드** 단추

세부 정보를 참조 하세요. [빠른 시작: 웹 Api에 액세스 하는 클라이언트 응용 프로그램 구성 | 응용 프로그램에 자격 증명 추가](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>PowerShell을 사용 하는 클라이언트 암호를 등록 합니다.

또는 명령줄 도구를 사용 하 여 Azure AD를 사용 하 여 응용 프로그램을 등록할 수 있습니다. 합니다 [활성-directory-dotnetcore-데몬-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 샘플은 Azure AD 응용 프로그램을 사용 하 여 응용 프로그램 비밀 또는 인증서를 등록 하는 방법을 보여 줍니다.

- 응용 프로그램 암호를 등록 하는 방법에 대 한 자세한 내용은 참조 하세요. [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- 응용 프로그램을 사용 하 여 인증서를 등록 하는 방법에 대 한 자세한 내용은 참조 하세요. [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)