---
title: 웹 API 호출 웹 API를 프로덕션으로 이동 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 프로덕션 으로 호출하는 웹 API를 이동하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d557c70c09824f3aead41a654dc92c4ec46e9238
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885058"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>웹 API를 호출하는 웹 API: 프로덕션으로 이동

웹 API를 호출하는 토큰을 획득한 후 앱을 프로덕션으로 이동할 수 있습니다.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>자세한 정보

이제 자체 웹 API에서 웹 API를 호출하는 방법에 대한 기본 사항을 알고 있으므로 웹 API를 호출하는 보호된 웹 API를 빌드하는 데 사용되는 코드를 설명하는 다음 자습서에 관심이 있을 수 있습니다.

| 샘플 | 플랫폼 | Description |
|--------|----------|-------------|
| [액티브 디렉토리 - aspnetcore -webapi-자습서-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET 코어 2.2 웹 API, 데스크톱(WPF) | ASP.NET 코어 2.2 웹 API는 Microsoft ID 플랫폼(v2.0)을 사용하여 WPF 응용 프로그램에서 호출하는 Microsoft 그래프를 호출합니다. |
