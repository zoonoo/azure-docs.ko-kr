---
title: "Linux의 Azure Web App 소개 | Microsoft Docs"
description: "Linux의 Azure Web App에 대해 자세히 알아봅니다."
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 87b9da134bce20b037e2dce7e1c9f54454fafe9e
ms.contentlocale: ko-kr
ms.lasthandoff: 08/09/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Linux의 Azure Web App 소개

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>개요
고객은 지원되는 응용 프로그램 스택에 대해 Linux의 웹앱을 사용하여 Linux에서 웹앱을 호스트할 수 있습니다. 다음 섹션에는 현재 지원되는 응용 프로그램 스택이 나와 있습니다. 

## <a name="features"></a>기능
Linux의 웹앱은 Service는 현재 다음과 같은 응용 프로그램 스택을 지원합니다.

* Node.js
    * 4.4.
    * 4.5.
    * 6.2
    * 6.6
    * 6.9
    * 6.10
* PHP
    * 5.6
    * 7.0
* .NET Core
    * 1.0
    * 1.1
* 루비
    * 2.3

고객은 다음을 사용하여 해당 응용 프로그램을 배포할 수 있습니다.

* FTP
* 로컬 Git
* GitHub
* Bitbucket

응용 프로그램 크기 조정:

* 고객은 App Service 계획의 계층을 변경하여 웹앱을 확장 및 축소할 수 있습니다.
* 고객은 자신의 SKU 범위 내에서 응용 프로그램을 규모 확장하고 여러 앱 인스턴스를 실행할 수 있습니다.

Kudu의 경우 일부 기본 기능은 다음과 같습니다.

* 환경
* 배포
* 기본 콘솔
* SSH

devops:

* 스테이징 환경
* ACR 및 DockerHub CI/CD

## <a name="limitations"></a>제한 사항
Azure Portal에는 Linux의 웹앱에 대해 작동하는 기능만 표시되고 나머지는 숨겨집니다. 더 많은 기능이 사용 가능해지면 포털에 표시될 것입니다.

가상 네트워크 통합, Azure Active Directory/타사 인증 또는 Kudu 사이트 확장 등의 일부 기능은 아직 사용할 수 없습니다. 이러한 기능이 사용 가능해지면 설명서와 블로그에 변경 내용이 업데이트될 것입니다.

이 공개 미리 보기는 현재 다음 지역에서 사용할 수 있습니다.

* 미국 서부
* 미국 동부
* 서유럽
* 북유럽
* 동남아시아
* 오스트레일리아 동부
* 일본 동부
* 브라질 남부

Linux의 Web Apps는 전용 App Service 계획에서만 지원되며 무료 또는 공유 계층은 없습니다. 또한 일반 및 Linux 웹앱에 대한 App Service 계획은 상호 배타적이므로 비 Linux App Service 계획에서 Linux 웹앱을 만들 수 없습니다.

Linux의 Web Apps는 동일한 지역에 비 Linux 웹앱을 포함하지 않는 리소스 그룹에서 만들어야 합니다.

## <a name="next-steps"></a>다음 단계
Linux에서 App Service를 시작하려면 다음 링크를 참조하세요. [당사 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시할 수 있습니다.

* [Linux의 Azure Web App에서 Web Apps 만들기](app-service-linux-how-to-create-web-app.md)
* [Linux에서 Azure Web App에 대한 사용자 지정 Docker 이미지를 사용하는 방법](app-service-linux-using-custom-docker-image.md)
* [Linux의 Azure Web App에서 Node.js용 PM2 구성 사용](app-service-linux-using-nodejs-pm2.md)
* [Linux의 Azure App Service Web App에서 .NET Core 사용](app-service-linux-using-dotnetcore.md)
* [Linux의 Azure App Service Web App에서 Ruby 사용](app-service-linux-ruby-get-started.md)
* [Linux의 Azure App Service Web App에 대한 FAQ](app-service-linux-faq.md)
* [Linux의 Azure Web App에 대한 SSH 지원](./app-service-linux-ssh-support.md)
* [Azure App Service에서 스테이징 환경 설정](./web-sites-staged-publishing.md)
* [Linux에서 Azure Web App을 사용한 Docker 허브 연속 배포](./app-service-linux-ci-cd.md)


