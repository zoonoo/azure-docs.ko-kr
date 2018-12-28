---
title: Azure Digital Twins에서 개체에 Blob 추가 | Microsoft Docs
description: Azure Digital Twins에서 Blob을 개체에 추가하는 방법을 알아봅니다.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 8a68ba35ddf7caacbf2339d87c5aeef80f470ba4
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725627"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Azure Digital Twins에서 개체에 Blob 추가

Blob은 그림 및 로그 같은 일반적인 파일 형식의 비정형 표현입니다. Blob은 MIME 형식(예: "image/jpeg") 및 메타데이터(이름, 설명, 형식 등)를 사용하여 표현하는 데이터의 종류를 추적합니다.

Azure Digital Twins는 Blobs를 디바이스, 공간 및 사용자에 연결할 수 있습니다. Blob은 사용자, 디바이스 사진, 비디오, 맵 또는 로그에 대한 프로필 사진을 나타낼 수 있습니다.

> [!NOTE]
> 이 문서에서는 다음과 같이 가정합니다.
> * 인스턴스가 관리 API 요청을 받도록 올바르게 구성되어 있습니다.
> * 원하는 REST 클라이언트를 사용하여 올바르게 인증했습니다.

## <a name="uploading-blobs-an-overview"></a>Blob 업로드: 개요

다중 파트 요청을 사용하여 Blob을 특정 엔드포인트 및 해당 기능에 업로드할 수 있습니다.

> [!IMPORTANT]
> 다중 파트 요청에는 세 가지 정보가 필요합니다.
> * **Content-Type** 헤더:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Content-Disposition**: `form-data; name="metadata"`
> * 업로드할 파일 콘텐츠
>
> **Content-Type** 및 **Content-Disposition** 정보는 사용 시나리오에 따라 달라질 수 있습니다.

Azure Digital Twins 관리 API에 대한 다중 파트 요청은 다음과 같은 두 파트로 구성됩니다.

* **Content-Type** 및 **Content-Disposition** 정보에서 본 것처럼, 연결된 MIME 형식 같은 Blob 메타데이터

* Blob 콘텐츠(파일의 비정형 콘텐츠)  

**PATCH** 요청에는 두 파트 중 어떤 것도 필요하지 않습니다. 두 파트는 모두 **POST** 또는 만들기 작업에 필요합니다.

### <a name="blob-metadata"></a>Blob 메타데이터

**Content-Type** 및 **Content-Disposition** 외에도, 다중 파트 요청은 올바른 JSON 본문을 지정해야 합니다. 제출할 JSON 본문은 수행되는 HTTP 요청 작업의 종류에 따라 달라집니다.

네 가지 주요 JSON 스키마는 다음과 같습니다.

![JSON 스키마][1]

Swagger 설명서는 이러한 모델 스키마에 대해 매우 자세하게 설명합니다.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[Swagger를 사용하는 방법](./how-to-use-swagger.md)을 읽고 참조 설명서를 사용하는 방법을 알아보세요.

### <a name="examples"></a>예

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

텍스트 파일을 Blob으로 업로드하고 공간과 연결하는 **POST** 요청을 만들려면:

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

다음 코드는 동일한 Blob 업로드의 .NET 구현으로 [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) 클래스를 사용합니다.

```csharp
//Supply your metadata in a suitable format
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

다음 섹션에서는 핵심 엔드포인트 및 해당 기능을 살펴봅니다.

### <a name="devices"></a>디바이스

디바이스에 Blob을 연결할 수 있습니다. 다음 이미지는 관리 API의 Swagger 참조 설명서를 보여줍니다. Blob 사용량에 대한 디바이스 관련 API 엔드포인트와 이를 전달하는 데 필요한 경로 매개 변수를 지정합니다.

![디바이스 Blob][2]

예를 들어 Blob을 업데이트하거나 만들고 디바이스에 Blob을 연결하려면 다음에 대한 **PATCH** 요청을 만듭니다.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_BLOB_ID* | 원하는 Blob ID |

요청이 성공하면 응답에 **DeviceBlob** JSON 개체가 반환됩니다. **DeviceBlob** 개체는 다음과 같은 JSON 스키마를 준수합니다.

| 특성 | type | 설명 | 예 |
| --- | --- | --- | --- |
| **DeviceBlobType** | 문자열 | 디바이스에 연결할 수 있는 Blob 범주입니다. | `Model` 및 `Specification` |
| **DeviceBlobSubtype** | 문자열 | **DeviceBlobType**보다 구체적인 Blob 하위 범주입니다. | `PhysicalModel`, `LogicalModel`, `KitSpecification` 및 `FunctionalSpecification` |

> [!TIP]
> 성공적으로 반환된 요청 데이터를 처리하려면 앞의 표를 사용하세요.

### <a name="spaces"></a>공백

Blob을 공간에 연결할 수도 있습니다. 다음 이미지는 Blob 처리를 담당하는 모든 공간 API 엔드포인트를 나열합니다. 이러한 엔드포인트를 전달할 경로 매개 변수도 나열합니다.

![공간 Blob][3]

예를 들어 공간에 연결된 Blob을 반환하려면 다음에 대한 **GET** 요청을 만듭니다.

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_BLOB_ID* | 원하는 Blob ID |

동일한 엔드포인트에 대한 **PATCH** 요청을 만들면 메타데이터 설명을 업데이트하고 새 버전의 Blob을 만들 수 있습니다. HTTP 요청은 필요한 메타 및 다중 파트 양식 데이터와 함께 **PATCH** 메서드를 통해 수행됩니다.

성공한 작업은 다음 스키마를 준수하는 **SpaceBlob** 개체를 반환합니다. 이 개체를 사용하여 반환된 데이터를 사용할 수 있습니다.

| 특성 | type | 설명 | 예 |
| --- | --- | --- | --- |
| **SpaceBlobType** | 문자열 | 공간에 연결할 수 있는 Blob 범주입니다. | `Map` 및 `Image` |
| **SpaceBlobSubtype** | 문자열 | **SpaceBlobType**보다 구체적인 Blob 하위 범주입니다. | `GenericMap`, `ElectricalMap`, `SatelliteMap` 및 `WayfindingMap` |

### <a name="users"></a>사용자

Blob을 사용자 모델에 연결할 수 있습니다(예: 프로필 사진 연결). 다음 이미지는 관련 사용자 API 엔드포인트 및 필요한 경로 매개 변수(예: `id`)를 보여줍니다.

![사용자 Blob][4]

예를 들어 사용자에게 연결된 Blob을 가져오려면 필요한 양식 데이터를 사용하여 **GET** 요청을 수행합니다.

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_BLOB_ID* | 원하는 Blob ID |

반환된 JSON(**UserBlob** 개체)은 다음 JSON 모델을 준수합니다.

| 특성 | type | 설명 | 예 |
| --- | --- | --- | --- |
| **UserBlobType** | 문자열 | 사용자에 연결할 수 있는 Blob 범주입니다. | `Image` 및 `Video` |
| **UserBlobSubtype** |  문자열 | **UserBlobType**보다 구체적인 Blob 하위 범주입니다. | `ProfessionalImage`, `VacationImage`, `CommercialVideo` |

## <a name="common-errors"></a>일반 오류

일반 오류는 올바른 헤더 정보를 포함하고 있지 않습니다.

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에 대한 Swagger 참조 설명서의 자세한 내용은 [Azure Digital Twins Swagger 사용](how-to-use-swagger.md)을 참조하세요.

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
