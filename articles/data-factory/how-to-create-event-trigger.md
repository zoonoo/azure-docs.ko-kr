---
title: Azure Data Factory에서 이벤트 기반 트리거 만들기 | Microsoft Docs
description: Azure Data Factory에서 이벤트에 대한 응답으로 파이프라인을 실행하는 트리거를 만드는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: douglasl
ms.openlocfilehash: a9c15b239ee0bd0dde0b1f11691565b2676e3d07
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062124"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>이벤트에 대한 응답으로 파이프라인을 실행하는 트리거 만들기

이 문서에서는 Data Factory 파이프라인에서 만들 수 있는 이벤트 기반 트리거를 설명합니다.

EDA(이벤트 기반 아키텍처)는 프로덕션, 검색, 소비 및 이벤트에 대한 응답이 포함된 일반적인 데이터 통합 패턴입니다. 데이터 통합 시나리오에서는 Data Factory 고객이 이벤트를 기반으로 파이프라인을 트리거해야 하는 경우가 종종 있습니다. Data Factory가 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합되어 이벤트에서 파이프 라인을 트리거 할 수 있습니다.

## <a name="data-factory-ui"></a>Data Factory UI

### <a name="create-a-new-event-trigger"></a>새 이벤트 트리거 만들기

일반적인 이벤트는 Azure Storage 계정에 파일이 도착하거나 파일을 삭제하는 것입니다. Data Factory 파이프라인에서 이런 이벤트에 응답하는 트리거를 만들 수 있습니다.

> [!NOTE]
> 이 통합은 버전 2 저장소 계정(범용)만 지원합니다.

![새 이벤트 트리거 만들기](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>이벤트 트리거 유형 선택

파일이 저장소 위치에 도착하고 해당 Blob이 만들어지는 즉시 이 이벤트는 Data Factory 파이프라인을 트리거하여 실행합니다. Blob 생성 이벤트, Blob 삭제 이벤트 또는 두 이벤트 모두에 응답하는 트리거를 Data Factory 파이프라인에 만들 수 있습니다.

![트리거 유형을 이벤트로 선택](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>이벤트 트리거 구성

**Blob path begins with**(Blob 경로 시작 문자) 및 **Blob path ends with**(Blob 경로 마지막 문자) 속성을 통해 이벤트를 수신할 컨테이너, 폴더 및 Blob 이름을 지정할 수 있습니다. **Blob path begins with**(Blob 경로 시작 문자) 및 **Blob path ends with**(Blob 경로 마지막 문자) 속성 모두에 대해 이 문서의 뒷부분에 나오는 예제와 같이 다양한 패턴을 사용할 수 있습니다. 이러한 속성 중 하나 이상이 필요합니다.

![이벤트 트리거 구성](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>JSON 스키마

다음 표에는 이벤트 기반 트리거와 관련된 스키마 요소의 개요가 제공됩니다.

| **JSON 요소** | **설명** | **형식** | **허용되는 값** | **필수** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | 저장소 계정의 Azure Resource Manager 리소스 ID입니다. | 문자열 | Azure Resource Manager ID | 예 |
| **events** | 이 트리거를 발생시키는 이벤트 유형입니다. | 배열    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | 예, 어떤 조합이든 가능합니다. |
| **blobPathBeginsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 시작해야 합니다. 예를 들어, '/records/blobs/december/'는 records 컨테이너의 december 폴더에 있는 Blob에 대해서만 트리거를 발생시킵니다. | 문자열   | | blobPathBeginsWith, blobPathEndsWith 속성 중 하나 이상을 제공해야 합니다. |
| **blobPathEndsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 끝나야 합니다. 예를 들어, 'december/boxes.csv'는 december 폴더의 boxes라는 이름의 Blob에 대해서만 트리거를 발생시킵니다. | 문자열   | | blobPathBeginsWith, blobPathEndsWith 속성 중 하나 이상을 제공해야 합니다. |

## <a name="examples-of-event-based-triggers"></a>이벤트 기반 트리거의 예

이 섹션에는 이벤트 기반 트리거 설정의 예가 제공됩니다.

-   **Blob path begins with**(Blob 경로 시작 문자)('/containername/') – 컨테이너에 있는 모든 Blob에 대한 이벤트를 수신합니다.
-   **Blob path begins with**(Blob 경로 시작 문자)('/containername/blobs/foldername') – containername 컨테이너 및 foldername 폴더에 있는 모든 Blob에 대한 이벤트를 수신합니다.
-   **Blob path begins with**(Blob 경로 시작 문자)('/containername/blobs/foldername/file.txt') – containername 컨테이너 아래 foldername 폴더에 있는 file.txt라는 이름의 Blob에 대한 이벤트를 수신합니다.
-   **Blob path ends with**(Blob 경로 마지막 문자)('file.txt') – 모든 경로에서 file.txt라는 이름의 Blob에 대한 이벤트를 수신합니다.
-   **Blob path ends with**(Blob 경로 마지막 문자)('/containername/blobs/file.txt') – containername 컨테이너에 있는 file.txt라는 이름의 Blob에 대한 이벤트를 수신합니다.
-   **Blob path ends with**(Blob 경로 마지막 문자)('/containername/file.txt') – 모든 컨테이너의 foldername 폴더에 있는 file.txt라는 이름의 Blob에 대한 이벤트를 수신합니다.

> [!NOTE]
> 컨테이너 및 폴더, 컨테이너 및 파일, 컨테이너, 폴더 및 파일을 지정할 때마다 경로의 `/blobs/` 세그먼트를 포함해야 합니다.

## <a name="using-blob-events-trigger-properties"></a>BLOB 이벤트 트리거 속성 사용

Blob 이벤트로 인해 트리거가 발생하면 파이프 라인에서 두 가지 변수 즉 *folderPath* 및 *fileName*을 사용할 수 있습니다. 이 변수에 액세스하려면 `@triggerBody().fileName` 또는 `@triggerBody().folderPath` 식을 사용합니다.

예를 들어 `.csv`를 사용하여 `blobPathEndsWith`의 값으로 Blob이 생성되면 실행되도록 구성되는 트리거가 있습니다. .csv 파일이 저장소 계정에 저장되면 *folderPath*와 *fileName*은 .csv 파일의 위치를 설명합니다. 예를 들어 *folderPath*의 값은 `/containername/foldername/nestedfoldername`이고 *fileName*의 값은 `filename.csv`입니다.

## <a name="next-steps"></a>다음 단계
트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#triggers)를 참조하세요.
