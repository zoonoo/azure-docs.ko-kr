---
title: 세션 관리 REST API
description: 세션을 관리 하는 방법을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 9eb1bb87792dbc61e7c85dbc20c136499f23f67c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530215"
---
# <a name="use-the-session-management-rest-api"></a>세션 관리 REST API 사용

Azure 원격 렌더링 기능을 사용 하려면 *세션* 을 만들어야 합니다. 각 세션은 Azure에서 클라이언트 장치에 연결할 수 있는 서버에 해당 합니다. 장치가 연결 되 면 서버가 요청 된 데이터를 렌더링 하 고 결과를 비디오 스트림으로 사용 합니다. 세션을 만드는 동안 실행 하려는 [서버 종류](../reference/vm-sizes.md) 를 선택 하 여 가격 책정을 결정 합니다. 세션이 더 이상 필요 하지 않으면 중지 되어야 합니다. 수동으로 중지 되지 않은 경우 세션의 *임대 시간이* 만료 되 면 자동으로 종료 됩니다.

## <a name="rest-api-reference"></a>REST API 참조

[여기에 REST API 참조와 swagger](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering) 정의가 여기에 [나와 있습니다.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)
서비스의 사용을 보여 주는 *RenderingSession.ps1* 이라는 *스크립트* 폴더의 [ARR 샘플 리포지토리에서](https://github.com/Azure/azure-remote-rendering) PowerShell 스크립트를 제공 합니다. 스크립트 및 해당 구성에 대 한 설명은 [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)를 참조 하세요.
또한 [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java 및 Python sdk를 제공 합니다.

> [!IMPORTANT]
> 대기 시간은 원격 렌더링을 사용할 때 중요 한 요소입니다. 최상의 환경을 위해 가장 가까운 지역에서 세션을 만듭니다. [Azure 대기 시간 테스트](https://www.azurespeed.com/Azure/Latency) 는 사용자에 게 가장 가까운 지역을 결정 하는 데 사용할 수 있습니다.

> [!IMPORTANT]
> 클라이언트 장치에서 렌더링 세션에 연결 하려면 ARR 런타임 SDK가 필요 합니다. 이러한 Sdk는 [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering?view=remoterendering) 및 [c + +](https://docs.microsoft.com/cpp/api/remote-rendering/)에서 사용할 수 있습니다. 서비스에 연결 하는 것 외에도 이러한 Sdk를 사용 하 여 세션을 시작 하 고 중지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [인증을 위해 Azure Frontend API 사용](frontend-apis.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
