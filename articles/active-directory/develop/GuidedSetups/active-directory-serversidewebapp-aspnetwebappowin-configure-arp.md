---
title: "Azure AD v2 ASP.NET 웹 서버 시작 - 구성 | Microsoft Docs"
description: "OpenID Connect 표준을 사용하여 기존 웹 브라우저 기반 응용 프로그램을 사용하는 ASP.NET 솔루션에서 Microsoft 로그인 구현"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8a1650a65e7980f4a13fa4edc7918b0099bb5464
ms.contentlocale: ko-kr
ms.lasthandoff: 07/08/2017


---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>응용 프로그램의 등록 정보를 사용하여 ASP.NET 웹앱 구성

이 단계에서는 SSL을 사용한 다음 SSL URL을 사용하여 응용 프로그램 등록 정보를 구성하도록 프로젝트를 구성합니다. 그런 다음 *web.config*를 통해 솔루션에 응용 프로그램 등록 정보를 추가합니다.

1.  솔루션 탐색기에서 프로젝트를 선택하고 `Properties` 창을 확인합니다([속성] 창이 보이지 않으면 F4 누르기).
2.  `SSL Enabled`를 `True`로 변경
3.  위의 `SSL URL`에서 값을 복사해 이 페이지 맨 위에 있는 `Redirect URL` 필드에 붙여넣은 다음 [업데이트]를 클릭합니다.<br/><br/>![](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
4.  다음 내용을 루트 폴더에 있는 `web.config` 파일의 `configuration\appSettings` 섹션 아래에 추가합니다.

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```

