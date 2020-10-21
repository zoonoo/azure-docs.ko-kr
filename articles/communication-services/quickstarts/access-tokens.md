---
title: 빠른 시작 - 액세스 토큰 만들기 및 관리
titleSuffix: An Azure Communication Services quickstart
description: Azure Communication Services 관리 클라이언트 라이브러리를 사용하여 ID 및 액세스 토큰을 관리하는 방법을 알아봅니다.
author: tomaschladek
manager: jken
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e323f1f50fe6c67a841c300fcbec1eed3afc4497
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074127"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>빠른 시작: 액세스 토큰 만들기 및 관리

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Communication Services 관리 클라이언트 라이브러리를 사용하여 액세스 토큰을 프로비저닝하고 관리하여 Azure Communication Services를 시작하세요. 액세스 토큰을 통해 채팅 및 호출 클라이언트 라이브러리가 Azure Communication Services에 대해 직접 인증할 수 있습니다. 이러한 토큰은 구현하는 서버 쪽 토큰 프로비저닝 서비스에서 생성됩니다. 그런 다음, 클라이언트 디바이스에서 Communication Services 클라이언트 라이브러리를 초기화하는 데 사용됩니다.

이 자습서 전체에서 이미지에 표시되는 가격은 예시용일 뿐입니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

앱의 출력은 완료된 각 작업을 설명합니다.
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](./create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음과 같은 방법을 배웠습니다.

> [!div class="checklist"]
> * ID 관리
> * 액세스 토큰 발급
> * Communication Services 관리 클라이언트 라이브러리 사용


> [!div class="nextstepaction"]
> [앱에 음성 통화 추가](./voice-video-calling/getting-started-with-calling.md)

다음을 수행할 수도 있습니다.

 - [인증에 대한 자세한 정보](../concepts/authentication.md)
 - [앱에 채팅 추가](./chat/get-started.md)
 - [클라이언트 및 서버 아키텍처에 대한 자세한 정보](../concepts/client-and-server-architecture.md)
