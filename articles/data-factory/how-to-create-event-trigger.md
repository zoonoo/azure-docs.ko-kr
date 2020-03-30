---
title: Azure 데이터 팩터리에서 이벤트 기반 트리거 만들기
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
ms.openlocfilehash: e4301afafb48fb9a1b0c9e36dde9800e2b8390f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443931"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>이벤트에 대한 응답으로 파이프라인을 실행하는 트리거 만들기

이 문서에서는 Data Factory 파이프라인에서 만들 수 있는 이벤트 기반 트리거를 설명합니다.

EDA(이벤트 기반 아키텍처)는 프로덕션, 검색, 소비 및 이벤트에 대한 응답이 포함된 일반적인 데이터 통합 패턴입니다. 데이터 통합 시나리오에서는 Azure Storage 계정의 파일 도착 또는 삭제와 같은 이벤트를 기반으로 데이터 팩터리 고객이 파이프라인을 트리거해야 하는 경우가 많습니다. Data Factory가 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합되어 이벤트에서 파이프 라인을 트리거 할 수 있습니다.

10분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 이 문서에서 설명하는 통합은 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)에 따라 달라집니다. 구독이 Event Grid 리소스 공급자에 등록되어 있는지 확인합니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요.

## <a name="data-factory-ui"></a>Data Factory UI

이 섹션에서는 Azure 데이터 팩터리 사용자 인터페이스 내에서 이벤트 트리거를 만드는 방법을 보여 줍니다.

1. **저작 캔버스로** 이동

1. 왼쪽 아래 모서리에서 트리거 버튼을 **클릭합니다.**

1. 클릭 **+ 새로** 생성 트리거 측면 탐색을 엽니 다

