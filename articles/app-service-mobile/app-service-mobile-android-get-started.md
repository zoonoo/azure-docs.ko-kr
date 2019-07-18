---
title: Azure App Service Mobile Apps에서 Android 앱 만들기 | Microsoft Docs
description: 이 자습서에 따라 Azure 모바일 앱 백 엔드를 사용하여 Android 개발을 시작할 수 있습니다.
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: emalani
ms.openlocfilehash: 238fde023ba1b5c8b21f181655c9633329c22699
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443603"
---
# <a name="create-an-android-app"></a>Android 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발을 위한 중앙 새 및 통합 서비스에 투자 합니다. 개발자가 사용할 수 **빌드**를 **테스트** 하 고 **배포** 연속 통합 및 배달 파이프라인을 설정 하는 서비스입니다. 개발자 상태 및 사용 하 여 해당 앱의 사용량을 모니터링할 수 있습니다, 앱을 배포한 후 합니다 **Analytics** 하 고 **진단** , 서비스를 사용 하 여 사용자와 소통 하세요를 **푸시** 서비스입니다. 개발자가 활용할 수도 있습니다 **인증** 해당 사용자를 인증 하 고 **데이터** 유지 하 고 클라우드에 앱 데이터 동기화 서비스. 체크 아웃 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started) 지금 합니다.
>

## <a name="overview"></a>개요
이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 Android 모바일 앱에 추가하는 방법을 보여 줍니다.  새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 *할 일 목록* Android 앱을 만듭니다.

이 자습서를 완료해야 Azure App Service에서 Mobile Apps 기능을 사용하는 방법에 대한 다른 모든 Android 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

* Android Studio 통합 개발 환경이 포함된 [Android 개발자 도구](https://developer.android.com/sdk/index.html) 및 최신 Android 플랫폼이 필요합니다.
* Azure Mobile Android SDK.
* [활성 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>새 Azure 모바일 앱 백 엔드 만들기
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트와 서버 프로젝트 구성
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Android 앱 실행
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
