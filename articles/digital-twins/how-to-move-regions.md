---
title: 인스턴스를 다른 Azure 지역으로 이동
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 인스턴스를 한 Azure 지역에서 다른 Azure 자역으로 이동하는 방법을 살펴봅니다.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e268cca87479625af023b5970bb27c56721f6d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102049851"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Azure Digital Twins 인스턴스를 다른 Azure 지역으로 이동

Azure Digital Twines 인스턴스를 한 지역에서 다른 지역으로 이동해야 하는 경우 현재 프로세스는 새 지역에서 리소스를 다시 만든 다음, 원래 리소스를 삭제하는 것입니다. 이 프로세스가 끝나면 업데이트된 위치를 제외하고 처음과 동일한 새 Azure Digital Twins 인스턴스를 사용하게 됩니다.

이 문서에서는 새 인스턴스를 원본과 일치시키는 데 필요한 모든 항목을 완전히 이동하고 복사하는 방법에 대한 지침을 제공합니다.

이 프로세스에는 다음 단계가 포함됩니다.

1. 준비: 원본 모델, 트윈 및 그래프를 다운로드합니다.
1. 이동: 새 Azure Digital Twins 인스턴스를 새 지역에 만듭니다.
1. 이동: 새 Azure Digital Twins 인스턴스를 다시 채웁니다.
    - 원래 모델, 트윈 및 그래프를 업로드합니다.
    - 엔드포인트 및 경로를 다시 만듭니다.
    - 연결된 리소스를 다시 연결합니다.
1. 원본 리소스 정리: 원본 인스턴스를 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Digital Twins 인스턴스를 다시 만들기 전에 원래 인스턴스의 구성 요소를 검토하여 다시 만들어야 하는 모든 부분을 명확히 파악합니다.

고려해야 할 질문은 다음과 같습니다.

* 내 인스턴스에 업로드된 *모델* 은 무엇인가요? 몇 개인가요?
* 내 인스턴스의 *트윈* 은 무엇인가요? 몇 개인가요?
* 내 인스턴스에서 *그래프* 의 일반적인 모양은 무엇인가요? 얼마나 많은 관계가 있나요?
* 내 인스턴스에 있는 *엔드포인트* 는 무엇인가요?
* 내 인스턴스에 있는 *경로* 는 무엇인가요? 필터가 있나요?
* 내 인스턴스가 *다른 Azure 서비스에 연결* 되는 위치는 어떻게 되나요? 몇 가지 일반적인 통합 지점은 다음과 같습니다.

    - Azure Event Grid, Azure Event Hubs 또는 Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning Service
* 내 인스턴스에 연결할 수 있는 다른 *개인 또는 회사 앱* 은 무엇인가요?

