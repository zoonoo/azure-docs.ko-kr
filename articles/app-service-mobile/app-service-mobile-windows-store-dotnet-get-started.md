---
title: Azure Mobile Apps를 사용하는 UWP(유니버설 Windows 플랫폼) 만들기 | Microsoft Docs
description: 이 자습서에 따라 Azure 모바일 앱 백 엔드를 사용하여 C#, Visual Basic 또는 JavaScript로 UWP(범용 Windows 플랫폼) 앱 개발을 시작할 수 있습니다.
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: be579b631fd910c56f2c360d6aace5b8d35c22e5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235967"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Azure 백 엔드를 사용하여 Windows 앱 만들기

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>개요

이 자습서에서는 UWP(범용 Windows 플랫폼) 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 자세한 내용은 [Mobile Apps 정의](app-service-mobile-value-prop.md)를 참조하세요. 다음은 완성된 앱의 화면 캡처입니다.

![완료된 데스크톱 앱](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

먼저 이 자습서를 완료해야만 UWP 앱용 다른 모든 모바일 앱 자습서를 학습할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Windows 10.
* Visual Studio Community 2017.
* UWP 앱 개발 경험. [UWP 자습서](https://docs.microsoft.com/windows/uwp/)를 방문하여 UWP 앱을 빌드하기 위한 [설정](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) 방법을 알아보세요.

## <a name="create-a-new-azure-mobile-app-backend"></a>새 Azure 모바일 앱 백 엔드 만들기

다음 단계에 따라 새 모바일 앱 백 엔드를 만드세요.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트와 서버 프로젝트 구성
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>클라이언트 프로젝트를 실행 합니다.

1. UWP 프로젝트를 엽니다.

2. 로 이동 합니다 [Azure portal](https://portal.azure.com/) 만든 모바일 앱으로 이동 합니다. 에 `Overview` 블레이드에서 모바일 앱에 대 한 공용 엔드포인트 URL 찾습니다. 예제-내 앱 이름 "test123"에 대 한 sitename 됩니다 https://test123.azurewebsites.net합니다.

3. 파일을 열고 `App.xaml.cs` 이 폴더-windows-uwp-cs/ZUMOAPPNAME/입니다. 응용 프로그램 이름이 `ZUMOAPPNAME`합니다.

4. `App` 클래스를 대체 `ZUMOAPPURL` 위의 공용 끝점을 사용 하 여 매개 변수입니다.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    됩니다.
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. 배포 하 고 앱을 실행 하려면 F5 키를 누릅니다.

6. 앱에서 **할 일 항목 삽입** 텍스트 상자에 *자습서 완료*와 같은 의미 있는 텍스트를 입력하고 **저장**을 클릭합니다.

    ![Windows 빠른 시작 완료 데스크톱](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다.
