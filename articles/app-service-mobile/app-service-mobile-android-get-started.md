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
ms.openlocfilehash: 0f79d2e7112847dbc5553e60f12bc872a5c0b5d5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027647"
---
# <a name="create-an-android-app"></a>Android 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center은 모바일 앱 개발에 대 한 종단 간 및 통합 서비스의 중심을 지원 합니다. 개발자는 **빌드**, **테스트** 및 **배포** 서비스를 사용 하 여 지속적인 통합 및 배달 파이프라인을 설정할 수 있습니다. 앱이 배포 되 면 개발자는 **분석** 및 **진단** 서비스를 사용 하 여 앱의 상태와 사용 현황을 모니터링 하 고, **푸시** 서비스를 사용 하 여 사용자와 참여할 수 있습니다. 또한 개발자는 **Auth** 를 활용 하 여 사용자 및 **데이터** 서비스를 인증 하 여 클라우드에서 앱 데이터를 유지 하 고 동기화 할 수 있습니다.
> 모바일 응용 프로그램에서 클라우드 서비스를 통합 하려는 경우 현재 App Center [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 에 등록 하세요.

## <a name="overview"></a>개요
이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 Android 모바일 앱에 추가하는 방법을 보여 줍니다.  새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 *할 일 목록* Android 앱을 만듭니다.

이 자습서를 완료해야 Azure App Service에서 Mobile Apps 기능을 사용하는 방법에 대한 다른 모든 Android 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 자습서를 완료하려면 다음이 필요합니다.

* Android Studio 통합 개발 환경이 포함된 [Android 개발자 도구](https://developer.android.com/sdk/index.html) 및 최신 Android 플랫폼이 필요합니다.
* Azure Mobile Android SDK.
* [활성 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>새 Azure 모바일 앱 백 엔드 만들기
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트 및 서버 프로젝트를 구성 합니다.
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Android 앱 실행
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
