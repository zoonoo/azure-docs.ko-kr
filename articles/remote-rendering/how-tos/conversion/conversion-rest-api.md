---
title: 자산 변환 REST API
description: REST API를 통해 자산을 변환하는 방법을 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e3db4b9c9b4a05142f1327f681b067748cb1a2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951643"
---
# <a name="use-the-model-conversion-rest-api"></a>모델 변환 REST API 사용

[모델 변환](model-conversion.md) 서비스는 [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)를 통해 제어됩니다. 이 API를 사용하여 변환을 만들고 변환 속성을 가져오고 기존 변환을 나열할 수 있습니다.

## <a name="rest-api-reference"></a>REST API 참조

Remote Rendering REST API 참조 설명서는 [여기](/rest/api/mixedreality/2021-01-01/remoterendering), swagger 정의는 [여기](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)에서 찾을 수 있습니다.

*스크립트* 폴더의 [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에서 서비스의 사용을 보여 주는 *Conversion.ps1* 이라는 PowerShell 스크립트를 제공합니다. 스크립트 및 해당 구성에 대한 설명은 [PowerShell 스크립트 예](../../samples/powershell-example-scripts.md)를 참조하세요. 또한 [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) 및 [Java]( https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md)용 SDK도 제공합니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환에 Azure Blob Storage 사용](blob-storage.md)
- [모델 변환](model-conversion.md)