1. 트리거 유형 **이벤트** 선택

    ![새 이벤트 트리거 만들기](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Azure 구독 드롭다운에서 저장소 계정을 선택하거나 저장소 계정 리소스 ID를 수동으로 사용하여 저장 계정을 선택합니다. 이벤트가 발생할 컨테이너를 선택합니다. 컨테이너 선택은 선택 사항이지만 모든 컨테이너를 선택하면 많은 수의 이벤트가 발생할 수 있습니다.

   > [!NOTE]
   > 이벤트 트리거는 현재 Azure Data Lake 저장소 Gen2 및 범용 버전 2 저장소 계정만 지원합니다. Azure 이벤트 그리드 제한으로 인해 Azure Data Factory는 저장소 계정당 최대 500개의 이벤트 트리거만 지원합니다.

1. **Blob 경로로 시작하고** **Blob 경로는 속성으로 끝나므로** 이벤트를 수신할 컨테이너, 폴더 및 Blob 이름을 지정할 수 있습니다. 이벤트 트리거에는 이러한 속성 중 하나 이상이 정의되어야 합니다. **Blob path begins with**(Blob 경로 시작 문자) 및 **Blob path ends with**(Blob 경로 마지막 문자) 속성 모두에 대해 이 문서의 뒷부분에 나오는 예제와 같이 다양한 패턴을 사용할 수 있습니다.

    * **Blob 경로는 다음으로 시작합니다.** Blob 경로는 폴더 경로로 시작해야 합니다. 유효한 값은 `2018/` 및 `2018/april/shoes.csv`이고 컨테이너를 선택하지 않은 경우 이 필드를 선택할 수 없습니다.
    * **Blob 경로는 다음으로 끝납니다.** Blob 경로는 파일 이름 또는 확장명으로 끝나야 합니다. 유효한 값은 `shoes.csv` 및 `.csv`이고 컨테이너 및 폴더 이름은 선택 사항이지만 지정하면 `/blobs/` 세그먼트별로 구분해야 합니다. 예를 들어 'orders'라는 컨테이너의 값은 `/orders/blobs/2018/april/shoes.csv`할 수 있습니다. 컨테이너에 폴더를 지정하려면 선행 '/' 문자를 생략합니다. 예를 들어, `april/shoes.csv` 모든 컨테이너에서 'april'라고 하는 폴더에 이름이 지정된 `shoes.csv` 모든 파일에 대한 이벤트가 트리거됩니다. 

1. 트리거가 **Blob 생성** 이벤트에 응답할지, **Blob삭제된** 이벤트인지 또는 둘 다에 응답할지 선택합니다. 지정된 저장소 위치에서 각 이벤트는 트리거와 연결된 데이터 팩터리 파이프라인을 트리거합니다.

    ![이벤트 트리거 구성](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. 트리거가 바이트가 0인 Blob을 무시할지 여부를 선택합니다.

1. 트리거를 구성한 후 **다음: 데이터 미리 보기를**클릭합니다. 이 화면에는 이벤트 트리거 구성과 일치하는 기존 Blob이 표시됩니다. 특정 필터가 있는지 확인합니다. 너무 넓은 필터를 구성하는 것은 많은 수의 파일을 만들/삭제할 수 있으며 비용에 큰 영향을 미칠 수 있습니다. 필터 조건이 확인되면 **완료를**클릭합니다.

    ![이벤트 트리거 데이터 미리 보기](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. 이 트리거에 파이프라인을 연결하려면 파이프라인 캔버스로 이동하여 **트리거 추가를** 클릭하고 **새로/편집을**선택합니다. 측면 탐색이 나타나면 **트리거 선택 을 클릭하고** 드롭다운을 클릭하고 만든 트리거를 선택합니다. **다음: 데이터 미리 보기를** 클릭하여 구성이 올바른지 확인한 다음 **다음 데이터** 미리 보기가 올바른지 확인합니다.

1. 파이프라인에 매개 변수가 있는 경우 트리거 실행 매개 변수 측 탐색에서 매개 변수를 지정할 수 있습니다. 이벤트 트리거는 Blob의 폴더 경로 및 파일 이름을 `@triggerBody().folderPath` `@triggerBody().fileName`속성 및 에 캡처합니다. 파이프라인에서 이러한 속성 값을 사용하려면 속성을 파이프라인 매개 변수에 매핑해야 합니다. 속성을 매개 변수에 매핑한 후 파이프라인 전체에서 `@pipeline().parameters.parameterName` 식을 통해 트리거가 캡처한 값에 액세스할 수 있습니다. 완료되면 **완료를** 클릭합니다.

    ![속성을 파이프라인 매개 변수에 매핑](media/how-to-create-event-trigger/event-based-trigger-image4.png)

앞의 예제에서 트리거는 컨테이너 샘플 데이터의 폴더 이벤트 테스트에서 .csv로 끝나는 Blob 경로가 생성될 때 발생하도록 구성됩니다. **folderPath** 및 **fileName** 속성은 새 Blob의 위치를 캡처합니다. 예를 들어 MoviesDB.csv가 경로 샘플 데이터/이벤트 `@triggerBody().folderPath` 테스트에 추가될 때 `sample-data/event-testing` `@triggerBody().fileName` 의 값이 `moviesDB.csv`있고 의 값이 있습니다. 이러한 값은 예제에서 파이프라인 매개 `sourceFolder` 변수에 `sourceFile` 매핑되며 파이프라인 `@pipeline().parameters.sourceFolder` 전체에서 `@pipeline().parameters.sourceFile` 각각 사용할 수 있습니다.

## <a name="json-schema"></a>JSON 스키마

다음 표에는 이벤트 기반 트리거와 관련된 스키마 요소의 개요가 제공됩니다.

| **JSON 요소** | **설명** | **유형** | **허용된 값** | **필수** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | 스토리지 계정의 Azure Resource Manager 리소스 ID입니다. | String | Azure Resource Manager ID | yes |
| **이벤트** | 이 트리거를 발생시키는 이벤트 유형입니다. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | 예, 이러한 값의 조합입니다. |
| **blobPathBeginsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 시작해야 합니다. 예를 들어 `/records/blobs/december/`는 `records` 컨테이너 아래의 `december` 폴더에서 Blob에 대한 트리거만을 시작합니다. | String   | | 이러한 속성 중 하나 이상에 대한 값을 제공해야 합니다. `blobPathBeginsWith` 또는 `blobPathEndsWith` |
| **blobPathEndsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 끝나야 합니다. 예를 들어 `december/boxes.csv`는 `december` 폴더에서 `boxes`라는 이름의 Blob에 대한 트리거만을 시작합니다. | String   | | 이러한 속성 중 하나 이상에 대한 값을 제공해야 합니다. `blobPathBeginsWith` 또는 `blobPathEndsWith` |
| **비우빈블럼 무시** | 0바이트 Blob이 파이프라인 실행을 트리거할지 여부입니다. 기본적으로 true로 설정됩니다. | 부울 | true 또는 false | 예 |

## <a name="examples-of-event-based-triggers"></a>이벤트 기반 트리거의 예

이 섹션에는 이벤트 기반 트리거 설정의 예가 제공됩니다.

> [!IMPORTANT]
> 다음 예제에 표시된 대로 컨테이너 및 폴더, 컨테이너 및 파일, 컨테이너, 폴더 및 파일을 지정할 때마다 경로의 `/blobs/` 세그먼트를 포함해야 합니다. **BlobPathBeginsWith의**경우 데이터 팩터리 UI는 트리거 JSON의 폴더와 컨테이너 이름 사이에 자동으로 추가됩니다. `/blobs/`

| 속성 | 예제 | 설명 |
|---|---|---|
| **다음으로 Blob 경로 시작** | `/containername/` | 컨테이너에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/` | `containername` 컨테이너 및 `foldername` 폴더에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/subfoldername/` | 하위 폴더를 참조할 수도 있습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/file.txt` | `containername` 컨테이너 아래의 `foldername` 폴더에서 `file.txt`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `file.txt` | 모든 경로에서 `file.txt`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `/containername/blobs/file.txt` | `containername` 컨테이너 아래에서 `file.txt`라는 이름의 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `foldername/file.txt` | 모든 컨테이너 아래의 `foldername` 폴더에서 `file.txt`라는 이름의 Blob에 대한 이벤트를 받습니다. |

## <a name="next-steps"></a>다음 단계
트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#triggers)를 참조하세요.
