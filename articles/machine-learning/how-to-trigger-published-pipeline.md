---
title: 논리 앱에서 ML 파이프라인 실행을 트리거합니다.
titleSuffix: Azure Machine Learning
description: Azure 논리 앱을 사용하여 ML 파이프라인 실행을 트리거하는 방법을 알아봅니다.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122858"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>논리 앱에서 기계 학습 파이프라인 실행 트리거

새 데이터가 나타나면 Azure 기계 학습 파이프라인의 실행을 트리거합니다. 예를 들어 Blob 저장소 계정에 새 데이터가 나타날 때 새 모델을 학습하도록 파이프라인을 트리거할 수 있습니다. [Azure 논리 앱으로](../logic-apps/logic-apps-overview.md)트리거를 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure 기계 학습 작업 영역 만들기를](how-to-manage-workspace.md)참조하십시오.

* 게시된 기계 학습 파이프라인의 REST 끝점입니다. [파이프라인을 만들고 게시합니다.](how-to-create-your-first-pipeline.md) 그런 다음 파이프라인 ID를 사용하여 게시된 파이프라인의 REST 끝점을 찾습니다.
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* 데이터를 저장할 [Azure Blob 저장소입니다.](../storage/blobs/storage-blobs-overview.md)
* 작업 영역의 [데이터 스토어로](how-to-access-data.md) Blob 저장소 계정의 세부 정보가 포함되어 있습니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기

이제 [Azure 논리 앱](../logic-apps/logic-apps-overview.md) 인스턴스를 만듭니다. 원하는 경우 [ISE(통합 서비스 환경)를 사용하고](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 로직 앱에서 사용할 수 있는 [고객 관리 키를 설정합니다.](../logic-apps/customer-managed-keys-integration-service-environment.md)

Logic App이 프로비전되면 다음 단계를 사용하여 파이프라인에 대한 트리거를 구성합니다.

1. Azure 기계 학습 작업 영역에 대한 앱 액세스 권한을 부여하기 위해 [시스템 할당된 관리되는 ID를 만듭니다.](../logic-apps/create-managed-service-identity.md)

1. 논리 앱 디자이너 보기로 이동하여 빈 논리 앱 템플릿을 선택합니다. 
    > [!div class="mx-imgBorder"]
    > ![새 템플릿](media/how-to-trigger-published-pipeline/blank-template.png)

1. 디자이너에서 **Blob을**검색합니다. **Blob이 추가되거나 수정된 경우(속성만 해당)** 트리거를 선택하고 이 트리거를 논리 앱에 추가합니다.
    > [!div class="mx-imgBorder"]
    > ![트리거 추가](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Blob 추가 또는 수정 사항을 모니터링할 Blob 저장소 계정에 대한 연결 정보를 입력합니다. 모니터링할 컨테이너를 선택합니다. 
 
    **간격** 및 **빈도를** 선택하여 자신에게 적합한 업데이트를 폴링합니다.  

    > [!NOTE]
    > 이 트리거는 선택한 컨테이너를 모니터링하지만 하위 폴더를 모니터링하지는 않습니다.

1. 새 또는 수정된 Blob이 검색될 때 실행되는 HTTP 작업을 추가합니다. **+ 새 단계를**선택한 다음 HTTP 작업을 검색하고 선택합니다.

  > [!div class="mx-imgBorder"]
  > ![HTTP 작업 검색](media/how-to-trigger-published-pipeline/search-http.png)

  다음 설정을 사용하여 작업을 구성합니다.

  | 설정 | 값 | 
  |---|---|
  | HTTP 동작 | POST |
  | URI |[필수 구성 조건으로](#prerequisites) 찾은 게시된 파이프라인의 끝점 |
  | 인증 모드 | 관리 ID |

1. [DataPath 파이프라인 매개 변수의](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) 값을 설정 하려면 일정을 설정 합니다.

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    작업 영역에 추가한 을 [필수 구성 조건으로](#prerequisites)사용합니다. `DataStoreName`
     
    > [!div class="mx-imgBorder"]
    > ![HTTP 설정](media/how-to-trigger-published-pipeline/http-settings.png)

1. **저장을** 선택하면 일정이 준비되었습니다.