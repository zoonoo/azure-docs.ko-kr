---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: da2477b19327273fe922ac81f909233cb4ef8f06
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502904"
---
## <a name="setting-up-your-web-server-or-project"></a>웹 서버 또는 프로젝트 설정

> 이 샘플의 프로젝트를 다운로드하고 싶으세요?
> - 로컬 웹 서버(예: Node)용 [프로젝트 파일 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
>
> 또는
> - [Visual Studio 프로젝트 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)
>
> 그런 후 [구성 단계](#register-your-application)로 건너뛰어 실행 전에 코드 샘플을 구성합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 실행하려면 [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) 또는 IIS Express와 같은 로컬 웹 서버가 [Visual Studio 2017](https://www.visualstudio.com/downloads/)과 통합되어 있어야 합니다.

Node.js를 사용하여 프로젝트를 실행하려면 [Visual Studio Code](https://code.visualstudio.com/download)와 같은 IDE를 설치하여 프로젝트 파일을 편집합니다.

이 가이드의 지침은 Node.js 및 Visual Studio 2017 둘 다를 기준으로 하지만 다른 개발 환경 또는 웹 서버를 사용해도 무방합니다.

## <a name="create-your-project"></a>프로젝트 만들기

> ### <a name="option-1-node-other-web-servers"></a>옵션 1: Node/기타 웹 서버
> [Node.js](https://nodejs.org/en/download/)를 설치했는지 확인한 후 아래 단계를 따릅니다.
> - 애플리케이션을 호스트할 폴더를 만듭니다.

<p><!-- -->

> ### <a name="option-2-visual-studio"></a>옵션 2: Visual Studio
> Visual Studio를 사용하며 새 프로젝트를 만드는 경우 아래 단계에 따라 새 Visual Studio 솔루션을 만드세요.
> 1.    Visual Studio에서  **파일 > 새로 만들기 > 프로젝트**
> 2.    **Visual C#\Web**에서 **ASP.NET 웹 애플리케이션(.NET Framework)** 을 선택합니다.
> 3.    애플리케이션 이름을 입력하고 **확인**을 선택합니다.
> 4.    **새 ASP.NET 웹 애플리케이션**에서 **비어 있음**을 선택합니다.

## <a name="create-your-single-page-applications-ui"></a>단일 페이지 애플리케이션의 UI 만들기
1. JavaScript SPA에 대한 `index.html` 파일을 만듭니다. Visual Studio를 사용하는 경우 프로젝트(프로젝트 루트 폴더)를 선택하고, 마우스 오른쪽 단추를 클릭하고 **추가 > 새 항목 > HTML 페이지**를 선택한 후 이름을 index.html이라고 지정합니다.

2. 페이지에 다음 코드를 추가합니다.
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.4/js/msal.js"></script>
       <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```
