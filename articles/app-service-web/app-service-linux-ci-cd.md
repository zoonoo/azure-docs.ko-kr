---
title: "Linux에서 Azure Web App을 사용한 Docker 허브 연속 배포 | Microsoft Docs"
description: "Linux의 Azure Web App에서 연속 배포를 설정하는 방법"
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 68e11f07520bbe8da6812a34fe95708fc9e932ac
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="docker-hub-continuous-deployment-with-azure-web-app-on-linux"></a>Linux에서 Azure Web App을 사용한 Docker 허브 연속 배포

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

이 자습서에서는 [Docker 허브](https://hub.docker.com)에서 사용자 지정 컨테이너 이미지에 대한 연속 배포를 구성합니다.

## <a name="step-1---log-in-to-azure"></a>1단계 - Azure에 로그인

Azure Portal http://portal.azure.com 에 로그인합니다.

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>2단계 - Docker 허브 연속 배포 사용

**[Azure Portal](https://portal.azure.com/)**에서 페이지 왼쪽의 **App Service** 옵션을 클릭합니다.

Docker 허브 연속 배포를 구성하려는 앱의 이름을 클릭합니다.

**앱 설정**에서 `true` 값을 갖는 `DOCKER_ENABLE_CI`라는 앱을 추가합니다.

![앱 설정 이미지 삽입](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---add-a-web-hook-to-docker-hub"></a>3단계 - Docker 허브에 웹후크 추가

Docker 허브 페이지에서 **웹후크**를 클릭한 후 **웹후크 만들기**를 클릭합니다.

![웹후크 추가 이미지 1 삽입](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

웹후크 URL에는 끝점 `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`가 필요합니다.

![웹후크 추가 이미지 2 삽입](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Azure Portal에서 웹앱 게시 프로필을 다운로드하여 `publishingusername` 및 `publishingpwd`를 구할 수 있습니다.

![웹후크 추가 이미지 2 삽입](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

이미지를 업데이트할 때 자동으로 웹앱이 새 이미지로 업데이트됩니다.

## <a name="next-steps"></a>다음 단계
* [Linux에서 Azure Web App이란?](./app-service-linux-intro.md)
* [Linux의 Azure Web App에서 앱 만들기](./app-service-linux-how-to-create-web-app.md)
* [Linux의 Azure Web App에서 Node.js용 PM2 구성 사용](app-service-linux-using-nodejs-pm2.md)
* [Linux의 Azure Web App에서 .NET Core 사용](app-service-linux-using-dotnetcore.md)
* [Linux의 Azure Web App에서 Ruby 사용](app-service-linux-ruby-get-started.md)
* [Linux에서 Azure Web App에 대한 사용자 지정 Docker 이미지를 사용하는 방법](./app-service-linux-using-custom-docker-image.md)
* [Linux의 Azure App Service Web App에 대한 FAQ](./app-service-linux-faq.md) 




