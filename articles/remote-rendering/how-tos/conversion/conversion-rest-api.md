---
title: 자산 변환 REST API
description: REST API를 통해 자산을 변환 하는 방법을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 0a0feb6b638cb6e3a74fcd30baea5e8a04375699
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82857795"
---
# <a name="use-the-model-conversion-rest-api"></a>모델 변환 REST API 사용

[모델 변환](model-conversion.md) 서비스는 [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)을 통해 제어 됩니다. 이 문서에서는 변환 서비스 API 세부 정보에 대해 설명 합니다.

## <a name="regions"></a>영역

요청을 보낼 기본 Url에 대 한 [사용 가능한 지역 목록을](../../reference/regions.md) 참조 하세요.

## <a name="common-headers"></a>공용 헤더

### <a name="common-request-headers"></a>일반 요청 헤더

모든 요청에 대해 다음 헤더를 지정 해야 합니다.

- **권한 부여** 헤더의 값은 "전달자 [*token*]" 이어야 합니다. 여기서 [*token*]은 [서비스 액세스 토큰](../tokens.md)입니다.

### <a name="common-response-headers"></a>일반적인 응답 헤더

모든 응답에는 다음 헤더가 포함 됩니다.

- **MS CV** 헤더에는 서비스 내에서 호출을 추적 하는 데 사용할 수 있는 고유한 문자열이 포함 되어 있습니다.

## <a name="endpoints"></a>엔드포인트

변환 서비스는 다음의 세 가지 REST API 끝점을 제공 합니다.

- Azure 원격 렌더링 계정으로 연결 된 저장소 계정을 사용 하 여 모델 변환을 시작 합니다. 
- 제공 된 *SAS (공유 액세스 서명)* 를 사용 하 여 모델 변환을 시작 합니다.
- 변환 상태 쿼리

### <a name="start-conversion-using-a-linked-storage-account"></a>연결 된 저장소 계정을 사용 하 여 변환 시작
Azure 원격 렌더링 계정에는 [저장소 계정을 연결](../create-an-account.md#link-storage-accounts)하는 방법에 대 한 단계를 수행 하 여 제공 된 저장소 계정에 대 한 액세스 권한이 있어야 합니다.

| 엔드포인트 | 메서드 |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/create | POST |

JSON 문서에 래핑된 진행 중인 변환의 ID를 반환 합니다. 필드 이름은 "conversionId"입니다.

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
### <a name="start-conversion-using-provided-shared-access-signatures"></a>제공 된 공유 액세스 서명을 사용 하 여 변환 시작
ARR 계정이 저장소 계정에 연결 되지 않은 경우이 REST 인터페이스를 사용 하 여 *SAS (공유 액세스 서명)* 를 사용 하 여 액세스를 제공할 수 있습니다.

| 엔드포인트 | 메서드 |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/createWithSharedAccessSignature | POST |

JSON 문서에 래핑된 진행 중인 변환의 ID를 반환 합니다. 필드 이름은 "conversionId"입니다.

#### <a name="request-body"></a>요청 본문

요청 본문은 위의 create REST 호출에서와 동일 하지만 입력 및 출력에는 *SAS (공유 액세스 서명) 토큰이*포함 되어 있습니다. 이러한 토큰은 입력을 읽고 변환 결과를 쓸 수 있도록 저장소 계정에 대 한 액세스를 제공 합니다.

> [!NOTE]
> 이러한 SAS URI 토큰은 쿼리 문자열이 며 전체 URI가 아닙니다. 


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

### <a name="poll-conversion-status"></a>폴링 변환 상태
위의 REST 호출 중 하나로 시작 하는 진행 중인 변환의 상태는 다음 인터페이스를 사용 하 여 쿼리할 수 있습니다.


| 엔드포인트 | 메서드 |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/**conversionId** | GET |

다음 값을 가질 수 있는 "status" 필드가 포함 된 JSON 문서를 반환 합니다.

- 만들어지며
- 이상을
- "Success"
- 실패로

상태가 "오류" 인 경우 오류 정보를 포함 하는 "message" 하위 필드가 있는 추가 "오류" 필드가 나타납니다. 추가 로그가 출력 컨테이너에 업로드 됩니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환에 Azure Blob Storage 사용](blob-storage.md)
- [모델 변환](model-conversion.md)
