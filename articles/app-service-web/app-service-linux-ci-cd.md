---
title: "Linux에서 Azure Web App을 사용한 연속 배포 | Microsoft Docs"
description: "Linux의 Azure Web App에서 연속 배포를 설정하는 방법"
keywords: azure app service, linux, oss, acr
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 6b6c173c6c4bb3f670c54208c80e6d966a1f396e
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="continuous-deployment-with-azure-web-app-on-linux"></a>Linux에서 Azure Web App을 사용한 연속 배포

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

이 자습서에서는 관리되는 [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/) 리포지토리 또는 [Docker 허브](https://hub.docker.com)에서 사용자 지정 컨테이너 이미지에 대한 연속 배포를 구성합니다.

## <a name="step-1---sign-in-to-azure"></a>1단계 - Azure에 로그인

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>2단계 - Docker 허브 연속 배포 사용

**[Azure Portal](https://portal.azure.com/)**에서 페이지 왼쪽의 **App Service** 옵션을 클릭합니다.

Docker 허브 연속 배포를 구성하려는 앱의 이름을 클릭합니다.

**앱 설정**에서 `true` 값을 갖는 `DOCKER_ENABLE_CI`라는 앱을 추가합니다.

![앱 설정 이미지 삽입](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>3단계 - 웹후크 URL 준비

웹후크 URL에는 끝점 `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`가 필요합니다.

Azure Portal에서 웹앱 게시 프로필을 다운로드하여 `publishingusername` 및 `publishingpwd`를 구할 수 있습니다.

![웹후크 추가 이미지 2 삽입](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>4단계: 웹후크 추가

### <a name="azure-container-registry"></a>Azure Container Registry

레지스트리 포털 블레이드에서 **웹후크**를 클릭하고 **추가**를 클릭하여 새 웹후크를 만듭니다. **웹후크 만들기** 블레이드에서 웹후크에 이름을 지정합니다. 웹후크 URI에 **3단계**에서 가져온 URL을 제공해야 합니다.

범위를 컨테이너 이미지를 포함하는 리포지토리로 정의해야 합니다.

![웹후크 이미지 삽입](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

이미지를 업데이트할 때 자동으로 웹앱이 새 이미지로 업데이트됩니다.

### <a name="docker-hub"></a>Docker 허브

Docker 허브 페이지에서 **웹후크**를 클릭한 후 **웹후크 만들기**를 클릭합니다.

![웹후크 추가 이미지 1 삽입](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

웹후크 URL에 **3단계**에서 가져온 URL을 제공해야 합니다.

![웹후크 추가 이미지 2 삽입](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

이미지를 업데이트할 때 자동으로 웹앱이 새 이미지로 업데이트됩니다.

## <a name="next-steps"></a>다음 단계
* [Linux에서 Azure Web App이란?](./app-service-linux-intro.md)
* [Linux의 Azure Web App에서 앱 만들기](./app-service-linux-how-to-create-web-app.md)
* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
* [Linux의 Azure Web App에서 Node.js용 PM2 구성 사용](app-service-linux-using-nodejs-pm2.md)
* [Linux의 Azure Web App에서 .NET Core 사용](app-service-linux-using-dotnetcore.md)
* [Linux의 Azure Web App에서 Ruby 사용](app-service-linux-ruby-get-started.md)
* [Linux에서 Azure Web App에 대한 사용자 지정 Docker 이미지를 사용하는 방법](./app-service-linux-using-custom-docker-image.md)
* [Linux의 Azure App Service Web App에 대한 FAQ](./app-service-linux-faq.md) 




