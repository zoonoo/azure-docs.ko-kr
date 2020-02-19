---
title: iOS 앱 만들기
description: 이 자습서에 따라 Swift 또는에서 iOS 개발용 Azure 모바일 앱 백 엔드를 사용 하 여 시작 하세요.
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 1b1114a22d33689f485aa228a8a1cf65eba719da
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461490"
---
# <a name="create-an-ios-app"></a>iOS 앱 만들기

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
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
