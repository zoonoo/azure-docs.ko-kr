---
title: Azure App Service Mobile Apps에서 iOS 앱 만들기 | Microsoft Docs
description: 이 자습서에 따라 Objective-C 또는 Swift로 iOS용 Azure 모바일 앱 백 엔드 사용을 시작합니다.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: cdfe395bca7ac73f00506da0180146310bd71d3d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027237"
---
# <a name="create-an-ios-app"></a>iOS 앱 만들기

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center은 모바일 앱 개발에 대 한 종단 간 및 통합 서비스의 중심을 지원 합니다. 개발자는 **빌드**, **테스트** 및 **배포** 서비스를 사용 하 여 지속적인 통합 및 배달 파이프라인을 설정할 수 있습니다. 앱이 배포 되 면 개발자는 **분석** 및 **진단** 서비스를 사용 하 여 앱의 상태와 사용 현황을 모니터링 하 고, **푸시** 서비스를 사용 하 여 사용자와 참여할 수 있습니다. 또한 개발자는 **Auth** 를 활용 하 여 사용자 및 **데이터** 서비스를 인증 하 여 클라우드에서 앱 데이터를 유지 하 고 동기화 할 수 있습니다.
> 모바일 응용 프로그램에서 클라우드 서비스를 통합 하려는 경우 현재 App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 에 등록 하세요.

## <a name="overview"></a>개요

이 자습서는 클라우드 백 엔드 서비스인 [Azure App Service Mobile Apps](app-service-mobile-value-prop.md)를 iOS 앱에 추가하는 방법을 보여줍니다. 첫 번째 단계는 Azure에서 새 모바일 백 엔드를 만드는 것입니다. 그런 다음, Azure에 데이터를 저장하는 간단한 *Todo 목록* iOS 샘플 앱을 다운로드합니다.

이 자습서를 완료하려면 Mac과 [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>새 Azure 모바일 앱 백 엔드 만들기

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트 및 서버 프로젝트를 구성 합니다.
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>IOS 앱 실행

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
