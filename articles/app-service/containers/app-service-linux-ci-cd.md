---
title: 사용자 정의 리눅스 컨테이너에 CI / CD
description: Azure 앱 서비스에서 사용자 지정 Linux 컨테이너에 대한 지속적인 배포를 설정하는 방법에 대해 알아봅니다. Docker 허브 및 ACR에 대해 지속적인 배포가 지원됩니다.
keywords: Azure App Service, Linux, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687625"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Web App for Containers를 사용한 연속 배포

이 자습서에서는 관리되는 [Azure 컨테이너 레지스트리](https://azure.microsoft.com/services/container-registry/) 리포지토리 또는 Docker [Hub에서](https://hub.docker.com)사용자 지정 컨테이너 이미지에 대한 연속 배포를 구성합니다.

## <a name="enable-continuous-deployment-with-acr"></a>ACR을 사용하여 지속적인 배포 활성화

![ACR webhook 스크린샷](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 페이지의 왼쪽에서 **App Service** 옵션을 선택합니다.
3. 지속적인 배포를 구성하려는 앱의 이름을 선택합니다.
4. **컨테이너 설정** 페이지에서 **단일 컨테이너**를 선택합니다.
5. **Azure Container Registry**를 선택합니다.
6. **지속적인 배포 > 켜짐**을 선택합니다.
7. **저장**을 선택하여 지속적인 배포를 활성화합니다.

## <a name="use-the-acr-webhook"></a>ACR webhook 사용

지속적인 배포가 활성화되면 Azure Container Registry webhook 페이지에서 새로 만든 webhook를 볼 수 있습니다.

![ACR webhook 스크린샷](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Container Registry에서 Webhook를 클릭하여 현재 webhook를 볼 수 있습니다.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Docker 허브(선택 사항)를 사용하여 지속적인 배포 활성화

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 페이지의 왼쪽에서 **App Service** 옵션을 선택합니다.
3. 지속적인 배포를 구성하려는 앱의 이름을 선택합니다.
4. **컨테이너 설정** 페이지에서 **단일 컨테이너**를 선택합니다.
5. **Docker 허브**를 선택합니다.
6. **지속적인 배포 > 켜짐**을 선택합니다.
7. **저장**을 선택하여 지속적인 배포를 활성화합니다.

![앱 설정 스크린샷](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Webhook URL을 복사합니다. Docker 허브에 대한 webhook를 추가하려면 <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker 허브에 대한 webhook</a>를 따릅니다.

## <a name="next-steps"></a>다음 단계

* [Linux의 Azure App Service 소개](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Linux의 App Service에서 .NET Core 웹앱 만들기](quickstart-dotnetcore.md)
* [Linux의 App Service에서 Ruby 웹앱 만들기](quickstart-ruby.md)
* [Web App for Containers에 Docker/Go 웹앱 배포](quickstart-docker-go.md)
* [Linux의 Azure App Service에 대한 FAQ](./app-service-linux-faq.md)
* [Azure CLI를 사용하여 Web App for Containers 관리](./app-service-linux-cli.md)
