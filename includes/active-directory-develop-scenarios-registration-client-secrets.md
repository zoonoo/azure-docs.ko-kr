---
title: 포함 파일
description: 기밀 클라이언트 시나리오 방문 페이지(데몬, 웹 앱, 웹 API)에 대한 파일 포함
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
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773372"
---
## <a name="register-secrets-or-certificates"></a>비밀 또는 인증서 등록

모든 기밀 클라이언트 응용 프로그램에 관해서는 비밀 또는 인증서를 등록해야 합니다. [Azure 포털의](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) 대화형 환경을 사용하거나 PowerShell과 같은 명령줄 도구를 사용하여 응용 프로그램 비밀을 등록할 수 있습니다.

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>응용 프로그램 등록 포털을 사용하여 클라이언트 암호 등록

클라이언트 자격 증명 관리는 응용 프로그램의 **인증서 & 비밀** 페이지에서 수행됩니다.

![인증서 & 비밀 페이지](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- 응용 프로그램 보안(클라이언트 보안이라고도 이름)은 기밀 클라이언트 응용 프로그램을 등록하는 동안 Azure AD에서 생성됩니다. 이 세대는 새 클라이언트 보안 을 선택할 때 발생 **합니다.** 이 때 **에 저장을**선택하기 전에 앱에서 사용할 비밀 문자열을 클립보드에 복사해야 합니다. 이 문자열은 더 이상 표시되지 않습니다.
- 응용 프로그램 등록 중에 **인증서 업로드** 단추를 사용하여 인증서를 업로드합니다. Azure AD는 응용 프로그램에 직접 등록되고 인증서 체인을 따르지 않는 인증서만 지원합니다.

자세한 내용은 [빠른 시작: 웹 API에 액세스하도록 클라이언트 응용 프로그램 구성 | 응용 프로그램에 자격 증명을 추가합니다.](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="register-client-secrets-by-using-powershell"></a>PowerShell을 사용하여 클라이언트 비밀 등록

또는 명령줄 도구를 사용하여 Azure AD에 응용 프로그램을 등록할 수 있습니다. [활성 디렉터리-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 샘플은 Azure AD 응용 프로그램에 응용 프로그램 보안 또는 인증서를 등록하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 있습니다.

- 응용 프로그램 비밀을 등록하는 방법에 대한 자세한 내용은 [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)을 참조하십시오.
- 응용 프로그램에 인증서를 등록하는 방법에 대한 자세한 내용은 [AppCreationScripts-withCert/Configure.ps1을](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)참조하십시오.
