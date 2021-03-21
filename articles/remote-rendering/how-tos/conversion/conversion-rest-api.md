---
title: 자산 변환 REST API
description: REST API를 통해 자산을 변환 하는 방법을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: f33e5717cd5556e72d996e7e943867c16805e71b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101705180"
---
# <a name="use-the-model-conversion-rest-api"></a>모델 변환 REST API 사용

[모델 변환](model-conversion.md) 서비스는 [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)을 통해 제어 됩니다. 이 API를 사용 하 여 변환을 만들고 변환 속성을 가져오고 기존 변환을 나열할 수 있습니다.

## <a name="rest-api-reference"></a>REST API 참조

원격 렌더링 REST API 참조 설명서와 [여기](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality) [에서 swagger 정의를 찾을 수](/rest/api/mixedreality/2021-01-01preview/remoterendering)있습니다.

서비스의 사용을 보여 주는 *Conversion.ps1* 이라는 *스크립트* 폴더의 [ARR 샘플 리포지토리에서](https://github.com/Azure/azure-remote-rendering) PowerShell 스크립트를 제공 합니다. 스크립트 및 해당 구성에 대 한 설명은 [예제 PowerShell 스크립트](../../samples/powershell-example-scripts.md)를 참조 하세요. 또한 [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java 및 Python sdk를 제공 합니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환에 Azure Blob Storage 사용](blob-storage.md)
- [모델 변환](model-conversion.md)