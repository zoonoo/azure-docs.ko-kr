---
title: "Linux에서 실행되는 Azure Web App 만들기 | Microsoft Docs"
description: "Linux의 Azure Web App용 웹앱 만들기 워크플로"
keywords: "azure app service, 웹앱, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 49091d4a85bed23927850f9c0bbc5ea8b6e8c9e1
ms.contentlocale: ko-kr
ms.lasthandoff: 06/08/2017


---
# <a name="create-an-azure-web-app-running-on-linux"></a>Linux에서 실행되는 Azure Web App 만들기

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


## <a name="use-the-azure-portal-to-create-your-web-app"></a>Azure Portal을 사용하여 웹앱 만들기
다음 그림에 나와 있는 것처럼 [Azure Portal](https://portal.azure.com)에서 Linux의 웹앱을 만들 수 있습니다.

![Azure Portal에서 웹앱 만들기][1]

그러면 다음 그림과 같이 **만들기** 블레이드가 열립니다.

![만들기 블레이드][2]

1. 웹앱에 이름을 지정합니다.
2. 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. ([제한 사항 섹션](app-service-linux-intro.md)에서 사용 가능한 지역 참조)
3. 기존 Azure App Service 계획을 선택하거나 새 App Service 계획을 만듭니다. ([제한 사항 섹션](app-service-linux-intro.md)에서 App Service 정보 참조)
4. 사용하려는 응용 프로그램 스택을 선택합니다. 여러 버전의 Node.js, PHP, .Net Core, Ruby 중에 선택할 수 있습니다.

앱을 만든 후 다음 그림에 나와 있는 것처럼 응용 프로그램 설정에서 응용 프로그램 스택을 변경할 수 있습니다.

![응용 프로그램 설정][3]

## <a name="deploy-your-web-app"></a>웹앱 배포
관리 포털에서 **배포 옵션**을 선택하면 로컬 Git 또는 GitHub 리포지토리를 사용하여 응용 프로그램을 배포하기 위한 옵션이 제공됩니다. 나머지 설명은 비 Linux 웹앱과 유사합니다. [로컬 Git 배포](app-service-deploy-local-git.md) 또는 [연속 배포](app-service-continuous-deployment.md)의 지침에 따라 앱을 배포할 수 있습니다.

또한 FTP를 사용하여 사이트에 응용 프로그램을 업로드할 수도 있습니다. 다음 그림에 나와 있는 것처럼 진단 로그 섹션에서 웹앱에 대한 FTP 끝점을 가져올 수 있습니다.

![진단 로그][4]

## <a name="next-steps"></a>다음 단계
* [Linux에서 Azure Web App이란?](app-service-linux-intro.md)
* [Linux의 Azure Web App에서 Node.js용 PM2 구성 사용](app-service-linux-using-nodejs-pm2.md)
* [Linux의 Azure App Service Web App에서 Ruby 사용](app-service-linux-ruby-get-started.md)
* [Linux의 Azure App Service Web App에 대한 FAQ](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png

