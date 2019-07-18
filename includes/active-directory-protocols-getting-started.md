---
title: Azure AD .NET 프로토콜 개요 | Microsoft Docs
description: Azure AD를 사용하여 테넌트에서 웹 애플리케이션 및 Web API에 대한 액세스 권한을 부여하기 위해 HTTP 메시지를 사용하는 방법입니다.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182376"
---
## <a name="register-your-application-with-your-ad-tenant"></a>AD 테넌트에 애플리케이션 등록
먼저, 애플리케이션을 Azure AD(Azure Active Directory) 테넌트에 등록해야 합니다. 그러면 애플리케이션에 대한 애플리케이션 ID가 제공되며 토큰을 수신하는 데 사용할 수 있습니다.

* [Azure Portal](https://portal.azure.com)에 로그인합니다.
* 페이지 오른쪽 위 모서리에서 계정을 클릭하고, **디렉터리 전환** 탐색을 클릭한 다음, 적절한 테넌트를 선택하여 Azure AD 테넌트를 선택합니다. 
  * 계정에 하나의 Azure AD 테넌트만 있거나 이미 적절한 Azure AD 테넌트를 선택한 경우 이 단계를 건너뛰세요.
* 왼쪽 탐색 창에서 **Azure Active Directory**를 클릭합니다.
* 클릭할 **앱 등록** 를 클릭 **새 등록**합니다.
* 프롬프트에 따라 새 애플리케이션을 만듭니다. 이 자습서에서는 공용 클라이언트 (모바일 및 데스크톱) 응용 프로그램 또는 웹 응용 프로그램을 든 지 상관 없지만 웹 응용 프로그램 또는 공용 클라이언트 응용 프로그램에 대 한 구체적인 예제를 하려는 경우 체크 아웃 우리의 [퀵 스타트](../articles/active-directory/develop/v1-overview.md)합니다.
  * **이름**은 애플리케이션 이름이고 최종 사용자에게 애플리케이션을 설명합니다.
  * **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
  * 제공 된 **리디렉션 URI**합니다. 웹 응용 프로그램에 대 한이 앱의 기본 URL을 사용자가 로그인 할 수 있는 합니다.  예: `http://localhost:12345` 공용 클라이언트 (모바일 및 데스크톱)에 대 한 Azure AD를 사용 하 여 해당 토큰 응답을 반환 합니다. 애플리케이션에 특정한 값을 입력합니다.  예: `http://MyFirstAADApp`
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* 고유한 클라이언트 식별자를 Azure AD는 응용 프로그램 등록을 완료 한 후 할당 (합니다 **응용 프로그램 ID**). 이 값은 다음 섹션에서 필요하므로 애플리케이션 페이지에서 이 값을 복사해 둡니다.
* Azure Portal에서 애플리케이션을 찾으려면 **앱 등록**을 클릭하고 **모든 애플리케이션 보기**를 클릭합니다.
