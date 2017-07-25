---
title: "Azure App Service Mobile Apps에서 iOS 앱 만들기 | Microsoft Docs"
description: "이 자습서에 따라 Objective-C 또는 Swift로 iOS용 Azure 모바일 앱 백 엔드 사용을 시작합니다."
services: app-service\mobile
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 36936ae66c458fcbedeec95cfa2f573a40c8af53
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="create-an-ios-app"></a>iOS 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>개요
이 자습서는 [Azure 모바일 앱](app-service-mobile-value-prop.md), 클라우드 백 엔드 서비스를 iOS 앱에 추가하는 방법을 보여줍니다. 우선 새 모바일 백 엔드를 만듭니다. 그런 다음 간단한 *할 일 목록* iOS 앱을 사용하여 Azure에 데이터를 저장합니다.

이 자습서를 완료하려면 Mac과 [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>단계 I: 새 Azure 모바일 앱 백 엔드 만들기
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>단계 II: 백 엔드 프로젝트 구성
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>단계 III: iOS 앱 다운로드 및 실행
[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

