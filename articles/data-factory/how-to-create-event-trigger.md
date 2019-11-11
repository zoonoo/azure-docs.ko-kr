---
title: Azure Data Factory에서 이벤트 기반 트리거 만들기
description: Azure Data Factory에서 이벤트에 대한 응답으로 파이프라인을 실행하는 트리거를 만드는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: 0b1d9fad2992397a3a6768d0f5e7ff26a400a2b3
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889318"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>이벤트에 대한 응답으로 파이프라인을 실행하는 트리거 만들기

이 문서에서는 Data Factory 파이프라인에서 만들 수 있는 이벤트 기반 트리거를 설명합니다.

EDA(이벤트 기반 아키텍처)는 프로덕션, 검색, 소비 및 이벤트에 대한 응답이 포함된 일반적인 데이터 통합 패턴입니다. 데이터 통합 시나리오에서는 고객이 Azure Storage 계정의 파일 도착 또는 삭제와 같은 이벤트를 기반으로 파이프라인을 트리거하기를 Data Factory 해야 하는 경우가 많습니다. Data Factory가 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합되어 이벤트에서 파이프 라인을 트리거 할 수 있습니다.

10분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 이 문서에서 설명하는 통합은 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)에 따라 달라집니다. 구독이 Event Grid 리소스 공급자에 등록되어 있는지 확인합니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/resource-manager-supported-services.md#azure-portal)을 참조하세요.

## <a name="data-factory-ui"></a>Data Factory UI

이 섹션에서는 Azure Data Factory 사용자 인터페이스 내에서 이벤트 트리거를 만드는 방법을 보여 줍니다.

1. **제작 캔버스로** 이동

1. 왼쪽 아래 모서리에서 **트리거** 단추를 클릭 합니다.

1. **+ 새로** 만들기를 클릭 하 여 트리거 쪽 탐색을 엽니다.

