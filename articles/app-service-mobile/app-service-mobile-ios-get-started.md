---
title: Azure App Service Mobile Apps에서 iOS 앱 만들기 | Microsoft Docs
description: 이 자습서에 따라 Objective-C 또는 Swift로 iOS용 Azure 모바일 앱 백 엔드 사용을 시작합니다.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 60190e0f8441d52b3d753e1dc79c67f480434dbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240274"
---
# <a name="create-an-ios-app"></a>iOS 앱 만들기

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>개요

이 자습서는 클라우드 백 엔드 서비스인 [Azure App Service Mobile Apps](app-service-mobile-value-prop.md)를 iOS 앱에 추가하는 방법을 보여줍니다. 첫 번째 단계는 Azure에서 새 모바일 백 엔드를 만드는 것입니다. 그런 다음, Azure에 데이터를 저장하는 간단한 *Todo 목록* iOS 샘플 앱을 다운로드합니다.

이 자습서를 완료하려면 Mac과 [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>새 Azure 모바일 앱 백 엔드 만들기

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트와 서버 프로젝트 구성
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>IOS 앱 실행

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
