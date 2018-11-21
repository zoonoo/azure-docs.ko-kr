---
title: Azure Digital Twins에서 Blob을 개체에 추가하는 방법 | Microsoft Docs
description: Azure Digital Twins에서 Blob을 개체에 추가하는 방법을 알아봅니다.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688335"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Azure Digital Twins에서 Blob을 개체에 추가하는 방법

Blob은 일반적인 파일 형식(예: 그림 및 로그)의 구조화되지 않은 표현입니다. Blob은 MIME 형식(예: "image/jpeg") 및 메타데이터(이름, 설명, 형식 등)를 사용하여 표현하는 데이터의 종류를 추적합니다.

Azure Digital Twins는 Blobs를 디바이스, 공간 및 사용자에 연결할 수 있습니다. Blob은 사용자, 디바이스 사진, 비디오, 맵 또는 로그에 대한 프로필 사진을 나타낼 수 있습니다.

> [!NOTE]
> 이 문서에서는 다음과 같이 가정합니다.
> * 인스턴스가 관리 API 요청을 받도록 올바르게 구성되어 있습니다.
> * 선택한 REST 클라이언트를 사용하여 올바르게 인증했습니다.

## <a name="uploading-blobs-an-overview"></a>Blob 업로드: 개요

다중 파트 요청은 Blob을 특정 엔드포인트 및 해당 기능에 업로드하는 데 사용됩니다.

> [!IMPORTANT]
> 다중 파트 요청에는 세 가지 필수 정보가 필요합니다. Azure Digital Twins의 경우 다음과 같습니다.
> * **Content-Type** 헤더:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Content-Disposition**: `form-data; name="metadata"`
> * 업로드할 파일 콘텐츠
>
> 정확한 **Content-Type** 및 **Content-Disposition**은 사용 시나리오에 따라 달라질 수 있습니다.

각 다중 파트 요청은 여러 청크로 분할됩니다. Azure Digital Twins 관리 API에 대한 다중 파트 요청은 다음과 같이 **두**(**2**) 파트로 구분됩니다.

1. 첫 번째 파트는 필수이며, 위의 **Content-Type** 및 **Content-Disposition**에 따라 연결된 MIME 형식과 같은 Blob 메타데이터를 포함합니다.

1. 두 번째 파트는 실제 Blob 콘텐츠(파일의 구조화되지 않은 내용)를 포함합니다.  

**PATCH** 요청에는 두 파트가 모두 필요하지 않습니다. 두 파트는 모두 **POST** 또는 만들기 작업에 필요합니다.

### <a name="blob-metadata"></a>Blob 메타데이터

**Content-Type** 및 **Content-Disposition** 외에도 다중 파트 요청은 올바른 JSON 본문도 지정해야 합니다. 제출할 JSON 본문은 수행되는 HTTP 요청 작업의 종류에 따라 달라집니다.

사용되는 네 가지 주요 JSON 스키마는 다음과 같습니다.

![공간 Blob][1]

이러한 모델 스키마는 제공된 Swagger 설명서에서 자세히 설명하고 있습니다.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[Swagger를 사용하는 방법](./how-to-use-swagger.md)을 참조하여 제공된 참조 설명서를 사용하는 방법에 대해 알아봅니다.

### <a name="examples"></a>예

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

텍스트 파일을 Blob으로 업로드하고 이를 공간과 연결하는 POST 요청을 만듭니다.

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| 매개 변수 값 | 다음 항목으로 교체 |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | 다중 파트 콘텐츠 경계 이름 |

아래에 제공된 동일한 Blob 업로드의 .NET 구현에서는 [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) 클래스를 사용합니다.

