---
title: "5분 내에 Azure에서 정적 HTML 웹앱 만들기 | Microsoft Docs"
description: "샘플 앱을 배포하여 App Service에서 웹앱을 실행하는 작업이 얼마나 쉬운지 알아봅니다."
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>5분 내에 Azure에서 정적 HTML 웹앱 만들기

이 빠른 시작에서는 기본적인 HTML+CSS 사이트를 Azure에 배포하는 방법을 단계별로 안내합니다. [Azure App Service 계획](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)을 사용하여 앱을 실행하고, [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli)를 사용하여 웹앱을 만듭니다. Git를 사용하여 앱을 Azure에 배포합니다. 필수 구성 요소가 설치된 후 자습서를 완료하는 데는 약 5분 정도 걸립니다.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>필수 조건

이 샘플을 만들기 전에 다음 구성 요소를 다운로드하여 설치합니다.

- [Git](https://git-scm.com/)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>샘플 다운로드

터미널 창에서 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>HTML 보기

샘플 HTML을 포함하는 디렉터리로 이동합니다. 브라우저에서 *index.html* 파일을 엽니다.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

`quickStartPlan` App Service 계획에서 [웹앱](app-service-web-overview.md)을 만듭니다. 웹앱은 코드에 대한 호스팅 공간을 제공하고, 배포된 앱을 확인할 수 있도록 URL도 제공합니다.

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

이 페이지는 Azure App Service 웹앱으로 실행됩니다.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>앱 업데이트 및 다시 배포

*index.html* 파일을 엽니다. 태그를 변경합니다. 예를 들어 `Hello world!`를 `Hello Azure!`로 변경하세요.

Git에서 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git commit -am "updated HTML"
git push azure master
```

배포가 완료되면 브라우저를 새로 고쳐 변경 내용을 확인합니다.

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

- 샘플 [Web Apps CLI 스크립트](app-service-cli-samples.md)를 탐색합니다.
- [App Service 앱](app-service-web-tutorial-custom-domain.md)에 contoso.com 등의 [사용자 지정 도메인 이름을 매핑](app-service-web-tutorial-custom-domain.md)하는 방법을 알아봅니다.
