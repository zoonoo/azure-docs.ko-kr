---
title: Azure blob storage에 연결-Azure Logic Apps
description: Azure Logic Apps를 사용하여 Azure Storage에서 Blob 만들기 및 관리
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: d57ea1a881980203b1c8f216239b27b64f0d71cd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051046"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Azure Blob Storage에서 Blob 만들기 및 관리

이 문서에서는 Azure Blob Storage 커넥터를 사용하여 논리 앱 내에서 Azure 스토리지 계정의 Blob으로 저장된 파일에 액세스하고 관리하는 방법을 보여줍니다. 이러한 방식으로 파일을 관리하는 작업 및 워크플로를 자동화하는 논리 앱을 만들 수 있습니다. 예를 들어 스토리지 계정에서 파일을 만들고, 가져오고, 업데이트하고, 삭제하는 논리 앱을 빌드할 수 있습니다.

Azure 웹 사이트에서 업데이트되는 도구가 있다고 가정해 보겠습니다. 이 도구는 논리 앱에 대한 트리거의 역할을 합니다. 이 이벤트가 발생하면 논리 앱에서 Blob Storage 컨테이너의 일부 파일을 업데이트하도록 할 수 있습니다. 이는 논리 앱의 작업입니다.

> [!NOTE]
> Logic Apps는 방화벽을 통해 Azure Storage 계정에 직접 연결하도록 지원하지 않습니다. 이러한 스토리지 계정에 액세스하려면 다음 옵션 중 하나를 사용합니다.
>
> * [통합 서비스 환경](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 만듭니다. Azure Virtual Network의 리소스에 연결할 수 있습니다.
>
> * API Management를 이미 사용 중인 경우 이 시나리오에 이 서비스를 사용할 수 있습니다. 자세한 내용은 [간단한 엔터프라이즈 통합 아키텍처](https://aka.ms/aisarch)를 참조하세요.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요. 커넥터 관련 기술 정보는 [Azure Blob Storage 커넥터 참조](/connectors/azureblobconnector/)를 참조하세요.

## <a name="limits"></a>제한

* 기본적으로 Azure Blob Storage 작업은 *50 MB 미만의*파일을 읽거나 쓸 수 있습니다. 50 MB 보다 큰 파일을 처리 하기 위해 최대 1024 MB까지 Azure Blob Storage 작업은 [메시지 청크](../logic-apps/logic-apps-handle-large-messages.md)를 지원 합니다. **Blob 콘텐츠 가져오기** 작업은 청크를 암시적으로 사용 합니다.

* Azure Blob Storage 트리거는 청크를 지원 하지 않습니다. 파일 콘텐츠를 요청 하는 경우 트리거는 50 MB 미만의 파일만 선택 합니다. 50MB보다 큰 파일을 가져오려면 다음 패턴을 따릅니다.

  * **Blob이 추가 되거나 수정**되는 경우와 같이 파일 속성을 반환 하는 Azure Blob Storage 트리거를 사용 합니다 (속성만).

  * 전체 파일을 읽고 청크를 암시적으로 사용 하는 Azure Blob Storage **Blob 콘텐츠 가져오기** 작업을 사용 하 여 트리거를 수행 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [Azure Storage 계정 및 스토리지 컨테이너](../storage/blobs/storage-quickstart-blobs-portal.md)

* Azure Blob Storage 계정에 액세스해야 하는 논리 앱 Azure Blob Storage 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다.

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Blob Storage 트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 실행될 때마다 Logic Apps 엔진에서 논리 앱 인스턴스를 만들고 앱의 워크플로를 실행하기 시작합니다.

이 예제에서는 blob의 속성이 저장소 컨테이너에서 추가 되거나 업데이트 될 때 **blob이 추가 되거나 수정 될 때 (속성만)** 트리거를 사용 하 여 논리 앱 워크플로를 시작 하는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com) 또는 Visual Studio에서 논리 앱 디자이너를 여는 빈 논리 앱을 만듭니다. 이 예에서는 Azure Portal을 사용합니다.

2. 검색 상자에서 "azure blob"을 필터로 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다.

   이 예에서는 다음 트리거를 사용 합니다. **Blob이 추가 되거나 수정 된 경우 (속성만)**

   ![트리거 선택](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. 연결 세부 정보를 묻는 메시지가 표시되면 [이제 Blob Storage 연결을 만듭니다](#create-connection). 또는 연결이 이미 존재하는 경우 트리거에 대한 필요한 정보를 제공합니다.

   이 예에서는 모니터링하려는 컨테이너 및 폴더를 선택합니다.

   1. **컨테이너** 상자에서 폴더 아이콘을 선택합니다.

   2. 폴더 목록에서 오른쪽 괄호( **>** )를 선택한 다음, 원하는 폴더를 찾고 선택할 때까지 찾습니다.

      ![폴더 선택](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. 트리거에서 변경 내용에 대한 폴더를 확인하려는 간격 및 빈도를 선택합니다.

4. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다.

5. 이제 트리거 결과와 함께 수행하려는 작업에 대한 논리 앱에 하나 이상의 작업을 계속해서 추가합니다.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Blob Storage 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. 이 예제의 경우 논리 앱은 [되풀이 트리거](../connectors/connectors-native-recurrence.md)로 시작합니다.

1. [Azure Portal](https://portal.azure.com) 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 이 예에서는 Azure Portal을 사용합니다.

2. 논리 앱 디자이너의 트리거 또는 작업에서 **새 단계**를 선택 합니다.

   ![작업 추가](./media/connectors-create-api-azureblobstorage/add-action.png) 

   기존 단계 간에 작업을 추가하려면 연결 화살표 위로 마우스를 이동합니다. 표시 되는 더하기 기호 **+** ()를 선택 하 고 **작업 추가**를 선택 합니다.

3. 검색 상자에서 "azure blob"을 필터로 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

   이 예제에서는 다음 작업을 수행 합니다. **Blob 콘텐츠 가져오기**

   ![작업 선택](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. 연결 세부 정보를 묻는 메시지가 표시되면 [이제 Azure Blob Storage 연결을 만듭니다](#create-connection).
또는 연결이 이미 존재하는 경우 작업에 대한 필요한 정보를 제공합니다.

   이 예제의 경우 원하는 파일을 선택합니다.

   1. **Blob** 상자에서 폴더 아이콘을 선택합니다.
  
      ![폴더 선택](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Blob의 **Id** 번호에 따라 원하는 파일을 찾고 선택합니다. 앞에서 설명한 Blob Storage 트리거에 의해 반환되는 Blob의 메타데이터에서 이 **Id** 번호를 찾을 수 있습니다.

5. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다.
논리 앱을 테스트하려면 선택한 폴더에 Blob이 포함되어 있는지 확인합니다.

이 예제에서는 Blob에 대한 콘텐츠만을 가져옵니다. 콘텐츠를 보려면 다른 커넥터를 사용하여 Blob으로 파일을 만드는 다른 작업을 추가합니다. 예를 들어 Blob 콘텐츠에 따라 파일을 만드는 OneDrive 작업을 추가합니다.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>스토리지 계정에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Open API (이전의 Swagger) 파일에 설명 된 대로 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](/connectors/azureblobconnector/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
