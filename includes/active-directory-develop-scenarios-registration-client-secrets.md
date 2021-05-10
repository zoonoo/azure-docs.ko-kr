---
title: 포함 파일
description: 기밀 클라이언트 시나리오 방문 페이지(디먼, 웹앱, 웹 API)에 대한 포함 파일
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996012"
---
## <a name="add-a-client-secret-or-certificate"></a>클라이언트 암호 또는 인증서 추가

모든 기밀 클라이언트 애플리케이션과 마찬가지로 해당 애플리케이션의 *자격 증명* 역할을 하는 비밀 또는 인증서를 추가해야 사용자 상호 작용 없이 자체적으로 인증할 수 있습니다.

[Azure Portal](#add-client-credentials-by-using-the-azure-portal) 또는 [PowerShell](#add-client-credentials-by-using-powershell)과 같은 명령줄 도구를 사용하여 클라이언트 앱의 등록에 자격 증명을 추가할 수 있습니다.

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Azure Portal을 사용하여 클라이언트 자격 증명 추가

기밀 클라이언트 애플리케이션의 앱 등록에 자격 증명을 추가하려면 해당 자격 증명 유형에 대한 [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](../articles/active-directory/develop/quickstart-register-app.md)의 단계를 따르세요.

* [클라이언트 암호 추가](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [인증서 추가](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>PowerShell을 사용하여 클라이언트 자격 증명 추가

또는 PowerShell을 사용하여 Microsoft ID 플랫폼에 애플리케이션을 등록할 때 자격 증명을 추가할 수 있습니다.

GitHub의 [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 코드 샘플에서는 애플리케이션을 등록할 때 애플리케이션 비밀 또는 인증서를 추가하는 방법을 보여줍니다.

- PowerShell을 사용하여 **클라이언트 암호** 를 추가하는 방법에 대한 자세한 내용은 [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)을 참조하세요.
- PowerShell을 사용하여 **인증서** 를 추가하는 방법에 대한 자세한 내용은 [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)을 참조하세요.
