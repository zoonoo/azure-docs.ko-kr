---
title: 포함 파일
description: 기밀 클라이언트 시나리오 방문 페이지 (디먼, 웹 앱, web API)에 대 한 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89436473"
---
## <a name="add-a-client-secret-or-certificate"></a>클라이언트 암호 또는 인증서 추가

모든 기밀 클라이언트 응용 프로그램에서와 마찬가지로 사용자 상호 작용 없이 자신으로 인증할 수 있도록 해당 응용 프로그램의 *자격 증명* 으로 사용할 비밀 또는 인증서를 추가 해야 합니다.

[Azure Portal](#add-client-credentials-by-using-the-azure-portal) 또는 [PowerShell](#add-client-credentials-by-using-powershell)과 같은 명령줄 도구를 사용 하 여 클라이언트 앱의 등록에 자격 증명을 추가할 수 있습니다.

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 클라이언트 자격 증명 추가

기밀 클라이언트 응용 프로그램의 앱 등록에 자격 증명을 추가 하려면 빠른 시작: 추가 하려는 자격 증명 형식에 대 한 [Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](../articles/active-directory/develop/quickstart-register-app.md) 의 단계를 따르세요.

* [클라이언트 암호 추가](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [인증서 추가](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>PowerShell을 사용 하 여 클라이언트 자격 증명 추가

또는 PowerShell을 사용 하 여 Microsoft id 플랫폼에 응용 프로그램을 등록할 때 자격 증명을 추가할 수 있습니다.

GitHub의 [dotnetcore](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 코드 샘플에서는 응용 프로그램을 등록할 때 응용 프로그램 비밀 또는 인증서를 추가 하는 방법을 보여 줍니다.

- PowerShell을 사용 하 여 **클라이언트 암호** 를 추가 하는 방법에 대 한 자세한 내용은 [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)를 참조 하세요.
- PowerShell을 사용 하 여 **인증서** 를 추가 하는 방법에 대 한 자세한 내용은 [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)를 참조 하세요.
