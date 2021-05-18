---
title: 세션 관리 REST API
description: 세션을 관리하는 방법에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4323884b2dee3eeccfe71ec7817d92467450e88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950028"
---
# <a name="use-the-session-management-rest-api"></a>세션 관리 REST API 사용

Azure Remote Rendering 기능을 사용하려면 *세션* 을 만들어야 합니다. 각 세션은 Azure에서 클라이언트 디바이스가 연결할 수 있는 서버에 해당합니다. 디바이스가 연결되면 서버가 요청된 데이터를 렌더링하고 결과를 비디오 스트림으로 제공합니다. 세션을 만드는 동안 실행하려는 [서버 종류](../reference/vm-sizes.md)를 선택하여 가격 책정을 결정합니다. 세션이 더 이상 필요하지 않으면 중지해야 합니다. 수동으로 중지되지 않은 경우 세션의 *임대 시간* 이 만료되면 자동으로 종료됩니다.

## <a name="rest-api-reference"></a>REST API 참조

REST API 참조는 [여기](/rest/api/mixedreality/2021-01-01preview/remoterendering), Swagger 정의는 [여기](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)에서 찾을 수 있습니다.
*스크립트* 폴더의 [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에서 서비스의 사용을 보여 주는 *RenderingSession.ps1* 이라는 PowerShell 스크립트를 제공합니다. 스크립트 및 해당 구성에 대한 설명은 [PowerShell 스크립트 예](../samples/powershell-example-scripts.md)를 참조하세요.
또한 [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) 및 [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md)용 SDK도 제공합니다.

> [!IMPORTANT]
> 대기 시간은 원격 렌더링을 사용할 때 중요한 요소입니다. 최상의 환경을 위해 가장 가까운 지역에서 세션을 만듭니다. [Azure 대기 시간 테스트](https://www.azurespeed.com/Azure/Latency)를 사용하여 사용자에게 가장 가까운 지역을 확인할 수 있습니다.

> [!IMPORTANT]
> 클라이언트 디바이스에서 렌더링 세션에 연결하려면 ARR 런타임 SDK가 필요합니다. 이러한 SDK는 [.NET](/dotnet/api/microsoft.azure.remoterendering) 및 [C++](/cpp/api/remote-rendering/)에서 사용할 수 있습니다. 서비스에 연결하는 것 외에도 이러한 SDK를 사용하여 세션을 시작하고 중지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [인증을 위해 Azure Frontend API 사용](frontend-apis.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)