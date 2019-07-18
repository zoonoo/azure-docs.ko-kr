---
title: Azure App Service Mobile Apps에서 Cordova 앱 만들기 | Microsoft Docs
description: 이 자습서에 따라 Azure 모바일 앱 백 엔드를 사용하여 Apache Cordova 개발을 시작할 수 있습니다.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
tags: ''
keywords: cordova,javascript,모바일,클라이언트
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8a1634cfa7b701401927859a9fbd3fb203b61a77
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445644"
---
# <a name="create-an-apache-cordova-app"></a>Apache Cordova 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발을 위한 중앙 새 및 통합 서비스에 투자 합니다. 개발자가 사용할 수 **빌드**를 **테스트** 하 고 **배포** 연속 통합 및 배달 파이프라인을 설정 하는 서비스입니다. 개발자 상태 및 사용 하 여 해당 앱의 사용량을 모니터링할 수 있습니다, 앱을 배포한 후 합니다 **Analytics** 하 고 **진단** , 서비스를 사용 하 여 사용자와 소통 하세요를 **푸시** 서비스입니다. 개발자가 활용할 수도 있습니다 **인증** 해당 사용자를 인증 하 고 **데이터** 유지 하 고 클라우드에 앱 데이터 동기화 서비스. 체크 아웃 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started) 지금 합니다.
>

## <a name="overview"></a>개요
이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 Apache Cordova 모바일 앱에 추가하는 방법을 보여 줍니다.  새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 *할 일 목록* Apache Cordova 앱을 만듭니다.

이 자습서를 완료해야 Azure App Service에서 Mobile Apps 기능을 사용하는 방법에 대한 다른 모든 Apache Cordova 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* [Visual Studio Community 2017] 이상이 설치된 PC
* [Visual Studio Tools for Apache Cordova]
* [활성 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

Visual Studio를 바이패스하고 직접 Apache Cordova 명령줄을 사용할 수도 있습니다.  명령줄을 사용하면 Mac 컴퓨터에서 자습서를 완료하는 경우 유용합니다.  명령줄을 사용하여 Apache Cordova 클라이언트 애플리케이션을 컴파일하는 작업은 이 자습서에서 다루지 않습니다.

## <a name="create-an-azure-mobile-app-backend"></a>Azure 모바일 앱 백 엔드 만들기
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트와 서버 프로젝트 구성
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Apache Cordova 앱 다운로드 및 실행
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]
<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx