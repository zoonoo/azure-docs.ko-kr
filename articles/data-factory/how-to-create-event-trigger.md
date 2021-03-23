---
title: Azure Data Factory에서 이벤트 기반 트리거 만들기
description: Azure Data Factory에서 이벤트에 대한 응답으로 파이프라인을 실행하는 트리거를 만드는 방법을 알아봅니다.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: deaa414a17240e8cdbdad7f4ba9b3e596b4f191f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780330"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>저장소 이벤트에 대 한 응답으로 파이프라인을 실행 하는 트리거 만들기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Data Factory 파이프라인에서 만들 수 있는 저장소 이벤트 트리거에 대해 설명 합니다.

EDA(이벤트 기반 아키텍처)는 프로덕션, 검색, 소비 및 이벤트에 대한 응답이 포함된 일반적인 데이터 통합 패턴입니다. 데이터 통합 시나리오에서는 고객이 Azure Blob Storage 계정의 파일 도착 또는 삭제와 같이 저장소 계정에서 발생 하는 이벤트를 기반으로 파이프라인을 트리거하기를 Data Factory 해야 하는 경우가 많습니다. Data Factory는 기본적으로 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합 되므로 이러한 이벤트에서 파이프라인을 트리거할 수 있습니다.

10분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> 이 문서에서 설명하는 통합은 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)에 따라 달라집니다. 구독이 Event Grid 리소스 공급자에 등록되어 있는지 확인합니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요. *Microsoft EventGrid/Eventgrid/** 작업을 수행할 수 있어야 합니다. 이 작업은 EventGrid Eventgrid 참여자 기본 제공 역할의 일부입니다.

## <a name="data-factory-ui"></a>Data Factory UI

이 섹션에서는 Azure Data Factory 사용자 인터페이스 내에서 저장소 이벤트 트리거를 만드는 방법을 보여 줍니다.

1. 연필 기호와 함께 표시 되는 **편집** 탭으로 전환 합니다.

1. 메뉴에서 **트리거** 를 선택 하 고 **새로 만들기/편집** 을 선택 합니다.

1. **트리거 추가** 페이지에서 **트리거 선택**...을 선택 하 고 **+ 새로 만들기** 를 선택 합니다.

1. 트리거 유형 **저장소 이벤트** 를 선택 합니다.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image1.png" alt-text="Data Factory UI에서 새 저장소 이벤트 트리거를 만드는 작성자 페이지의 스크린샷":::

1. Azure 구독 드롭다운에서 또는 스토리지 계정 리소스 ID를 수동으로 사용하여 스토리지 계정을 선택합니다. 이벤트가 발생할 컨테이너를 선택합니다. 컨테이너 선택은 선택 사항이지만 모든 컨테이너를 선택하면 많은 이벤트가 발생할 수 있습니다.

   > [!NOTE]
   > 저장소 이벤트 트리거는 현재 Azure Data Lake Storage Gen2 및 범용 버전 2 저장소 계정만 지원 합니다. Azure Event Grid 제한으로 인해 Azure Data Factory는 저장소 계정 당 최대 500 개의 저장소 이벤트 트리거만 지원 합니다.

   > [!NOTE]
   > 새 저장소 이벤트 트리거를 만들거나 수정 하려면 Data Factory에 로그인 하 고 저장소 이벤트 트리거를 게시 하는 데 사용 되는 Azure 계정에 저장소 계정에 대 한 적절 한 역할 기반 액세스 제어 (Azure RBAC) 권한이 있어야 합니다. 추가 권한이 필요 하지 않습니다. Azure Data Factory에 대 한 서비스 주체에는 저장소 계정 또는 Event Grid에 대 한 특별 한 권한이 필요 _하지_ 않습니다. 액세스 제어에 대 한 자세한 내용은 [역할 기반 액세스 제어](#role-based-access-control) 섹션을 참조 하세요.

1. **Blob 경로 시작 문자** 및 **Blob 경로 마지막 문자** 속성을 통해 이벤트를 수신할 컨테이너, 폴더 및 Blob 이름을 지정할 수 있습니다. 저장소 이벤트 트리거에는 이러한 속성 중 하나 이상이 정의 되어 있어야 합니다. **Blob path begins with**(Blob 경로 시작 문자) 및 **Blob path ends with**(Blob 경로 마지막 문자) 속성 모두에 대해 이 문서의 뒷부분에 나오는 예제와 같이 다양한 패턴을 사용할 수 있습니다.

    * **Blob 경로 시작 문자:** Blob 경로는 폴더 경로로 시작해야 합니다. 유효한 값은 `2018/` 및 `2018/april/shoes.csv`이고 컨테이너를 선택하지 않으면 이 필드를 선택할 수 없습니다.
    * **Blob 경로 마지막 문자:** Blob 경로는 파일 이름 또는 확장명으로 끝나야 합니다. 유효한 값은 `shoes.csv` 및 `.csv`이고 컨테이너 및 폴더 이름은 선택 사항이지만 지정된 경우 `/blobs/` 세그먼트로 구분해야 합니다. 예를 들어 이름이 ‘orders’인 컨테이너는 `/orders/blobs/2018/april/shoes.csv` 값을 가질 수 있습니다. 컨테이너의 폴더를 지정하려면 선행 '/' 문자를 생략합니다. 예를 들어 `april/shoes.csv`는 모든 컨테이너에서 이름이 'april'인 폴더에 있는 이름이 `shoes.csv`인 모든 파일에 대해 이벤트를 트리거합니다.
    * Blob 경로는로 **시작** 하 고 **로 끝납니다** . 저장소 이벤트 트리거에서 유일한 패턴 일치를 사용할 수 있습니다. 다른 유형의 와일드 카드 일치는 트리거 유형에 대해 지원 되지 않습니다.

1. 트리거가 **Blob 생성** 이벤트, **Blob 삭제** 이벤트 또는 둘 다에 응답할지 선택합니다. 지정된 스토리지 위치에서 각각의 이벤트는 트리거와 연결된 Data Factory 파이프라인을 트리거합니다.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image2.png" alt-text="저장소 이벤트 트리거 만들기 페이지의 스크린샷":::

1. 트리거가 0 바이트의 blob을 무시 하는지 여부를 선택 합니다.

1. 트리거를 구성한 후 **다음: 데이터 미리 보기** 를 클릭 합니다. 이 화면에는 저장소 이벤트 트리거 구성과 일치 하는 기존 blob이 표시 됩니다. 특정 필터가 있는지 확인합니다. 너무 광범위한 필터를 구성하면 많은 생성/삭제된 파일에 대한 일치를 수행하므로 비용에 상당한 영향을 미칠 수 있습니다. 필터 조건이 확인되면 **마침** 을 클릭합니다.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image3.png" alt-text="저장소 이벤트 트리거 미리 보기 페이지의 스크린샷":::

1. 파이프라인을이 트리거에 연결 하려면 파이프라인 캔버스로 이동 하 여 **트리거** 를 클릭 하 고 **새로 만들기/편집** 을 선택 합니다. 측면 탐색이 나타나면 **트리거 선택...** 드롭다운을 클릭하고 사용자가 만든 트리거를 선택합니다. **다음: 데이터 미리 보기** 를 클릭하여 구성이 정확한지 확인하고 **다음** 을 클릭하여 데이터 미리 보기가 올바른지 유효성을 검사합니다.

1. 파이프라인에 매개 변수가 있는 경우 트리거 실행 매개 변수 측 탐색에서 지정할 수 있습니다. Storage 이벤트 트리거는 blob의 폴더 경로 및 파일 이름을 속성 및에 캡처합니다 `@triggerBody().folderPath` `@triggerBody().fileName` . 파이프라인에서 이러한 속성 값을 사용하려면 속성을 파이프라인 매개 변수에 매핑해야 합니다. 속성을 매개 변수에 매핑한 후 파이프라인 전체에서 `@pipeline().parameters.parameterName` 식을 통해 트리거가 캡처한 값에 액세스할 수 있습니다. 자세한 설명은 [파이프라인의 참조 트리거 메타 데이터](how-to-use-trigger-parameterization.md) 를 참조 하세요.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image4.png" alt-text="파이프라인 매개 변수에 대 한 저장소 이벤트 트리거 매핑 속성의 스크린샷":::

    위의 예제에서 트리거는 컨테이너 _샘플 데이터_ 의 폴더 _이벤트 테스트_ 에서 .csv로 끝나는 blob 경로를 만들 때 발생 하도록 구성 됩니다. **folderPath** 및 **fileName** 속성은 새 Blob의 위치를 캡처합니다. 예를 들어 MoviesDB.csv가 sample-data/event-testing 경로에 추가된 경우 `@triggerBody().folderPath` 값은 `sample-data/event-testing`, `@triggerBody().fileName` 값은 `moviesDB.csv`가 됩니다. 이러한 값은 예제에서 파이프라인 매개 변수 및에 매핑되고 `sourceFolder` `sourceFile` 각각 파이프라인 전체에서 사용할 수 있습니다 `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` .

1. 마쳤으면 **마침** 을 클릭합니다.

## <a name="json-schema"></a>JSON 스키마

다음 표에서는 저장소 이벤트 트리거와 관련 된 스키마 요소에 대 한 개요를 제공 합니다.

| **JSON 요소** | **설명** | **형식** | **허용되는 값** | **필수** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **범위** | 스토리지 계정의 Azure Resource Manager 리소스 ID입니다. | String | Azure Resource Manager ID | 예 |
| **events** | 이 트리거를 발생시키는 이벤트 유형입니다. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | 예, 이러한 값의 조합입니다. |
| **blobPathBeginsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 시작해야 합니다. 예를 들어 `/records/blobs/december/`는 `records` 컨테이너 아래의 `december` 폴더에서 Blob에 대한 트리거만을 시작합니다. | String   | | 또는의 속성 중 하나 이상에 대 한 값을 `blobPathBeginsWith` 제공 `blobPathEndsWith` 합니다. |
| **blobPathEndsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 끝나야 합니다. 예를 들어 `december/boxes.csv`는 `december` 폴더에서 `boxes`라는 이름의 Blob에 대한 트리거만을 시작합니다. | String   | | 이러한 속성 중 하나 이상에 대한 값을 제공해야 합니다. `blobPathBeginsWith` 또는 `blobPathEndsWith` |
| **ignoreEmptyBlobs** | 0 바이트 BLOB이 파이프라인 실행을 트리거하는지 여부입니다. 기본적으로 True로 설정됩니다. | 부울 | true 또는 false | 예 |

## <a name="examples-of-storage-event-triggers"></a>저장소 이벤트 트리거의 예

이 섹션에서는 저장소 이벤트 트리거 설정의 예를 제공 합니다.

> [!IMPORTANT]
> 다음 예제에 표시된 대로 컨테이너 및 폴더, 컨테이너 및 파일, 컨테이너, 폴더 및 파일을 지정할 때마다 경로의 `/blobs/` 세그먼트를 포함해야 합니다. **blobPathBeginsWith** 의 경우 Data Factory UI는 트리거 JSON에서 폴더와 컨테이너 이름 사이에 `/blobs/`를 자동으로 추가합니다.

| 속성 | 예제 | Description |
|---|---|---|
| **다음으로 Blob 경로 시작** | `/containername/` | 컨테이너에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/` | `containername` 컨테이너 및 `foldername` 폴더에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/subfoldername/` | 하위 폴더를 참조할 수도 있습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/file.txt` | `containername` 컨테이너 아래의 `foldername` 폴더에서 `file.txt`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `file.txt` | 모든 경로에서 `file.txt`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `/containername/blobs/file.txt` | `containername` 컨테이너 아래에서 `file.txt`라는 이름의 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `foldername/file.txt` | 모든 컨테이너 아래의 `foldername` 폴더에서 `file.txt`라는 이름의 Blob에 대한 이벤트를 받습니다. |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Data Factory는 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 blob 이벤트에 연결 된 수신 대기, 업데이트 구독 및 트리거 파이프라인에 대 한 무단 액세스를 엄격히 금지 합니다.

* 새 저장소 이벤트 트리거를 성공적으로 만들거나 기존 저장소 이벤트 트리거를 업데이트 하려면 Data Factory에 로그인 한 Azure 계정에 관련 저장소 계정에 대 한 적절 한 액세스 권한이 있어야 합니다. 그렇지 않으면 작업이 실패 하 고 _액세스가 거부_ 됩니다.
* Data Factory에는 Event Grid에 대 한 특별 한 권한이 필요 하지 않으며, 작업에 대 한 Data Factory 서비스 사용자에 게 특수 RBAC 권한을 할당할 필요가 _없습니다_ .

다음 RBAC 설정은 저장소 이벤트 트리거에 대해 작동 합니다.

* 저장소 계정에 대 한 소유자 역할
* 저장소 계정에 대 한 참가자 역할
* _Microsoft EventGrid/Eventgrid/_ storage 계정에 대 한 쓰기 권한 _/subscriptions/# # # #/Ssourceg/# #_ # #/providers/Microsoft.Storage/storageAccounts/storageAccountName

Azure Data Factory를 제공 하는 방법을 이해 하기 위해 단계를 다시 수행 하 고 장면을 살펴보세요. Data Factory, 저장소 및 Event Grid 간 통합을 위한 개략적인 작업 흐름은 다음과 같습니다.

### <a name="create-a-new-storage-event-trigger"></a>새 저장소 이벤트 트리거 만들기

이 개략적인 워크플로는 Azure Data Factory Event Grid와 상호 작용 하 여 저장소 이벤트 트리거를 만드는 방법에 대해 설명 합니다.

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="저장소 이벤트 트리거 만들기 워크플로":::

작업 흐름에서 두 가지 눈에 띄는 호출:

* Azure Data Factory은 저장소 계정에 대 한 직접적인 접촉을 _하지 않습니다_ . 구독을 만드는 요청은 대신 Event Grid에 의해 릴레이 되 고 처리 됩니다. 따라서이 단계에서는 Data Factory에 저장소 계정에 대 한 권한이 필요 하지 않습니다.

* 액세스 제어 및 권한 확인은 Azure Data Factory 쪽에서 수행 됩니다. ADF가 저장소 이벤트 구독 요청을 보내기 전에 사용자에 대 한 사용 권한을 확인 합니다. 구체적으로 말하면, Azure 계정에 로그인 하 여 저장소 이벤트 트리거를 만드는 경우 관련 저장소 계정에 대 한 적절 한 액세스 권한이 있는지 확인 합니다. 권한 검사에 실패 하는 경우 트리거 생성도 실패 합니다.

### <a name="storage-event-trigger-data-factory-pipeline-run"></a>파이프라인 실행 Data Factory 저장소 이벤트 트리거

이 개략적인 작업 흐름은 저장소 이벤트에서 파이프라인을 실행 하는 방법을 설명 Event Grid

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="파이프라인 실행을 트리거하는 저장소 이벤트의 워크플로입니다.":::

Data Factory에서 이벤트를 트리거하는 파이프라인에 도달 하면 워크플로에서 다음과 같은 세 가지 주목할 만한 호출이 발생 합니다.

* Event Grid는 저장소에서 시스템에 메시지를 놓을 때 가능한 한 빨리 메시지를 릴레이 하는 푸시 모델을 사용 합니다. 이는 끌어오기 시스템이 사용 되는 Kafka와 같은 메시징 시스템과 다릅니다.
* Azure Data Factory에 대 한 이벤트 트리거는 들어오는 메시지에 대 한 활성 수신기 역할을 하며 연결 된 파이프라인을 제대로 트리거합니다.
* 저장소 이벤트 트리거 자체는 저장소 계정에 대 한 직접 연결을 사용 하지 않습니다.

  * 즉, 저장소 계정에서 데이터를 처리 하기 위해 파이프라인 내에 복사 또는 다른 활동이 있는 경우 Data Factory는 연결 된 서비스에 저장 된 자격 증명을 사용 하 여 저장소와의 직접 연결을 만듭니다. 연결 된 서비스가 적절 하 게 설정 되었는지 확인
  * 그러나 파이프라인에서 저장소 계정에 대 한 참조를 사용 하지 않는 경우 저장소 계정에 액세스 하기 위해 Data Factory에 권한을 부여할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

* 트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#trigger-execution)를 참조하세요.
* 파이프라인에서 트리거 메타 데이터를 참조 하는 방법에 대해 알아봅니다. [파이프라인 실행의 참조 트리거 메타 데이터](how-to-use-trigger-parameterization.md) 를 참조 하세요.
