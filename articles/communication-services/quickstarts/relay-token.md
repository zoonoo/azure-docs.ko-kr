---
title: 빠른 시작 - 네트워크 릴레이 토큰 가져오기
description: Azure Communication Services를 사용하여 STUN/TURN 토큰을 검색하는 방법을 알아봅니다.
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 05/21/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 2fc0bfeaef1d3e0e49d1597f41582b6d79d9a3db
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026930"
---
# <a name="quickstart-get-a-network-relay-token"></a>빠른 시작: 네트워크 릴레이 토큰 가져오기

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

이 빠른 시작에서는 Azure Communication Services TURN 서버에 액세스하기 위해 네트워크 릴레이 토큰을 검색하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free)
- 활성 Azure Communication Services 리소스. 없는 경우 [Communication Services 리소스 만들기](./create-communication-resource.md)를 참조하세요.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get a network relay token with .NET](./includes/relay-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get a network relay token with JavaScript](./includes/relay-token-js.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 리소스를 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](./create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.