1. 트리거 유형 **이벤트** 선택

    ![새 이벤트 트리거 만들기](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Azure 구독 드롭다운에서 저장소 계정을 선택 하거나 저장소 계정 리소스 ID를 사용 하 여 수동으로 선택 합니다. 이벤트를 발생 시키려는 컨테이너를 선택 합니다. 컨테이너 선택은 선택 사항 이지만 모든 컨테이너를 선택 하면 많은 수의 이벤트가 발생할 수 있습니다.

   > [!NOTE]
   > 이벤트 트리거는 현재 Azure Data Lake Storage Gen2 및 범용 버전 2 저장소 계정만 지원 합니다. Azure Event Grid 제한으로 인해 Azure Data Factory는 저장소 계정 당 최대 500 이벤트 트리거를 지원 합니다.

1. **Blob 경로는로 시작** 하 고 **blob 경로는 속성으로 끝남** 이벤트를 수신 하려는 컨테이너, 폴더 및 blob 이름을 지정할 수 있습니다. 이벤트 트리거를 사용 하려면 이러한 속성 중 하나 이상을 정의 해야 합니다. **Blob path begins with**(Blob 경로 시작 문자) 및 **Blob path ends with**(Blob 경로 마지막 문자) 속성 모두에 대해 이 문서의 뒷부분에 나오는 예제와 같이 다양한 패턴을 사용할 수 있습니다.

    * **다음으로 시작 하는 Blob 경로:** Blob 경로는 폴더 경로로 시작 해야 합니다. 유효한 값에는 `2018/` 및 `2018/april/shoes.csv`이 있습니다. 컨테이너를 선택 하지 않은 경우에는이 필드를 선택할 수 없습니다.
    * **Blob 경로가 다음으로 끝남:** Blob 경로는 파일 이름 또는 확장명으로 끝나야 합니다. 유효한 값에는 `shoes.csv` 및 `.csv`이 있습니다. 컨테이너 및 폴더 이름은 선택 사항 이지만 지정 된 경우 `/blobs/` 세그먼트로 구분 되어야 합니다. 예를 들어 ' orders ' 라는 컨테이너는 `/orders/blobs/2018/april/shoes.csv`값을 가질 수 있습니다. 컨테이너에 폴더를 지정 하려면 선행 '/' 문자를 생략 합니다. 예를 들어 `april/shoes.csv`은 모든 컨테이너에서 ' 4 월 ' 이라는 폴더의 `shoes.csv` 이라는 모든 파일에서 이벤트를 트리거합니다. 

1. 트리거가 **blob created** 이벤트, **blob deleted** 이벤트 또는 둘 다에 응답할지 여부를 선택 합니다. 지정 된 저장소 위치에서 각 이벤트는 트리거와 연결 된 Data Factory 파이프라인을 트리거합니다.

    ![이벤트 트리거 구성](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. 트리거가 0 바이트로 blob을 무시 하는지 여부를 선택 합니다.

1. 트리거를 구성 했으면 **다음: 데이터 미리 보기**를 클릭 합니다. 이 화면에는 이벤트 트리거 구성과 일치 하는 기존 blob이 표시 됩니다. 특정 필터가 있는지 확인 합니다. 너무 넓은 필터 구성은 생성/삭제 된 많은 수의 파일을 일치 시킬 수 있으며 비용에 크게 영향을 줄 수 있습니다. 필터 조건이 확인 되 면 **마침**을 클릭 합니다.

    ![이벤트 트리거 데이터 미리 보기](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. 파이프라인을이 트리거에 연결 하려면 파이프라인 캔버스로 이동 하 여 **트리거 추가** 를 클릭 하 고 **새로 만들기/편집**을 선택 합니다. 측면 탐색이 나타나면 **트리거 선택** ... 드롭다운을 클릭 하 고 만든 트리거를 선택 합니다. **다음: 데이터 미리 보기** 를 클릭 하 여 구성이 올바른지 확인 한 **다음 데이터** 미리 보기가 올바른지 확인 합니다.

1. 파이프라인에 매개 변수가 있는 경우 트리거 실행 매개 변수 쪽 탐색에서 지정할 수 있습니다. 이벤트 트리거는 blob의 폴더 경로 및 파일 이름을 `@triggerBody().folderPath` 속성에 캡처하고 `@triggerBody().fileName`합니다. 파이프라인에서 이러한 속성 값을 사용하려면 속성을 파이프라인 매개 변수에 매핑해야 합니다. 속성을 매개 변수에 매핑한 후 파이프라인 전체에서 `@pipeline().parameters.parameterName` 식을 통해 트리거가 캡처한 값에 액세스할 수 있습니다. 완료 되 면 **마침** 을 클릭 합니다.

    ![속성을 파이프라인 매개 변수에 매핑](media/how-to-create-event-trigger/event-based-trigger-image4.png)

위의 예제에서 트리거는 컨테이너 샘플 데이터의 폴더 이벤트 테스트에서 .csv로 끝나는 blob 경로를 만들 때 발생 하도록 구성 됩니다. **FolderPath** 및 **fileName** 속성은 새 blob의 위치를 캡처합니다. 예를 들어 MoviesDB이 경로 샘플-데이터/이벤트 테스트에 추가 되 면 `@triggerBody().folderPath`에 `sample-data/event-testing` 값이 포함 되 고 `@triggerBody().fileName`에는 `moviesDB.csv`값이 있습니다. 이러한 값은 예제에서 파이프라인 매개 변수에 `sourceFolder` 하 고 파이프라인 전체에서 사용 될 수 있는 `sourceFile` 파이프라인 매개 변수에는 각각 `@pipeline().parameters.sourceFolder` 및 `@pipeline().parameters.sourceFile`로 매핑됩니다.

## <a name="json-schema"></a>JSON 스키마

다음 표에는 이벤트 기반 트리거와 관련된 스키마 요소의 개요가 제공됩니다.

| **JSON 요소** | **설명** | **형식** | **허용되는 값** | **필수** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | 스토리지 계정의 Azure Resource Manager 리소스 ID입니다. | 문자열 | Azure Resource Manager ID | 예 |
| **events** | 이 트리거를 발생시키는 이벤트 유형입니다. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | 예, 이러한 값의 조합입니다. |
| **blobPathBeginsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 시작해야 합니다. 예를 들어 `/records/blobs/december/`는 `december` 컨테이너 아래의 `records` 폴더에서 Blob에 대한 트리거만을 시작합니다. | 문자열   | | 이러한 속성 중 하나 이상에 대한 값을 제공해야 합니다. `blobPathBeginsWith` 또는 `blobPathEndsWith` |
| **blobPathEndsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 끝나야 합니다. 예를 들어 `december/boxes.csv`는 `boxes` 폴더에서 `december`라는 이름의 Blob에 대한 트리거만을 시작합니다. | 문자열   | | 이러한 속성 중 하나 이상에 대한 값을 제공해야 합니다. `blobPathBeginsWith` 또는 `blobPathEndsWith` |
| **ignoreEmptyBlobs** | 0 바이트 blob이 파이프라인 실행을 트리거할 지 여부입니다. 이는 기본적으로 true로 설정 됩니다. | 부울 | true 또는 false | 아니오 |

## <a name="examples-of-event-based-triggers"></a>이벤트 기반 트리거의 예

이 섹션에는 이벤트 기반 트리거 설정의 예가 제공됩니다.

> [!IMPORTANT]
> 다음 예제에 표시된 대로 컨테이너 및 폴더, 컨테이너 및 파일, 컨테이너, 폴더 및 파일을 지정할 때마다 경로의 `/blobs/` 세그먼트를 포함해야 합니다. **BlobPathBeginsWith**의 경우 Data Factory UI는 JSON 트리거의 폴더와 컨테이너 이름 사이에 `/blobs/`를 자동으로 추가 합니다.

| 속성 | 예 | 설명 |
|---|---|---|
| **다음으로 Blob 경로 시작** | `/containername/` | 컨테이너에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/` | `containername` 컨테이너 및 `foldername` 폴더에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/subfoldername/` | 하위 폴더를 참조할 수도 있습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/file.txt` | `file.txt` 컨테이너 아래의 `foldername` 폴더에서 `containername`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `file.txt` | 모든 경로에서 `file.txt`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `/containername/blobs/file.txt` | `file.txt` 컨테이너 아래에서 `containername`라는 이름의 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `foldername/file.txt` | 모든 컨테이너 아래의 `file.txt` 폴더에서 `foldername`라는 이름의 Blob에 대한 이벤트를 받습니다. |

## <a name="next-steps"></a>다음 단계
트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#triggers)를 참조하세요.
