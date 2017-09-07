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
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: bbe2e0bafe48b39f12c5a8a46511e9275926d4b6
ms.contentlocale: ko-kr
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Linux의 Azure Web App 소개

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>개요
[Web App](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview)은 웹 사이트와 웹 응용 프로그램 호스팅을 위해 최적화된 완전히 관리되는 계산 플랫폼입니다. 고객은 지원되는 응용 프로그램 스택에 대해 Linux의 웹앱을 사용하여 Linux에서 웹앱을 호스트할 수 있습니다. 다음 섹션에는 현재 지원되는 응용 프로그램 스택이 나와 있습니다. 

### <a name="languages"></a>언어

|Node.js|PHP|.NET Core|루비|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

### <a name="deployments"></a>배포

* FTP
* 로컬 Git
* GitHub
* Bitbucket

### <a name="devops"></a>DevOps

* 스테이징 환경
* [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-intro) 및 DockerHub CI/CD

### <a name="console-publishing-and-debugging"></a>콘솔, 게시 및 디버깅

* 환경
* 배포
* 기본 콘솔
* SSH

### <a name="scaling"></a>확장

* 고객은 [App Service 계획](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)의 계층을 변경하여 웹앱을 확장 및 축소할 수 있습니다.

## <a name="limitations"></a>제한 사항
Azure Portal에는 Linux의 웹앱에 대해 작동하는 기능만 표시됩니다. 더 많은 기능이 사용 가능해지면 포털에 표시됩니다.

가상 네트워크 통합, Azure Active Directory/타사 인증 또는 Kudu 사이트 확장 등의 일부 기능은 아직 사용할 수 없습니다. 이러한 기능이 사용 가능해지면 설명서와 블로그에 변경 내용이 업데이트될 것입니다.

이 공개 미리 보기는 현재 다음 지역에서 사용할 수 있습니다.

|아메리카|아시아 태평양|유럽|
|:----------------:|:--------------:|:------------:|
|브라질 남부|오스트레일리아 동부|북유럽|
|미국 동부|동아시아|서유럽|
|미국 중북부|일본 동부||
|미국 중남부|인도 남부||
|미국 서부|동남아시아||

추가 영역을 사용할 수 있는 시기를 [Azure 상태 대시보드](https://azure.microsoft.com/status)에서 확인할 수 있습니다.

Linux의 Web Apps는 [기본, 표준 및 프리미엄](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/) App Service 계획에서만 지원되며 [체험 또는 공유](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/) 계층은 없습니다. Linux의 Web App에 대한 중요한 제한 사항은 다음과 같습니다.

* 비 Linux Web Apps을 호스트하는 App Service 계획에서 Linux의 Web App을 만들 수 없습니다.
* 비 Linux Web App을 포함하는 리소스 그룹에서 Linux의 Web App을 만들 경우 기존 App Service 계획과 다른 지역에서 App Service 계획을 만들어야 합니다.

## <a name="troubleshooting"></a>문제 해결 ##

응용 프로그램이 시작되지 못하거나 앱에서 로깅을 확인하려는 경우 LogFiles 디렉터리에서 Docker 로그를 확인합니다. SCM 사이트 또는 FTP를 통해 이 디렉터리에 액세스할 수 있습니다.
컨테이너에서 `stdout` 및 `stderr`을 로그하려면 **진단 로그** 아래에서 **Docker 컨테이너 로깅**을 활성화해야 합니다.

![로깅 사용][2]

![Kudu를 사용하여 Docker 로그 보기][1]

**고급 도구**의 **개발 도구** 메뉴에서 SCM 사이트에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Linux에서 App Service를 시작하려면 다음 링크를 참조하세요. [당사 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시할 수 있습니다.

* [Linux에서 Azure Web App에 대한 사용자 지정 Docker 이미지를 사용하는 방법](app-service-linux-using-custom-docker-image.md)
* [Linux의 Azure Web App에서 Node.js용 PM2 구성 사용](app-service-linux-using-nodejs-pm2.md)
* [Linux의 Azure App Service Web App에서 .NET Core 사용](app-service-linux-using-dotnetcore.md)
* [Linux의 Azure App Service Web App에서 Ruby 사용](app-service-linux-ruby-get-started.md)
* [Linux의 Azure App Service Web App에 대한 FAQ](app-service-linux-faq.md)
* [Linux의 Azure Web App에 대한 SSH 지원](./app-service-linux-ssh-support.md)
* [Azure App Service에서 스테이징 환경 설정](./web-sites-staged-publishing.md)
* [Linux에서 Azure Web App을 사용한 Docker 허브 연속 배포](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

