---
title: Kudu 서비스 개요
description: App Service 및 해당 기능에서 지속적인 배포를 지원하는 엔진에 대해 알아봅니다.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609631"
---
# <a name="kudu-service-overview"></a>Kudu 서비스 개요

Kudu는 소스 제어 기반 배포와 관련된 [Azure App Service](overview.md)의 다양한 기능, Dropbox 및 OneDrive 동기화와 같은 기타 배포 방법을 지원하는 엔진입니다. 

## <a name="access-kudu-for-your-app"></a>앱에 대한 Kudu 액세스
앱을 만들 때마다 App Service는 HTTPS를 통해 보안이 유지되는 앱의 도우미 앱을 만듭니다. 이 Kudu 앱은 다음 위치에서 액세스할 수 있습니다.

- 앱이 격리된 계층에 없음: `https://<app-name>.scm.azurewebsites.net`
- 앱이 격리된 계정에 있음(App Service Environment): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

자세한 내용은 [Kudu 서비스 액세스](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)를 참조하세요.

## <a name="kudu-features"></a>Kudu 기능

Kudu는 다음과 같은 App Service 앱에 대한 유용한 정보를 제공합니다.

- 앱 설정
- 연결 문자열
- 환경 변수
- 서버 변수
- HTTP 헤더

또한 다음과 같은 다른 기능도 제공합니다.

- [Kudu 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console)에서 명령어를 실행합니다.
- IIS 진단 덤프 또는 Docker 로그를 다운로드합니다.
- IIS 프로세스 및 사이트 확장을 관리합니다.
- Windows 앱에 대한 배포 웹후크를 추가합니다.
- `/ZipDeploy`로 ZIP 배포 UI를 허용합니다.
- [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 생성합니다.
- [REST API](https://github.com/projectkudu/kudu/wiki/REST-API)로 액세스를 허용합니다.

## <a name="more-resources"></a>기타 참고 자료

Kudu는 [오픈 소스 프로젝트](https://github.com/projectkudu/kudu)이며 [Kudu Wiki](https://github.com/projectkudu/kudu/wiki)에 설명서가 있습니다.

