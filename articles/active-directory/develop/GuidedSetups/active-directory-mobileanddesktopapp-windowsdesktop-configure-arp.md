---
title: Azure AD v2 Windows 데스크톱 시작-구성
description: Windows Desktop .NET(XAML) 애플리케이션이 액세스 토큰을 얻고 Azure Active Directory v2 엔드포인트로 보호되는 API를 호출하는 방식
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885772"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>앱에 애플리케이션의 등록 정보 추가
이 단계에서는 프로젝트에 애플리케이션 ID를 추가해야 합니다.

1.  `App.xaml.cs`를 열고 다음으로 `ClientId`를 바꿉니다.

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>다음 내용

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