이 정보는 [Azure Portal](https://portal.azure.com), [Azure Digital Twins API 및 SDK](how-to-use-apis-sdks.md), [Azure Digital Twins CLI 명령](how-to-use-cli.md) 또는 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 샘플을 사용하여 수집할 수 있습니다.

## <a name="prepare"></a>준비

이 섹션에서는 원래 인스턴스에서 원래 모델, 트윈 및 그래프를 다운로드하여 인스턴스를 다시 만들 준비를 합니다. 이 문서에서는 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 샘플을 이 작업에 사용합니다.

>[!NOTE]
>인스턴스에 모델 또는 그래프가 포함된 파일이 이미 있을 수 있습니다. 이 경우 모든 항목을 다운로드하는 것이 아니라 누락된 항목 또는 이러한 파일을 처음 업로드한 이후 변경되었을 수 있는 항목만 다시 다운로드하면 됩니다. 예를 들어 새 데이터로 업데이트된 트윈이 있을 수 있습니다.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer의 제한 사항

[Azure Digital Twins Explorer 샘플](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)은 그래프의 시각적 표현을 지원하고, 인스턴스와의 시각적 상호 작용을 제공하는 클라이언트 앱 샘플입니다. 이 문서에서는 이를 사용하여 모델, 트윈 및 그래프를 다운로드하고 나중에 다시 업로드하는 방법을 보여 줍니다.

이 샘플은 완전한 도구가 아닙니다. 스트레스 테스트를 거치지 않았고 큰 크기의 그래프를 처리하도록 빌드되지 않았습니다. 따라서 다음과 같은 기본 제공 샘플에 대한 제한 사항을 고려해야 합니다.

* 샘플은 현재 최대 1,000개의 노드와 2,000개의 관계에 대한 그래프 크기에서만 테스트되었습니다.
* 일시적인 오류가 발생하는 경우 샘플에서 다시 시도를 지원하지 않습니다.
* 업로드된 데이터가 완전하지 않을 경우 샘플에서 사용자에게 반드시 알리지는 않습니다.
* 메모리와 같은 사용 가능한 리소스를 초과하는 매우 큰 그래프로 인해 발생하는 오류는 샘플에서 처리하지 않습니다.

샘플에서 그래프의 크기를 처리할 수 없는 경우 다른 Azure Digital Twines 개발자 도구를 사용하여 그래프를 내보내고 가져올 수 있습니다.

* [Azure Digital Twins CLI 명령](how-to-use-cli.md)
* [Azure Digital Twins API 및 SDK](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Azure Digital Twins Explorer 애플리케이션 설정

Azure Digital Twins Explorer를 진행하려면 먼저 샘플 애플리케이션 코드를 다운로드하고 컴퓨터에서 실행되도록 설정합니다.

샘플을 가져오려면 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)를 참조하세요. **ZIP 다운로드** 단추를 선택하여 이 샘플 코드의 .zip 파일을 **Azure_Digital_Twins__ADT__explorer.zip** 으로 컴퓨터에 다운로드합니다. 파일의 압축을 풉니다.

다음으로 Azure Digital Twins Explorer에 대한 권한을 설정하고 구성합니다. [Azure Digital Twins 및 Azure Digital Twins Explorer 설정](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) 섹션의 지침을 따릅니다. 이 섹션에서는 다음 단계를 안내합니다.

1. Azure Digital Twins 인스턴스를 설정합니다. 이미 인스턴스가 있으므로 이 부분을 건너뛸 수 있습니다.
1. 인스턴스에 대한 액세스를 제공할 수 있도록 로컬 Azure 자격 증명을 설정합니다.
1. Azure Digital Twins Explorer를 실행하고 인스턴스에 연결하도록 구성합니다. 이동하는 원래 Azure Digital Twins 인스턴스의 *호스트 이름* 을 사용합니다.

이제 컴퓨터의 브라우저에서 Azure Digital Twins Explorer 샘플 앱이 실행됩니다. 샘플은 원래 Azure Digital Twins 인스턴스에 연결해야 합니다.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="localhost:3000에서 실행 중인 앱을 보여 주는 브라우저 창입니다. 이 앱은 Azure Digital Twins Explorer라고 하며 쿼리 탐색기, 모델 보기, 그래프 보기 및 속성 탐색기에 대한 상자를 포함하고 있습니다. 화면 데이터는 아직 없습니다." lightbox="media/how-to-move-regions/explorer-blank.png":::

연결을 확인하려면 **쿼리 실행** 단추를 선택하여 **그래프 탐색기** 상자의 그래프에 모든 트윈 및 관계를 표시하는 기본 쿼리를 실행합니다.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="창의 오른쪽 위 모서리에서 강조 표시된 쿼리 실행이라는 단추" lightbox="media/how-to-move-regions/run-query.png":::

이 문서 뒷부분에서 다시 사용하여 이러한 항목을 대상 지역의 새 인스턴스에 업로드하므로 Azure Digital Twins Explorer를 실행된 상태로 둘 수 있습니다.

### <a name="download-models-twins-and-graph"></a>모델, 트윈 및 그래프 다운로드

다음으로, 솔루션의 모델, 트윈 및 그래프를 컴퓨터에 다운로드합니다.

이러한 모든 항목을 한 번에 다운로드하려면 먼저 **그래프 보기** 상자에 전체 그래프가 표시되는지 확인합니다. 전체 그래프가 아직 표시되지 않으면 **쿼리 탐색기** 상자에서 `SELECT * FROM digitaltwins`의 기본 쿼리를 다시 실행합니다.
 
그런 다음, **그래프 보기** 상자에서 **그래프 내보내기** 아이콘을 선택합니다.

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="그래프 보기 상자에서 강조 표시된 아이콘. 구름에서 아래쪽을 가리키는 화살표를 보여 줍니다." lightbox="media/how-to-move-regions/export-graph.png":::

이 작업은 **그래프 보기** 상자에서 **다운로드** 링크를 사용하도록 설정할 수 있습니다. 모델, 트윈 및 관계를 포함하는 쿼리 결과의 JSON 기반 표현을 다운로드하려면 이 링크를 선택합니다. 이 작업은 .json 파일을 컴퓨터에 다운로드합니다.

>[!NOTE]
>다운로드한 파일의 확장명이 다른 파일 확장명으로 표시되는 경우 확장명을 직접 편집하여 .json으로 변경하세요.

## <a name="move"></a>이동

다음으로, 새 인스턴스를 대상 지역에 만들어 인스턴스의 "이동"을 완료합니다. 그런 다음, 원래 인스턴스의 데이터 및 구성 요소로 채웁니다.

### <a name="create-a-new-instance"></a>새 인스턴스 만들기

먼저, 새 Azure Digital Twines 인스턴스를 대상 지역에 만듭니다. [방법: 인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)의 지침을 따릅니다. 다음 사항에 유의하세요.

* 다른 리소스 그룹에 있는 *경우* 새 인스턴스에 대해 동일한 이름을 유지할 수 있습니다. 원래 인스턴스가 포함된 리소스 그룹을 사용해야 하는 경우 새 인스턴스에는 고유한 이름이 필요합니다.
* 위치를 묻는 메시지가 표시되면 새 대상 지역을 입력합니다.

이 단계가 완료된 후 데이터를 사용하여 계속 설정하려면 새 인스턴스의 호스트 이름이 필요합니다. 설치 중에 호스트 이름을 적어 두지 않은 경우 지금 [이러한 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)에 따라 Azure Portal에서 가져옵니다.

### <a name="repopulate-the-old-instance"></a>이전 인스턴스 다시 채우기

다음으로, 원래의 복사본이 되도록 새 인스턴스를 설정합니다.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer를 사용하여 원본 모델, 트윈 및 그래프 업로드

이 섹션에서는 모델, 트윈 및 그래프를 새 인스턴스에 다시 업로드할 수 있습니다. 모델, 트윈 또는 그래프가 원래 인스턴스에 없거나 새 인스턴스로 이동하지 않으려면 [다음 섹션](#re-create-endpoints-and-routes)으로 건너뛸 수 있습니다.

그렇지 않으면 Azure Digital Twins Explorer를 실행하는 브라우저 창으로 돌아가서 다음 단계를 수행합니다.

##### <a name="connect-to-the-new-instance"></a>새 인스턴스에 연결

현재 Azure Digital Twins Explorer는 원래 Azure Digital Twins 인스턴스에 연결되어 있습니다. 창의 오른쪽 위 모서리에서 **로그인** 단추를 선택하여 새 인스턴스를 가리키도록 연결을 전환합니다.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure Digital Twins Explorer 창의 오른쪽 위 모서리에서 강조 표시된 로그인 아이콘. 열쇠 실루엣과 겹쳐진 사람에 대한 간단한 실루엣을 보여 줍니다." lightbox="media/how-to-move-regions/sign-in.png":::

새 인스턴스를 반영하도록 **ADT URL** 을 바꿉니다. *https://{새 인스턴스 호스트 이름}* 이 되도록 이 값을 변경합니다.

**연결** 을 선택합니다. Azure 자격 증명을 사용하여 다시 로그인하거나 인스턴스에 대한 이 애플리케이션의 동의를 부여하라는 메시지가 표시될 수 있습니다.

##### <a name="upload-models-twins-and-graph"></a>모델, 트윈 및 그래프 업로드

다음으로, 이전에 다운로드한 솔루션 구성 요소를 새 인스턴스에 업로드합니다.

모델, 트윈 및 그래프를 업로드하려면 **그래프 보기** 상자에서 **그래프 가져오기** 아이콘을 선택합니다. 이 옵션은 이러한 세 가지 구성 요소를 모두 한 번에 업로드합니다. 그래프에서 현재 사용되지 않는 모델도 업로드합니다.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="그래프 보기 상자에서 아이콘이 강조 표시되어 있습니다. 이 아이콘은 클라우드를 가리키는 화살표를 보여 줍니다." lightbox="media/how-to-move-regions/import-graph.png":::

파일 선택기 상자에서 다운로드한 그래프로 이동합니다. 그래프 **.json** 파일, **열기** 를 차례로 선택합니다.

몇 초 후 Azure Digital Twins Explorer에서 **가져오기** 보기가 열리고 로드할 그래프의 미리 보기가 표시됩니다.

그래프 업로드를 확인하려면 **그래프 보기** 상자의 오른쪽 위 모서리에서 **저장** 아이콘을 선택합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="[그래프 미리 보기] 창에서 [저장] 아이콘이 강조 표시되어 있습니다." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

이제 Azure Digital Twins Explorer에서 모델 및 그래프(트윈 및 관계 포함)를 새 Azure Digital Twins 인스턴스에 업로드합니다. 업로드된 모델, 트윈 및 관계의 수를 알리는 성공 메시지가 표시됩니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="그래프 가져오기가 성공했음을 나타내는 대화 상자. '가져오기를 마쳤습니다. 2개의 모델을 가져왔습니다. 4개의 트윈을 가져왔습니다. 2개의 관계를 가져왔습니다.'라고 읽을 수 있습니다." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

모든 항목이 성공적으로 업로드되었는지 확인하려면 **그래프 탐색기** 상자에서 **쿼리 실행** 단추를 선택하여 모든 쌍과 관계를 그래프에 표시하는 기본 쿼리를 실행합니다. 이 작업은 **모델 보기** 상자의 모델 목록도 새로 고칩니다.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="창의 오른쪽 위 모서리에서 주위가 강조 표시된 쿼리 실행 단추" lightbox="media/how-to-move-regions/run-query.png":::

**그래프 탐색기** 상자의 모든 트윈과 관계를 보여 주는 그래프가 표시됩니다. 모델도 **모델 보기** 상자에 나열됩니다.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="모델 보기 상자에서 강조 표시된 두 가지 모델과 그래프 탐색기 상자에서 강조 표시된 그래프를 보여 주는 Azure Digital Twins Explorer의 보기" lightbox="media/how-to-move-regions/post-upload.png":::

이러한 보기는 모델, 트윈 및 그래프가 대상 지역의 새 인스턴스로 다시 업로드되었음을 확인합니다.

#### <a name="re-create-endpoints-and-routes"></a>엔드포인트 및 경로 다시 만들기

원래 인스턴스의 엔드포인트 또는 경로가 있는 경우 새 인스턴스에서 이를 다시 만들어야 합니다. 원래 인스턴스의 엔드포인트 또는 경로가 없거나 새 인스턴스로 이동하지 않으려면 [다음 섹션](#relink-connected-resources)으로 건너뛸 수 있습니다.

그렇지 않으면 새 인스턴스를 사용하여 [방법: 엔드포인트 및 경로 관리](how-to-manage-routes-portal.md)의 단계를 따릅니다. 다음 사항에 유의하세요.

* 엔드포인트에 사용하는 Event Grid, Event Hubs 또는 Service Bus 리소스를 다시 만들 필요가 *없습니다*. 자세한 내용은 엔드포인트 지침의 "필수 구성 요소" 섹션을 참조하세요. Azure Digital Twins 인스턴스에서 엔드포인트를 다시 만들기만 하면 됩니다.
* 범위가 다른 인스턴스로 지정되므로 엔드포인트 및 경로 이름은 다시 사용할 수 있습니다.
* 필요한 필터를 만든 경로에 추가해야 합니다.

#### <a name="relink-connected-resources"></a>연결된 리소스 다시 연결

원래 Azure Digital Twins 인스턴스에 연결된 다른 앱 또는 Azure 리소스가 있는 경우 새 인스턴스에 대신 연결되도록 연결을 편집해야 합니다. 이러한 리소스에는 Azure Digital Twins에서 작동하도록 설정한 다른 Azure 서비스 또는 개인/회사 앱이 포함될 수 있습니다.

원래 인스턴스의 연결된 다른 리소스가 없거나 새 인스턴스로 이동하지 않으려면 [다음 섹션](#verify)으로 건너뛸 수 있습니다.

그렇지 않으면 시나리오에서 연결된 리소스를 고려합니다. 연결된 리소스를 삭제하고 다시 만들 필요가 없습니다. 대신 호스트 이름을 통해 Azure Digital Twins 인스턴스에 연결되는 지점을 편집하기만 하면 됩니다. 그런 다음, 원래 인스턴스 대신 새 인스턴스의 호스트 이름을 사용하도록 이러한 지점을 업데이트합니다.

편집해야 하는 정확한 리소스는 시나리오에 따라 다르지만, 몇 가지 일반적인 통합 지점은 다음과 같습니다.

* Azure Functions. 원래 인스턴스의 호스트 이름이 포함된 코드의 Azure 함수가 있는 경우 이 값을 새 인스턴스의 호스트 이름으로 업데이트하고 함수를 다시 게시해야 합니다.
* Event Grid, Event Hubs 또는 Service Bus
* Logic Apps입니다.
* Time Series Insights
* Azure Maps
* IoT Hub Device Provisioning Service
* 인스턴스에 연결하고 Azure Digital Twins API를 호출하는 Azure 외부의 개인 또는 회사 앱(예: [자습서: 클라이언트 앱 코딩](tutorial-code.md)에서 만든 클라이언트 앱)
* Azure AD 앱 등록을 다시 만들 필요가 *없습니다*. [앱 등록](how-to-create-app-registration.md)을 사용하여 Azure Digital Twins API에 연결하는 경우 동일한 앱 등록을 새 인스턴스에 다시 사용할 수 있습니다.

이 단계가 완료되면 대상 지역의 새 인스턴스가 원래 인스턴스의 복사본이 됩니다.

## <a name="verify"></a>확인

새 인스턴스가 올바르게 설정되었는지 확인하려면 다음 도구를 사용합니다.

* [Azure Portal](https://portal.azure.com). 포털은 새 인스턴스가 있고 올바른 대상 지역에 있는지 확인하는 데 유용합니다. 또한 엔드포인트, 경로 및 다른 Azure 서비스에 대한 연결을 확인하는 데도 유용합니다.
* [Azure Digital Twins CLI 명령](how-to-use-cli.md). 이러한 명령은 새 인스턴스가 있고 올바른 대상 지역에 있는지 확인하는 데 유용합니다. 또한 인스턴스 데이터를 확인하는 데 사용할 수도 있습니다.
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digital Twines Explorer는 모델, 트윈 및 그래프와 같은 인스턴스 데이터를 확인하는 데 유용합니다.
* [Azure Digital Twins API 및 SDK](how-to-use-apis-sdks.md). 이러한 리소스는 모델, 트윈 및 그래프와 같은 인스턴스 데이터를 확인하는 데 유용합니다. 또한 엔드포인트 및 경로를 확인하는 데도 유용합니다.

또한 원래 인스턴스에서 실행했던 사용자 지정 앱 또는 엔드투엔드 흐름을 실행하여 새 인스턴스에서 제대로 작동하는지 확인할 수 있습니다.

## <a name="clean-up-source-resources"></a>원본 리소스 정리

이제 새 인스턴스가 원래 인스턴스의 데이터 및 연결 복사본을 사용하여 대상 지역에 설정되었으므로 원래 인스턴스를 삭제할 수 있습니다.

[Azure Portal](https://portal.azure.com), [Azure CLI](how-to-use-cli.md) 또는 [컨트롤 플레인 API](how-to-use-apis-sdks.md#overview-control-plane-apis)를 사용할 수 있습니다.

Azure Portal을 사용하여 인스턴스를 삭제하려면 브라우저 창에서 [포털을 열고](https://portal.azure.com), 포털 검색 창에서 이름을 검색하여 원래 Azure Digital Twins 인스턴스로 이동합니다.

**삭제** 단추를 선택하고, 화면의 지시에 따라 삭제를 완료합니다.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Azure Portal의 개요 탭에 있는 Azure Digital Twines 인스턴스 세부 정보의 보기. 삭제 단추가 강조 표시되어 있습니다.":::