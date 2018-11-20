---
title: Linux의 App Service 소개 | Microsoft Docs
description: Linux의 Azure App Service에 대해 알아봅니다.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: naziml
manager: cfowler
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/09/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 75eb260b107df3877219266794e45eb11d5deb35
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633243"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Linux의 Azure App Service 소개

[Web App](../app-service-web-overview.md)은 웹 사이트와 웹 응용 프로그램 호스팅을 위해 최적화된 완전히 관리되는 계산 플랫폼입니다. 고객은 지원되는 응용 프로그램 스택에 대해 Linux의 App Service를 사용하여 Linux에서 웹앱을 호스트할 수 있습니다. 다음 섹션에는 현재 지원되는 응용 프로그램 스택이 나와 있습니다.

## <a name="languages"></a>언어

Linux의 App Service는 개발자의 생산성을 향상시키기 위해 수많은 기본 제공 이미지를 지원합니다. 응용 프로그램에 필요한 런타임이 기본 제공 이미지에 지원되지 않는 경우 [자체 Docker 이미지를 빌드](tutorial-custom-docker-image.md)하여 Web App for Containers에 배포하는 방법에 대한 지침이 있습니다.

| 언어 | 지원되는 버전 |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1,10.10 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14(모두 JRE 8 실행) |
| PHP | 5.6, 7.0, 7.2 |
| Python(미리 보기) | 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1 |
| Ruby | 2.3 |

자세한 내용은 [Linux의 App Service에서 Java 웹앱 만들기](https://docs.microsoft.com/azure/app-service/containers/quickstart-java)를 참조하세요.

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

* 고객은 [App Service 계획](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)의 계층을 변경하여 웹앱을 확장 및 축소할 수 있습니다.

## <a name="locations"></a>위치

[Azure 상태 대시보드](https://azure.microsoft.com/status)를 확인합니다.

## <a name="limitations"></a>제한 사항

Azure Portal에는 현재 Web App for Containers에 대해 작동하는 기능만 표시됩니다. 더 많은 기능이 사용 가능해지면 포털에 표시됩니다.

가상 네트워크 통합, Azure Active Directory/타사 인증 또는 Kudu 사이트 확장 등의 일부 기능은 아직 사용할 수 없습니다. 이러한 기능이 사용 가능해지면 설명서와 블로그에 변경 내용이 업데이트될 것입니다.

Linux의 App Service는 [기본, 표준 및 프리미엄](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service 계획에서만 지원되며 [체험 또는 공유](https://azure.microsoft.com/pricing/details/app-service/plans/) 계층은 없습니다. 이미 비 Linux Web Apps를 호스트 중인 App Service 계획에서는 Web App for Containers를 만들 수 없습니다. 동일한 리소스 그룹에서도 Windows 및 Linux 앱을 함께 사용하지 않는 등 현재 제약 조건이 있습니다.

## <a name="troubleshooting"></a>문제 해결

응용 프로그램이 시작되지 못하거나 앱에서 로깅을 확인하려는 경우 LogFiles 디렉터리에서 Docker 로그를 확인합니다. SCM 사이트 또는 FTP를 통해 이 디렉터리에 액세스할 수 있습니다.
컨테이너에서 `stdout` 및 `stderr`을 로그하려면 **진단 로그** 아래에서 **Docker 컨테이너 로깅**을 활성화해야 합니다.

![로깅 사용][2]

![Kudu를 사용하여 Docker 로그 보기][1]

**고급 도구**의 **개발 도구** 메뉴에서 SCM 사이트에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 다양한 언어로 작성된 웹앱을 사용하여 Linux의 App Service를 시작합니다.

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-dotnetcore.md)
* [Node.JS](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [다중 컨테이너 앱](quickstart-multi-container.md)

또한 Linux의 App Service에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Linux의 App Service FAQ](app-service-linux-faq.md)
* [Linux의 App Service에 대한 SSH 지원](app-service-linux-ssh-support.md)
* [App Service에서 스테이징 환경 설정](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker 허브 지속적인 배포](app-service-linux-ci-cd.md)

[당사 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시할 수 있습니다.

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
