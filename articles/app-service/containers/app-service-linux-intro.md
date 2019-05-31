---
title: Linux의 App Service 소개 - Azure | Microsoft Docs
description: Linux의 Azure App Service에 대해 알아봅니다.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu;yili
ms.custom: seodec18
ms.openlocfilehash: 8d40b28103e24214ef00864b032266cc95889eff
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780341"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Linux의 Azure App Service 소개

[Azure App Service](../overview.md)는 웹 사이트와 웹 애플리케이션 호스팅을 위해 최적화된 완전 관리형 컴퓨팅 플랫폼입니다. 고객은 지원되는 애플리케이션 스택에 대해 Linux의 App Service를 사용하여 Linux에서 웹앱을 호스트할 수 있습니다. [언어](#languages) 섹션에는 현재 지원되는 애플리케이션 스택이 나와 있습니다.

## <a name="languages"></a>Languages

Linux의 App Service는 개발자의 생산성을 향상시키기 위해 수많은 기본 제공 이미지를 지원합니다. 애플리케이션에 필요한 런타임이 기본 제공 이미지에 지원되지 않는 경우 [자체 Docker 이미지를 빌드](tutorial-custom-docker-image.md)하여 Web App for Containers에 배포하는 방법에 대한 지침이 있습니다.

| 언어 | 지원되는 버전 |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1,10.10, 10.14 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14(모두 JRE 8 실행) |
| PHP | 5.6, 7.0, 7.2 |
| Python | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1, 2.2 |
| Ruby | 2.3, 2.4 |

## <a name="deployments"></a>배포

* FTP
* 로컬 Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* 스테이징 환경
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) 및 DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>콘솔, 게시 및 디버깅

* 환경
* 배포
* 기본 콘솔
* SSH

## <a name="scaling"></a>확장

* 고객은 [App Service 계획](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)의 계층을 변경하여 웹앱을 확장 및 축소할 수 있습니다.

## <a name="locations"></a>위치

[Azure 상태 대시보드](https://azure.microsoft.com/status)를 확인합니다.

## <a name="limitations"></a>제한 사항

Azure Portal에는 현재 Web App for Containers에 대해 작동하는 기능만 표시됩니다. 더 많은 기능이 사용 가능해지면 포털에 표시됩니다.

Linux의 App Service는 [무료, 기본, 표준 및 프리미엄](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service 계획에서만 지원되며 [공유](https://azure.microsoft.com/pricing/details/app-service/plans/) 계층은 없습니다. 이미 비 Linux Web Apps를 호스팅하는 App Service 계획에서는 Linux Web App을 만들 수 없습니다.  

현재 제한 사항에 따라 동일한 리소스 그룹에 대해 동일한 지역에서 Windows 및 Linux 앱을 혼합할 수 없습니다.

## <a name="troubleshooting"></a>문제 해결

애플리케이션이 시작되지 않거나 앱에서 로깅을 확인하려는 경우 LogFiles 디렉터리에서 Docker 로그를 확인하세요. SCM 사이트 또는 FTP를 통해 이 디렉터리에 액세스할 수 있습니다.
컨테이너에서 `stdout` 및 `stderr`을 로그하려면 **진단 로그** 아래에서 **Docker 컨테이너 로깅**을 활성화해야 합니다.

![로깅 사용][2]

설정은 즉시 적용됩니다. App Service는 설정 변경 사항을 감지하고 자동으로 컨테이너를 다시 시작합니다.

**고급 도구**의 **개발 도구** 메뉴에서 SCM 사이트에 액세스할 수 있습니다.

![Kudu를 사용하여 Docker 로그 보기][1]

## <a name="next-steps"></a>다음 단계

다음 문서에서는 다양한 언어로 작성된 웹앱을 사용하여 Linux의 App Service를 시작합니다.

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.JS](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [다중 컨테이너 앱](quickstart-multi-container.md)

Linux 기반 App Service에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Linux의 App Service FAQ](app-service-linux-faq.md)
* [Linux의 App Service에 대한 SSH 지원](app-service-linux-ssh-support.md)
* [App Service에서 스테이징 환경 설정](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker 허브 지속적인 배포](app-service-linux-ci-cd.md)

[당사 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시할 수 있습니다.

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