```csharp
//Supply your metaData in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>API 엔드포인트

아래에는 핵심 엔드포인트와 해당 특정 기능에 대한 연습이 제공됩니다.

### <a name="devices"></a>장치

Blob은 디바이스에 연결할 수 있습니다. 아래 이미지(관리 API에 대한 Swagger 참조 설명서의 표현)에는 Blob 사용에 대한 디바이스 관련 API 엔드포인트 및 이 엔드포인트에 전달하는 데 필요한 경로 매개 변수가 나와 있습니다.

![디바이스 Blob][2]

예를 들어 Blob을 업데이트하거나 만들고 디바이스에 이 Blob을 연결하려면 PATCH 요청을 수행합니다.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_BLOB_ID* | 원하는 Blob ID |

요청이 성공하면 응답에 DeviceBlob JSON 개체가 반환됩니다. DeviceBlobs에서 준수하는 JSON 스키마는 다음과 같습니다.

| 특성 | type | 설명 | 예 |
| --- | --- | --- | --- |
| **DeviceBlobType** | 문자열 | 디바이스에 연결할 수 있는 Blob 범주입니다. | `Model` 및 `Specification` |
| **DeviceBlobSubtype*** | 문자열 | DeviceBlobType보다 더 세분화된 Blob 하위 범주입니다. | `PhysicalModel`, `LogicalModel`, `KitSpecification` 및 `FunctionalSpecification` |

> [!TIP]
> 성공적으로 반환된 요청 데이터를 처리하려면 위의 표를 사용하세요.

### <a name="spaces"></a>공백

Blob은 공간에도 연결할 수 있습니다. 아래 이미지에는 Blob을 처리하는 모든 공간 API 엔드포인트와 이 엔드포인트에 전달할 경로 매개 변수가 나와 있습니다.

![공간 Blob][3]

예를 들어 공간에 연결된 Blob을 반환하려면 GET 요청을 수행합니다.

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_BLOB_ID* | 원하는 Blob ID |

PATCH 요청을 동일한 엔드포인트로 수행하면 메타데이터 설명을 업데이트하고 새 버전의 Blob을 만들 수 있습니다. HTTP 요청은 필요한 메타 및 다중 파트 양식 데이터와 함께 PATCH 메서드를 사용하여 수행됩니다.

작업이 성공하면 다음 스키마를 준수하고 반환된 데이터를 사용하는 데 사용할 수 있는 SpaceBlob이 반환됩니다.

| 특성 | type | 설명 | 예 |
| --- | --- | --- | --- |
| **SpaceBlobType** | 문자열 | 공간에 연결할 수 있는 Blob 범주입니다. | `Map` 및 `Image` |
| **SpaceBlobSubtype** | 문자열 | SpaceBlobType보다 더 세분화된 Blob 하위 범주입니다. | `GenericMap`, `ElectricalMap`, `SatelliteMap` 및 `WayfindingMap` |

### <a name="users"></a>사용자

Blob은 사용자 모델에 연결할 수 있습니다(예: 프로필 사진 연결). 아래 이미지에는 관련 사용자 API 엔드포인트 및 필요한 경로 매개 변수(예: `id`)가 나와 있습니다.

![사용자 Blob][4]

예를 들어 사용자에게 연결된 Blob을 가져오려면 필요한 양식 데이터를 사용하여 GET 요청을 수행합니다.

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_BLOB_ID* | 원하는 Blob ID |

반환된 JSON(UserBlobs)에서 준수하는 JSON 모델은 다음과 같습니다.

| 특성 | type | 설명 | 예 |
| --- | --- | --- | --- |
| **UserBlobType** | 문자열 | 사용자에 연결할 수 있는 Blob 범주입니다. | `Image` 및 `Video` |
| **UserBlobSubtype** |  문자열 | UserBlobType보다 더 세분화된 Blob 하위 범주입니다. | `ProfessionalImage`, `VacationImage`, `CommercialVideo` |

## <a name="common-errors"></a>일반 오류

올바른 헤더 정보를 포함하고 있지 않습니다.

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>다음 단계

제공된 Azure Digital Twins Swagger 참조 설명서에 대한 자세한 내용은 [Digital Twins Swagger를 사용하는 방법](how-to-use-swagger.md)을 참조하세요.

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
