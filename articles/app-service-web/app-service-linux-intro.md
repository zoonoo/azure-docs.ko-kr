---
title: "Linux의 App Service 소개 | Microsoft Docs"
description: "Linux의 App Service에 대해 자세히 알아봅니다."
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
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9ed502efadbc27ca507fa5be455f32c3bd1b9eee
ms.lasthandoff: 03/10/2017


---
# <a name="introduction-to-app-service-on-linux"></a>Linux의 App Service 소개
Linux의 Azure App Service는 현재 공개 미리 보기가 제공되며 Linux에서 웹앱을 실행하도록 지원합니다.

## <a name="overview"></a>개요
고객은 지원되는 응용 프로그램 스택에 대해 Linux의 App Service를 사용하여 Linux에서 웹앱을 호스트할 수 있습니다. 다음 섹션에는 현재 지원되는 응용 프로그램 스택이 나와 있습니다. 

## <a name="features"></a>기능
Linux의 App Service는 현재 다음과 같은 응용 프로그램 스택을 지원합니다.

* Node.js
    * 4.5.0
    * 4.4.7
    * 6.2.2
    * 6.6.0
    * 6.9.3
* PHP
    * 5.6.23
    * 7.0.6
* .NET Core
    * 1.0
* Ruby
    * 2.3

고객은 다음을 사용하여 해당 응용 프로그램을 배포할 수 있습니다.

* FTP
* 로컬 Git
* GitHub
* Bitbucket

응용 프로그램 크기 조정:

* 고객은 App Service 계획에서 계층을 변경하여 웹앱을 확장 및 축소할 수 있습니다.
* 고객은 자신의 SKU 범위 내에서 응용 프로그램을 규모 확장하고 여러 앱 인스턴스를 실행할 수 있습니다.

Kudu의 경우 일부 기본 기능이 다음에 작동합니다.

* 환경
* 배포
* 기본 콘솔

## <a name="limitations"></a>제한 사항
Azure Portal에는 Linux의 App Service에 대해 작동하는 기능만 표시되고 나머지는 숨겨집니다. 더 많은 기능이 사용 가능해지면 포털에 표시될 것입니다.

가상 네트워크 통합, Azure Active Directory/타사 인증 또는 Kudu 사이트 확장 등의 일부 기능은 미완성 상태입니다. 이러한 기능이 완성되면 설명서와 블로그에 변경 내용이 업데이트될 것입니다.

이 공개 미리 보기는 현재 다음 지역에서 사용할 수 있습니다.

* 미국 서부
* 서유럽 
* 동남아시아

Linux의 웹앱은 전용 App Service 계획에서만 지원되며 무료 또는 공유 계층은 없습니다. 또한 일반 및 Linux 웹앱에 대한 App Service 계획은 상호 배타적이므로 비 Linux App Service 계획에서 Linux 웹앱을 만들 수 없습니다.

Linux의 웹앱은 동일한 지역에 비 Linux 웹앱을 포함하지 않는 리소스 그룹에서 만들어야 합니다.

Linux의 Web Apps는 아직 컴파일되지 않은 원본에서 .NET Core 앱의 배포를 지원하지 않습니다. 먼저 .NET Core 앱을 로컬에 게시/컴파일한 다음 게시된 사이트 비트를 앱으로 푸시해야 합니다.

## <a name="next-steps"></a>다음 단계
Linux에서 App Service를 시작하려면 다음 링크를 참조하세요. [당사 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시할 수 있습니다.

* [Linux의 App Service에서 웹앱 만들기](app-service-linux-how-to-create-a-web-app.md)
* [Linux에서 App Service에 대한 사용자 지정 Docker 이미지를 사용하는 방법](app-service-linux-using-custom-docker-image.md)
* [Linux의 웹앱에 있는 Node.js용 PM2 구성 사용](app-service-linux-using-nodejs-pm2.md)
* [Linux의 Azure App Service 웹앱에서 .NET Core 사용](app-service-linux-using-dotnetcore.md)
* [Linux의 Azure App Service Web Apps에서 Ruby 사용](app-service-linux-using-ruby.md)
* [Linux의 Azure App Service Web Apps에 대한 FAQ](app-service-linux-faq.md)


