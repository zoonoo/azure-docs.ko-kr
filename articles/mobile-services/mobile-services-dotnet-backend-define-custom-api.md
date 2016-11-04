---
title: .NET 백 엔드 모바일 서비스에서 사용자 지정 API를 정의하는 방법 | Microsoft Docs
description: .NET 백 엔드 모바일 서비스에서 사용자 지정 API 끝점을 정의하는 방법을 알아봅니다.
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# 방법: .NET 백 엔드 모바일 서비스에서 사용자 지정 API 끝점 정의
> [!div class="op_single_selector"]
> * [JavaScript 백 엔드](mobile-services-javascript-backend-define-custom-api.md)
> * [.NET 백 엔드](mobile-services-dotnet-backend-define-custom-api.md)
> 
> 

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> 이 항목에 해당하는 모바일 앱 버전은 [방법: 사용자 지정 API 컨트롤러 정의](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-custom-api-controller)를 참조하세요.
> 
> 

이 항목에서는 .NET 백 엔드 모바일 서비스에서 사용자 지정 API 끝점을 정의하는 방법을 보여 줍니다. 사용자 지정 API를 사용하면 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능이 있는 사용자 지정 끝점을 정의할 수 있습니다. 사용자 지정 API를 사용하여 HTTP 헤더 및 본문 형식을 비롯한 메시지를 보다 효과적으로 제어할 수 있습니다.

[!INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

모바일 서비스 클라이언트 라이브러리를 사용하여 앱에서 사용자 지정 API를 호출하는 방법에 대한 자세한 내용은 클라이언트 SDK 참조에서 [사용자 지정 API 호출](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api)을 참조하세요.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0727_2016-->