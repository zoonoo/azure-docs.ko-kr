---
title: 다른 Azure 지역으로 인스턴스 이동
titleSuffix: Azure Digital Twins
description: 한 Azure 지역에서 다른 Azure 지역으로 Azure Digital Twins 인스턴스를 이동 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 1725c3ff162e4f6b7ac3a5ea1ede6976c827b510
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328499"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Azure Digital Twins 인스턴스를 다른 Azure 지역으로 이동

Azure Digital Twins 인스턴스를 한 지역에서 다른 지역으로 이동 해야 하는 경우 현재 프로세스는 **새 지역에서 리소스를 다시 만든**다음 원래 리소스를 삭제 하는 것입니다. 이 프로세스가 끝나면 업데이트 된 위치를 제외 하 고 첫 번째와 동일한 새 Azure Digital Twins 인스턴스를 사용 하 게 됩니다.

이 문서에서는 전체 이동 작업을 수행 하는 방법에 대 한 지침을 제공 하 여 새 인스턴스가 원본과 일치 하도록 하는 데 필요한 모든 항목을 복사 합니다.

이 프로세스에는 다음 단계가 포함됩니다.
1. 준비: 원래 모델, 쌍 및 그래프를 다운로드 합니다.
2. 이동: 새 지역에서 새 Azure Digital Twins 인스턴스를 만듭니다.
3. 이동: 새 Azure Digital Twins 인스턴스를 다시 채웁니다.
    - 원래 모델, 쌍 및 그래프를 업로드 합니다.
    - 끝점과 경로를 다시 만듭니다.
    - 연결 된 리소스를 다시 연결 합니다.
4. 원본 리소스 정리: 원본 인스턴스를 삭제 합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Digital Twins 인스턴스를 다시 만들기 전에 원래 인스턴스의 구성 요소를 확인 하 고 다시 만들어야 하는 모든 부분을 명확 하 게 파악 하는 것이 좋습니다.

다음은 고려해 야 할 몇 가지 질문입니다.
* 내 인스턴스에 업로드 된 **모델** 은 무엇 인가요? 몇 개 인가요?
* 내 인스턴스의 쌍 **은 무엇** 인가요? 몇 개 인가요?
* 내 인스턴스에서 **그래프** 의 일반적인 형태는 무엇입니까? 얼마나 많은 관계가 있나요?
* 내 인스턴스에 있는 **끝점** 은 무엇 인가요?
* 내 인스턴스에 있는 **경로** 는 무엇입니까? 필터가 있나요?
* 내 인스턴스가 **다른 Azure 서비스에 연결**하는 위치 몇 가지 일반적인 통합 요소에는 다음이 포함 됩니다.
    - Event Grid, Event Hub 또는 Service Bus
    - Azure Functions
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - 장치 프로 비전 서비스 (DPS)
* 내 인스턴스에 연결 하는 다른 **개인 또는 회사 앱** 은 무엇 인가요?

