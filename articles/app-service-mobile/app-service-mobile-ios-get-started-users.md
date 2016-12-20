---
title: "Azure 모바일 앱을 사용하여 iOS에서 인증 추가"
description: "Azure 모바일 앱을 사용하여 AAD, Google, Facebook, Twitter 및 Microsoft를 포함한 다양한 ID 공급자를 통해 iOS 앱 사용자를 인증하는 방법을 알아봅니다."
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4df1f27e5cefe9813fdc79b96d42e5c5d033901c


---
# <a name="add-authentication-to-your-ios-app"></a>iOS 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

이 자습서에서는 지원되는 ID 공급자를 사용하여 [iOS 빠른 시작 안내서] 에 인증을 추가합니다. 이 자습서는 먼저 완료해야 하는 [iOS 빠른 시작 안내서] 를 기반으로 합니다.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Xcode에서 **실행** 을 눌러 앱을 시작합니다. 앱이 인증되지 않은 사용자로 백 엔드에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 예외가 발생합니다.

## <a name="a-nameadd-authenticationaadd-authentication-to-app"></a><a name="add-authentication"></a>앱에 인증 추가
[!INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

<!-- URLs. -->

[iOS 빠른 시작 안내서]: app-service-mobile-ios-get-started.md

[Azure Portal]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


