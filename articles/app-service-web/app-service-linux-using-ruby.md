---
title: "Linux의 Azure App Service Web Apps에서 Ruby 사용 | Microsoft Docs"
description: "Linux의 Azure App Service Web Apps에서 Ruby 사용"
keywords: "azure app service, 웹앱, faq, linux, oss, ruby"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 8427034e5229000faf134bcd5b7174b0f7046321
ms.lasthandoff: 02/17/2017


---

# <a name="using-ruby-in-web-apps-on-linux"></a>Linux의 Web Apps에서 Ruby 사용 #

백 엔드 최신 업데이트에서는 Ruby v.2.3에 대한 지원이 추가되었습니다. Linux 웹앱의 구성을 설정하여 응용 프로그램 스택을 변경할 수 있습니다.

## <a name="using-the-azure-portal"></a>Azure 포털 사용 ##

[Azure Portal](https://portal.azure.com)의 새 메뉴에서 다음 이미지에 나와 있는 것처럼 Web + Mobile 옵션에서 Linux에 Web App을 만들도록 선택할 수 있습니다.

![Azure Portal에서 웹앱 만들기][1]

그러면 다음 그림과 같이 **만들기** 블레이드가 열립니다.

![만들기 블레이드][2]

1. 웹앱에 이름을 지정합니다.
2. 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. ([제한 사항 섹션](app-service-linux-intro.md)에서 사용 가능한 지역 참조)
3. 기존 Azure App Service 계획을 선택하거나 새 App Service 계획을 만듭니다. ([제한 사항 섹션](app-service-linux-intro.md)에서 App Service 정보 참조)
4. 기본 제공 런타임 스택에서 Ruby를 선택합니다.

Ruby 웹앱이 만들어지면 Git 또는 FTP를 사용하여 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Linux의 App Service란?](app-service-linux-intro.md)
* [Linux의 App Service에서 웹앱 만들기](app-service-linux-how-to-create-a-web-app.md)
* [Azure App Service에 대한 로컬 Git 배포](app-service-deploy-local-git.md)
* [Linux의 Azure App Service Web Apps에 대한 FAQ](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