[Azure Portal](https://portal.azure.com), [Azure Digital twins api 및 Sdk](how-to-use-apis-sdks.md), [azure digital Twins CLI 명령](how-to-use-cli.md)또는 [Azure digital twins (ADT) 탐색기](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 샘플을 사용 하 여이 정보를 수집할 수 있습니다.

## <a name="prepare"></a>준비

이 섹션에서는 원래 인스턴스에서 **원래 모델, 쌍 및 그래프를 다운로드** 하 여 인스턴스를 다시 만들 준비를 합니다. 이 문서에서는 [Azure Digital Twins (ADT) 탐색기](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 샘플을 사용 합니다.

>[!NOTE]
>인스턴스에 모델 및/또는 그래프가 포함 된 파일이 이미 있을 수 있습니다. 이 경우 모든 항목을 다시 다운로드할 필요가 없습니다. 누락 된 부분이 나 처음에 이러한 파일을 업로드 한 이후에 변경 되었을 수 있는 것입니다 (예: 새 데이터로 업데이트 되었을 수 있는 경우).

### <a name="limitations-of-adt-explorer"></a>ADT 탐색기의 제한 사항

[Azure Digital Twins (ADT) 탐색기 샘플](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 은 그래프의 시각적 표시를 지 원하는 클라이언트 앱 샘플 이며 인스턴스와의 시각적 상호 작용을 제공 합니다. 이 문서에서는이를 사용 하 여 다운로드 하 고 나중에 다시 업로드할 때, 모델, 쌍, 그래프를 다운로드 하는 방법을 보여 줍니다.

그러나이 샘플은 전체 도구가 아니라 **샘플** 입니다. 스트레스 테스트를 거치지 않았으며 큰 크기의 그래프를 처리 하도록 빌드되지 않았습니다. 따라서 다음과 같은 기본 샘플 제한 사항을 염두에 두어야 합니다.
* 이 샘플은 현재 그래프 크기에서 최대 1000 노드 및 2000 관계로 테스트 되었습니다.
* 이 샘플에서는 일시적인 오류가 발생 하는 경우 다시 시도 하는 것을 지원 하지 않습니다.
* 업로드 된 데이터가 완전 하지 않은 경우 샘플은 사용자에 게 알리지 않습니다.
* 샘플은 메모리와 같은 사용 가능한 리소스를 초과 하는 큰 그래프에서 발생 하는 오류를 처리 하지 않습니다.

샘플에서 그래프의 크기를 처리할 수 없는 경우 다른 Azure 디지털 쌍 개발자 도구를 사용 하 여 그래프를 내보내고 가져올 수 있습니다.
* [Azure Digital Twins CLI 명령](how-to-use-cli.md)
* [Azure Digital Twins Api 및 Sdk](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>ADT 탐색기 응용 프로그램 설정

ADT 탐색기를 계속 진행 하려면 먼저 샘플 응용 프로그램 코드를 다운로드 하 고 컴퓨터에서 실행 되도록 설정 합니다. 

[Azure Digital Twins (ADT) 탐색기](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)에서 샘플로 이동 합니다. *ZIP 다운로드* 단추를 눌러 이 샘플 코드의 *.ZIP* 파일을 _**ADT_Explorer.zip**_ 으로 컴퓨터에 다운로드합니다. 파일의 압축을 풉니다.

다음으로, 컴퓨터에서 ADT 탐색기를 실행 하는 데 필요한 권한을 설정 합니다. 이렇게 하려면 Azure Digital Twins 빠른 시작의 [*ADT 탐색기 권한 설정*](quickstart-adt-explorer.md#set-adt-explorer-permissions) 섹션에 있는 단계를 따르세요.

마지막으로, ADT 탐색기를 실행 하 고 구성 하 여 원래 Azure Digital Twins 인스턴스에 연결 합니다. 빠른 시작의 [*ADT 탐색기 실행 및 구성*](quickstart-adt-explorer.md#run-and-configure-adt-explorer) 섹션에 있는 단계를 따릅니다.

이제 ADT 탐색기 샘플 앱이 컴퓨터의 브라우저에서 실행 되 고 있어야 합니다. 이 샘플은 원래 Azure Digital Twins 인스턴스에 연결 해야 합니다.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="localhost:3000에서 실행 중인 앱을 보여 주는 브라우저 창입니다. 이 앱은 ADT 탐색기라고 하며, 쿼리 탐색기, 모델 보기, 그래프 보기 및 속성 탐색기에 대한 상자를 포함하고 있습니다. 화면 데이터는 아직 없습니다." lightbox="media/how-to-move-regions/explorer-blank.png":::

연결을 확인 하려면 *쿼리 실행* 단추를 클릭 하 여 그래프 *탐색기* 상자에서 그래프의 모든 쌍 및 관계를 표시 하는 기본 쿼리를 실행 합니다.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="창 위쪽에서 '쿼리 실행'이라는 단추가 강조 표시되어 있습니다." lightbox="media/how-to-move-regions/run-query.png":::

이 문서의 뒷부분에서 다시 사용 하는 것 처럼 ADT 탐색기를 실행 상태로 유지 하 여 대상 지역의 새 인스턴스에 이러한 항목을 다시 업로드할 수 있습니다.

### <a name="download-models-twins-and-graph"></a>모델, 쌍 및 그래프 다운로드

그런 다음 솔루션의 모델, 쌍 및 그래프를 컴퓨터에 다운로드 합니다.

이러한 모든 것을 한 번에 다운로드 하려면 먼저 *그래프 뷰* 상자에 전체 그래프가 표시 되는지 확인 `SELECT * FROM digitaltwins` 합니다. *쿼리 탐색기* 상자에서의 기본 쿼리를 다시 실행 하 여이 작업을 수행할 수 있습니다.
 
그런 다음 *그래프 뷰* 상자에서 *그래프 내보내기* 아이콘을 누릅니다.

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="그래프 뷰 상자에 아이콘이 강조 표시 됩니다. 클라우드에서 아래쪽을 가리키는 화살표를 표시 합니다." lightbox="media/how-to-move-regions/export-graph.png":::

그러면 *그래프 보기*에서 *다운로드* 링크가 사용 됩니다. 모델, 쌍 및 관계를 포함 하 여 쿼리 결과의 JSON 기반 표현을 다운로드 하려면이를 선택 합니다. 이렇게 하면 *json* 파일이 컴퓨터에 다운로드 됩니다.

>[!NOTE]
>다운로드 한 파일이 다른 파일 확장명으로 표시 되는 경우 확장을 직접 편집 하 고. i n t .를 *json*으로 변경 합니다.

## <a name="move"></a>이동

이제 대상 지역에 새 인스턴스를 만들고 원래 인스턴스의 데이터 및 구성 요소로 채워서 인스턴스의 "이동"을 완료 합니다.

### <a name="create-a-new-instance"></a>새 인스턴스 만들기

먼저 **대상 지역에 Azure Digital Twins의 새 인스턴스를 만듭니다**. 이렇게 하려면 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md)의 단계에 따라 다음 포인터를 염두에 두어야 합니다.
* 다른 리소스 그룹에 있는 **경우** 새 인스턴스와 동일한 이름을 유지할 수 있습니다. 원래 인스턴스를 포함 하는 것과 동일한 리소스 그룹을 사용 해야 하는 경우에는 새 인스턴스에 고유한 이름이 필요 합니다.
* 위치를 묻는 메시지가 표시 되 면 대상 새 지역을 입력 합니다.
* 앱 등록을 다시 만들 **필요는 없습니다** . 새 인스턴스는 이미 있는 것과 동일한 앱 등록을 다시 사용할 수 있습니다.
    - [스크립팅된](how-to-set-up-instance-scripted.md) 설치 문서를 사용 하는 경우 메시지가 표시 되 면 새 이름을 입력 하는 대신 기존 앱 등록의 세부 정보를 다시 입력할 수 있습니다.
    - 수동 [포털](how-to-set-up-instance-portal.md) 또는 [CLI](how-to-set-up-instance-cli.md) 설치 문서를 사용 하는 경우 *Azure Digital twins 인스턴스를 만들고* *사용자 액세스 권한 설정* 단계를 완료 한 후에 중지할 수 있습니다. *클라이언트 응용 프로그램에 대 한 액세스 권한을 설정 하는*과정을 계속할 필요가 없습니다.

이 작업이 완료 되 면 새 인스턴스의 **호스트 이름이** 필요 하므로 데이터를 사용 하 여 계속 설정할 수 있습니다. 설치 하는 동안이를 기록 하지 않은 경우 [다음 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) 에 따라 Azure Portal에서 지금 가져올 수 있습니다.

### <a name="repopulate-old-instance"></a>이전 인스턴스 다시 채우기

그런 다음 원래의 복사본 인 새 인스턴스를 설정 합니다.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>ADT 탐색기를 사용 하 여 원래 모델, twins 및 그래프 업로드

이 섹션에서는 모델, 쌍 및 그래프를 새 인스턴스에 다시 업로드할 수 있습니다. 원본 인스턴스에 모델, 쌍 또는 그래프가 없거나 새 인스턴스로 이동 하지 않으려는 경우에는 [다음 섹션](#recreate-endpoints-and-routes)으로 건너뛸 수 있습니다.

그렇지 않고 계속 하려면 **ADT 탐색기** 를 실행 하는 브라우저 창으로 돌아가서 아래 단계를 수행 합니다.

##### <a name="connect-to-the-new-instance"></a>새 인스턴스에 연결

현재 ADT 탐색기는 원래의 Azure Digital Twins 인스턴스에 연결 됩니다. 창 맨 위에 있는 *로그인* 단추를 눌러 새 인스턴스를 가리키도록 연결을 전환 합니다. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="창 위쪽 근처에 있는 로그인 아이콘이 강조 표시된 ADT 탐색기입니다. 이 아이콘은 열쇠의 실루엣과 겹쳐진 사람의 간단한 실루엣을 보여 줍니다." lightbox="media/how-to-move-regions/sign-in.png":::

앱 등록을 다시 사용 하 고 있으므로 *ADT URL*만 바꾸면 됩니다. 이 값을 *https://{새 인스턴스 호스트 이름}* 을 읽도록 변경 합니다.

*Connect*를 누릅니다. Azure 자격 증명을 사용 하 여 다시 로그인 하거나 인스턴스에 대해이 응용 프로그램에 대 한 동의를 부여 하 라는 메시지가 표시 될 수 있습니다.

##### <a name="upload-models-twins-and-graph"></a>모델, 쌍 및 그래프 업로드

그런 다음 이전에 다운로드 한 솔루션 구성 요소를 새 인스턴스에 업로드 합니다.

**모델, twins 및 그래프**를 업로드 하려면 그래프 *뷰* 상자에서 *그래프 가져오기* 아이콘을 누릅니다. 이 옵션은이 세 구성 요소를 한 번에 모두 업로드 합니다 (그래프에서 현재 사용 되지 않는 모델에도 해당).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="그래프 보기 상자에서 아이콘이 강조 표시되어 있습니다. 이 아이콘은 클라우드를 가리키는 화살표를 보여 줍니다." lightbox="media/how-to-move-regions/import-graph.png":::

파일 선택기 상자에서 다운로드 한 그래프로 이동 합니다. 그래프 *json* 파일을 선택 하 고 *열기*를 누릅니다.

몇 초 후 ADT 탐색기에서 로드될 그래프의 미리 보기를 표시하는 *가져오기* 보기를 엽니다.

그래프 업로드를 확인하려면 *그래프 보기*의 오른쪽 위 모서리에서 *저장* 아이콘을 누릅니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="그래프 미리 보기 창에서 저장 아이콘이 강조 표시되어 있습니다." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT 탐색기는 이제 모델 및 그래프 (쌍 및 관계 포함)를 새 Azure Digital 쌍 인스턴스에 업로드 합니다. 업로드 된 모델, 쌍 및 관계의 수를 알리는 성공 메시지가 표시 됩니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="그래프 가져오기 성공을 나타내는 대화 상자입니다. ' 가져오기 성공 '을 읽습니다. 2 개의 모델을 가져왔습니다. 4 개의 wins를 가져왔습니다. 2 개의 관계를 가져왔습니다. '" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

모든 항목이 성공적으로 업로드 되었는지 확인 하려면 *그래프 탐색기* 상자에서 *쿼리 실행* 단추를 클릭 하 여 그래프의 모든 쌍 및 관계를 표시 하는 기본 쿼리를 실행 합니다. 이렇게 하면 *모델 뷰의*모델 목록도 새로 고쳐집니다.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="창 위쪽의 이전에서 동일한 ' 쿼리 실행 ' 단추를 강조 표시 합니다." lightbox="media/how-to-move-regions/run-query.png":::

그래프 *탐색기* 상자에 모든 해당 쌍 및 관계가 표시 되는 그래프가 표시 되어야 합니다. *모델 뷰* 상자에도 모델이 표시 됩니다.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="' 모델 뷰 ' 상자에 강조 표시 된 2 개의 모델 및 ' Graph 탐색기 ' 상자에 강조 표시 된 그래프를 보여 주는 ADT 탐색기의 뷰입니다." lightbox="media/how-to-move-regions/post-upload.png":::

이렇게 하면 모델, 쌍 및 그래프가 대상 지역의 새 인스턴스에 다시 업로드 된 것을 확인할 수 있습니다.

#### <a name="recreate-endpoints-and-routes"></a>끝점 및 경로 다시 만들기

원본 인스턴스에 **끝점 및/또는 경로가** 있는 경우 새 인스턴스에서 다시 만들어야 합니다. 원본 인스턴스에 끝점 또는 경로가 없거나 새 인스턴스로 이동 하지 않으려는 경우에는 [다음 섹션](#re-link-connected-resources)으로 건너뛸 수 있습니다.

그렇지 않으면 계속 진행 하 고 방법: 새 인스턴스를 사용 하 여 [*끝점 및 경로 관리*](how-to-manage-routes-portal.md) 의 단계에 따라 이러한 포인터를 염두에 두어야 합니다. 
* 끝점에 사용 중인 Event Grid, Event Hub 또는 Service Bus 리소스 (끝점 명령의*필수 구성 요소* 섹션)를 다시 만들 **필요는 없습니다** . Azure Digital Twins 인스턴스에서 끝점을 다시 만들어야 합니다.
* 다른 인스턴스로 범위가 지정 되므로 끝점과 경로 **이름을**다시 사용할 수 있습니다.
* 만든 경로에 필요한 **필터** 를 추가 해야 합니다.

#### <a name="re-link-connected-resources"></a>연결 된 리소스 다시 연결

원래 Azure Digital Twins 인스턴스에 연결 된 다른 앱 또는 Azure 리소스가 있는 경우, 대신 새 인스턴스에 연결 되도록 연결을 편집 해야 합니다. 여기에는 다른 Azure 서비스 또는 Azure Digital Twins와 함께 작동 하도록 설정한 개인 또는 회사 앱이 포함 될 수 있습니다.

원본 인스턴스에 연결 된 다른 리소스가 없거나 새 인스턴스로 이동 하지 않으려는 경우 [다음 섹션](#verify)으로 건너뛸 수 있습니다.

그렇지 않은 경우 계속 하려면 시나리오에서 연결 된 리소스를 고려해 야 합니다. 연결 된 리소스를 삭제 하 고 다시 만들 필요는 없습니다. 대신 **호스트 이름을**통해 Azure Digital twins 인스턴스에 연결 하는 지점만 편집 하 고 원래 대신 새 인스턴스의 호스트 이름을 사용 하도록 업데이트 해야 합니다.

편집 해야 하는 정확한 리소스는 시나리오에 따라 달라 지지만 몇 가지 일반적인 통합 지점은 다음과 같습니다.
* Azure Functions. 해당 코드에 원래 인스턴스의 호스트 이름이 포함 된 Azure 함수가 있는 경우이 값을 새 인스턴스의 호스트 이름으로 업데이트 하 고 함수를 다시 게시 해야 합니다.
* Event Grid, Event Hubs 또는 Service Bus
* Logic Apps
* Time Series Insights
* Azure Maps
* 장치 프로 비전 서비스 (DPS)
* [*자습서: 클라이언트 앱 코딩*](tutorial-code.md), 인스턴스에 연결 하 고 Azure Digital Twins api를 호출 하는 클라이언트 **앱** 과 같이 azure 외부의 개인 또는 회사 앱

이 단계를 완료 한 후 대상 지역의 새 인스턴스는 원본 인스턴스의 복사본 이어야 합니다.

## <a name="verify"></a>확인

새 인스턴스가 올바르게 설정 되었는지 확인 하려면 다음 도구를 사용할 수 있습니다.
* [**Azure Portal**](https://portal.azure.com) (새 인스턴스가 있는지 확인 하 고 올바른 대상 지역에 있는지 확인 하는 데 적합 합니다. 끝점과 경로를 확인 하 고 다른 Azure 서비스에 대 한 연결을 확인 하는 데에도 적합 함)
* [Azure Digital Twins **CLI 명령** ](how-to-use-cli.md) (새 인스턴스가 존재 하 고 올바른 대상 지역에 있는지 확인 하는 데 적합 합니다. 또한 인스턴스 데이터를 확인 하는 데 사용할 수 있음)
* [**ADT 탐색기**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (모델, 쌍, 그래프 등의 인스턴스 데이터를 확인 하는 데 적합)
* [Azure Digital Twins api 및 sdk](how-to-use-apis-sdks.md) (모델, 쌍, 그래프 등의 인스턴스 데이터를 확인 하는 데 적합, 끝점과 경로를 확인 하는 데도 적합)

원본 인스턴스를 사용 하 여 실행 한 사용자 지정 앱 또는 종단 간 흐름을 실행 하 여 새 인스턴스를 올바르게 작동 하는지 확인할 수도 있습니다.

## <a name="clean-up-source-resources"></a>원본 리소스 정리

이제 새 인스턴스가 원본 인스턴스의 데이터 및 연결 복사본을 사용 하 여 대상 지역에 설정 되었으므로 **원래 인스턴스를 삭제할**수 있습니다.

[CLI](how-to-use-cli.md)또는 [제어 평면 api](how-to-use-apis-sdks.md#overview-control-plane-apis)를 사용 하 여 [Azure Portal](https://portal.azure.com)에서이 작업을 수행할 수 있습니다.

Azure Portal를 사용 하 여 인스턴스를 삭제 하려면 브라우저 창에서 [포털을 열고](https://portal.azure.com) 포털 검색 표시줄에서 해당 이름을 검색 하 여 원래 Azure Digital twins 인스턴스로 이동 합니다.

*삭제* 단추를 누르고 표시 되는 메시지에 따라 삭제를 완료 합니다.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="개요 탭의 Azure Portal에 있는 Azure 디지털 Twins 인스턴스 세부 정보를 표시 합니다. 삭제 단추가 강조 표시 됩니다.":::