---
title: 자산 변환 REST API
description: REST API를 통해 자산을 변환하는 방법에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 064a7b8adaec6f47a153743b58f4e1caf50db723
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681507"
---
# <a name="use-the-model-conversion-rest-api"></a>모델 변환 REST API 사용

[모델 변환](model-conversion.md) 서비스는 REST [API를](https://en.wikipedia.org/wiki/Representational_state_transfer)통해 제어됩니다. 이 문서에서는 변환 서비스 API 세부 정보를 설명합니다.

## <a name="regions"></a>영역

요청을 보낼 기본 [URL에](../../reference/regions.md) 사용할 수 있는 지역 목록을 참조하세요.

## <a name="common-headers"></a>공통 헤더

### <a name="common-request-headers"></a>공통 요청 헤더

이러한 헤더는 모든 요청에 대해 지정해야 합니다.

- **권한 부여** 헤더는 [TOKEN]이 [서비스 액세스 토큰인](../tokens.md)"Bearer *[TOKEN]"의*값을 가져야 합니다.*TOKEN*

### <a name="common-response-headers"></a>공통 응답 헤더

모든 응답에는 다음 헤더가 포함됩니다.

- **MS-CV** 헤더에는 서비스 내에서 호출을 추적하는 데 사용할 수 있는 고유한 문자열이 포함되어 있습니다.

## <a name="endpoints"></a>엔드포인트

변환 서비스는 세 가지 REST API 끝점을 제공합니다.

- Azure 원격 렌더링 계정과 연결된 저장소 계정을 사용하여 모델 변환을 시작합니다. 
- 제공된 *SAS(공유 액세스 서명)를*사용하여 모델 변환을 시작합니다.
- 변환 상태 쿼리

### <a name="start-conversion-using-a-linked-storage-account"></a>연결된 저장소 계정을 사용하여 전환 시작
Azure 원격 렌더링 계정은 저장소 계정을 연결하는 방법에 대한 단계를 수행하여 제공된 [저장소 계정에](../create-an-account.md#link-storage-accounts)액세스할 수 있어야 합니다.

| 엔드포인트 | 방법 |
|-----------|:-----------|
| /v1/계정/계정 ID/변환/생성**accountID** | POST |

JSON 문서에 래핑된 진행 중인 변환의 ID를 반환합니다. 필드 이름은 "변환 Id"입니다.

#### <a name="request-body"></a>요청 본문


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>제공된 공유 액세스 서명을 사용하여 변환 시작
ARR 계정이 저장소 계정에 연결되어 있지 않은 경우 이 REST 인터페이스를 사용하면 *SAS(공유 액세스 서명)를*사용하여 액세스를 제공할 수 있습니다.

| 엔드포인트 | 방법 |
|-----------|:-----------|
| /v1/계정/계정 ID/변환/createWithSharedAccess서명**accountID** | POST |

JSON 문서에 래핑된 진행 중인 변환의 ID를 반환합니다. 필드 이름은 "변환 Id"입니다.

#### <a name="request-body"></a>요청 본문

요청 본문에는 위의 REST 호출 만들기와 동일하지만 입력 및 출력에는 *SAS(공유 액세스 서명) 토큰이*포함되어 있습니다. 이러한 토큰은 입력을 읽고 변환 결과를 작성하기 위한 저장소 계정에 대한 액세스를 제공합니다.

> [!NOTE]
> 이러한 SAS URI 토큰은 전체 URI가 아닌 쿼리 문자열입니다. 


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>폴링 전환 상태
위의 REST 호출 중 하나로 시작된 진행 중인 변환의 상태는 다음 인터페이스를 사용하여 쿼리할 수 있습니다.


| 엔드포인트 | 방법 |
|-----------|:-----------|
| /v1/계정/계정**accountID**ID/변환/변환ID**conversionId** | GET |

다음 값을 가질 수 있는 "상태" 필드가 있는 JSON 문서를 반환합니다.

- "달리기"
- "Success"
- "고장"

상태가 "실패"인 경우 오류 정보가 포함된 "메시지" 하위 필드가 있는 추가 "오류" 필드가 있습니다. 추가 로그가 출력 컨테이너에 업로드됩니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환에 Azure Blob 저장소 사용](blob-storage.md)
- [모델 변환](model-conversion.md)
