---
title: "Linux에서 Azure Web App에 대한 사용자 지정 Docker 이미지를 사용하는 방법 | Microsoft Docs"
description: "Linux에서 Azure Web App에 대한 사용자 지정 Docker 이미지를 사용하는 방법"
keywords: "azure app service, 웹앱, linux, docker, 컨테이너"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 9962766b0a1fdfe313b70ea65e08785ec8a97996
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

# <a name="using-a-custom-docker-image-for-azure-web-app-on-linux"></a>Linux에서 Azure Web App에 대한 사용자 지정 Docker 이미지 사용 #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


App Service는 PHP 7.0 및 Node.js 4.5와 같은 특정 버전에 대한 지원을 통해 Linux에 미리 정의된 응용 프로그램 스택을 제공합니다. Linux의 App Service는 Docker 컨테이너를 사용하여 이러한 미리 작성된 응용 프로그램 스택을 호스트합니다. 또한 사용자 지정 Docker 이미지를 사용하여 Azure에 아직 정의되지 않은 응용 프로그램 스택에 웹앱을 배포할 수도 있습니다. 사용자 지정 Docker 이미지는 공용 또는 개인 Docker 리포지토리에 호스트할 수 있습니다.


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>방법: 웹앱에 대한 사용자 지정 Docker 이미지 설정
신규 및 기존 웹앱에 대해 사용자 지정 Docker 이미지를 설정할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 Linux의 웹앱을 만들 경우 **컨테이너 구성**을 클릭하여 사용자 지정 Docker 이미지를 설정합니다.

![Linux의 새 웹앱에 대한 사용자 지정 Docker 이미지][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>방법: Docker 허브에서 사용자 지정 Docker 이미지 사용 ##
Docker 허브에서 사용자 지정 Docker 이미지를 사용하려면

1. [Azure Portal](https://portal.azure.com)에서 Linux의 웹앱을 찾은 후 **설정**에서 **Docker 컨테이너**를 클릭합니다.

2.  **Docker 허브**를 **이미지 소스**로 선택하고 **공용** 또는 **개인**을 클릭하고 **이미지 및 선택적 태그 이름**(예: `node:4.5`)을 입력합니다. **시작 명령**은 자동으로 Docker 이미지 파일에 정의된 내용을 기준으로 하지만 명령을 직접 설정할 수도 있습니다.  

    ![Docker 허브 공용 리포지토리 이미지 구성][2]

    이미지가 개인 리포지토리에 있는 것이면 개인 Docker 허브 리포지토리에 대해 Docker 허브 자격 증명(**로그인 사용자 이름** 및 **암호**)도 입력해야 합니다.

    ![Docker 허브 개인 리포지토리 이미지 구성][3]

3. 컨테이너를 구성한 후 **저장**을 클릭합니다.

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>개인 이미지 레지스트리의 Docker 이미지를 사용하는 방법 ##
개인 이미지 레지스트리의 사용자 지정 Docker 이미지를 사용하려면

1. [Azure Portal](https://portal.azure.com)에서 Linux의 웹앱을 찾은 후 **설정**에서 **Docker 컨테이너**를 클릭합니다.

2.  **이미지 소스**로 **개인 레지스트리**를 클릭합니다. 개인 레지스트리에 대한 **이미지 및 선택적 태그 이름**, **서버 URL**을 입력하고 자격 증명(**로그인 사용자 이름** 및 **암호**)를 함께 입력합니다. **Save**를 클릭합니다.

    ![개인 레지스트리에서 Docker 이미지 구성][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>방법: Docker 이미지에 사용되는 포트 설정 ##

웹앱에 대한 사용자 지정 Docker 이미지를 사용하는 경우 생성된 컨테이너에 추가된 `PORT` 환경 변수를 Dockerfile에서 사용할 수 있습니다. 다음의 Ruby 응용 프로그램용 docker 파일 예제를 살펴보세요.

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p $PORT -e production

명령의 마지막 줄에서 런타임에 PORT 환경 변수가 전달된다는 것을 알 수 있습니다. 명령은 대/소문자를 구분해야 합니다.

다른 사용자가 작성한 기존 Docker 이미지를 사용하면 응용 프로그램에 대해 포트 80 이외의 포트를 지정해야 할 수도 있습니다. 포트를 구성하려면 아래와 같이 값과 함께 `PORT`라는 응용 프로그램 설정을 추가합니다.

![사용자 지정 Docker 이미지에 대한 PORT 앱 설정 구성][6]


## <a name="how-to-switch-back-to-using-a-built-in-image"></a>방법: 기본 제공 이미지 사용 방식으로 다시 전환 ##

사용자 지정 이미지를 사용하는 방식에서 기본 제공 이미지를 사용하는 방식으로 전환하려면

1. [Azure Portal](https://portal.azure.com)에서 Linux의 웹앱을 찾은 후 **설정**에서 **App Service**를 클릭합니다.

2. 기본 제공 이미지에 사용한 **런타임 스택**을 선택하고 **저장**을 클릭합니다. 

![기본 제공 Docker 이미지 구성][5]


## <a name="troubleshooting"></a>문제 해결 ##

응용 프로그램이 사용자 지정 Docker 이미지로 시작하지 못할 경우 LogFiles/docker 디렉터리에서 Docker 로그를 확인합니다. SCM 사이트 또는 FTP를 통해 이 디렉터리에 액세스할 수 있습니다. 

![Kudu를 사용하여 Docker 로그 보기][7]

**고급 도구**의 **개발 도구** 메뉴에서 SCM 사이트에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계 ##

Linux에서 웹앱을 시작하려면 다음 링크를 따르세요.   

* [Linux의 Azure Web App 소개](./app-service-linux-intro.md)
* [Linux의 Azure Web App에서 웹앱 만들기](./app-service-linux-how-to-create-web-app.md)
* [Linux의 Azure Web App에서 Node.js용 PM2 구성 사용](./app-service-linux-using-nodejs-pm2.md)
* [Linux의 Azure App Service Web App에 대한 FAQ](app-service-linux-faq.md)

[당사 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시하세요.


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png

