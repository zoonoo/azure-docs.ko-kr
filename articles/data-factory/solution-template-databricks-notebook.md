---
title: Azure Data Factory의 Databricks를 사용하여 데이터 변환 | Microsoft Docs
description: 솔루션 템플릿을 사용하여 Azure Data Factory의 Databricks 노트북을 통해 데이터를 변환하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 562ce675acc43002ce468d60f8a8c412410be86c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60395404"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Azure Data Factory의 Databricks를 사용하여 데이터 변환

이 자습서에서는 Data Factory에 **조회**, **복사** 및 **Databricks 노트북** 작업이 포함된 엔드투엔드 파이프라인을 만듭니다.

-   **조회** 또는 GetMetadata 작업은 복사 및 분석 작업을 트리거하기 전에 원본 데이터 세트가 다운스트림 사용에 준비되었는지 확인하는 데 사용됩니다.

-   **복사 작업**은 원본 파일/데이터 세트를 싱크 스토리지에 복사합니다. Spark에서 데이터 세트를 직접 사용할 수 있도록 싱크 스토리지가 Databricks 노트북에 DBFS로 탑재됩니다.

-   **Databricks 노트북 작업**은 데이터 세트를 변환하고 처리된 폴더/SQL DW에 추가하는 Databricks 노트북을 트리거합니다.

이 템플릿을 단순하게 유지하기 위해 템플릿은 예약된 트리거를 만들지 않습니다. 필요한 경우 추가할 수 있습니다.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>필수 조건

1.  **싱크**로 사용할, `sinkdata`라는 컨테이너 및 **Blob Storage 계정**을 만듭니다. 나중에 템플릿에서 참조되므로 **스토리지 계정 이름**, **컨테이너 이름** 및 **액세스 키**를 적어 둡니다.

2.  **Azure Databricks 작업 영역**이 있는지 확인하거나, 새 작업 영역을 만듭니다.

1.  **ETL에 사용할 노트북을 가져옵니다**. 아래 변환 노트북을 Databricks 작업 영역으로 가져옵니다. 아래와 동일한 위치에 있을 필요는 없지만 나중을 위해 선택한 경로를 기억해 두세요. URL 필드에 이 URL(`https://adflabstaging1.blob.core.windows.net/share/Transformations.html`)을 입력하여 URL에서 노트북을 가져옵니다. **가져오기**를 선택합니다.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  이제 **스토리지 연결 정보**(이름 및 액세스 키)로 **변환** 노트북을 업데이트하겠습니다. 위의 가져온 노트북에서 **명령 5**로 이동한 다음, 강조 표시된 값을 바꾼 후 아래 코드 조각으로 대체합니다. 이 계정이 앞에서 만든 것과 동일한 스토리지 계정이고 `sinkdata` 컨테이너를 포함하는지 확인합니다.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1.  Databricks에 액세스할 Data Factory에 대해 **Databricks 액세스 토큰**을 생성합니다. 나중에 Databricks에 연결된 서비스를 만들 때 사용하기 위해 **액세스 토큰을 저장**합니다. 토큰은 ‘dapi32db32cbb4w6eee18b7d87e45exxxxxx’와 유사합니다.

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>연결된 서비스 및 데이터 세트를 만듭니다.

1.  Data Factory UI에서 ‘연결에 연결된 서비스 + 새로 만들기’로 이동하여 **연결된 서비스**를 새로 만듭니다.

    1.  **원본** - 원본 데이터에 액세스하는 데 사용합니다. 이 샘플의 소스 파일을 포함하는 공용 Blob Storage를 사용할 수 있습니다.

        **Blob Storage**를 선택하고 아래 **SAS URI**를 사용하여 원본 스토리지에 연결합니다(읽기 전용 액세스).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **싱크** - 데이터를 복사하는 데 사용합니다.

        싱크 연결된 서비스의 필수 조건 1에서 만든 스토리지를 선택합니다.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** - Databricks 클러스터에 연결하는 데 사용합니다.

        필수 조건 2.c에서 생성된 액세스 키를 사용하여 Databricks에 연결된 서비스를 만듭니다. *대화형 클러스터*가 있는 경우 해당 클러스터를 선택할 수 있습니다. 이 예제에서는 *새 작업 클러스터* 옵션을 사용합니다.

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  **데이터 세트**를 만듭니다.

    1.  **‘sourceAvailability_Dataset’** 를 만들어 원본 데이터를 사용할 수 있는지 확인합니다.

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **원본 데이터 세트** - 원본 데이터를 복사하는 데 사용합니다(이진 복사 사용).

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **싱크 데이터 세트** - 싱크/대상 위치에 복사하는 데 사용합니다.

        1.  연결된 서비스 - 1.b에서 만든 ‘sinkBlob_LS’를 선택합니다.

        2.  파일 경로 - ‘sinkdata/staged_sink’

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>작업을 만듭니다.

1.  원본 가용성 확인을 수행하기 위한 조회 작업 ‘**가용성 플래그**’를 만듭니다(Lookup 또는 GetMetadata를 사용할 수 있음). 2.a에서 만든 ‘sourceAvailability_Dataset’를 선택합니다.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  원본에서 싱크로 데이터 세트를 복사하기 위한 복사 작업 ‘**file-to-blob**’을 만듭니다. 이 경우 데이터는 이진 파일입니다. 복사 작업의 원본 및 싱크 구성에 대해서는 아래 스크린샷을 참조하세요.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  **파이프라인 매개 변수**를 정의합니다.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  **Databricks 작업**을 만듭니다.

    이전 단계에서 만든 연결된 서비스를 선택합니다.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    **설정**을 구성합니다. 스크린샷에 표시된 대로 **기본 매개 변수**를 만든 다음, Data Factory에서 Databricks 노트북으로 전달할 매개 변수를 만듭니다. **필수 조건 2**에서 업로드된 **올바른 노트북 경로**를 찾아 **선택**합니다.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **파이프라인을 실행**합니다. 자세한 Spark 로그를 보려면 Databricks 로그 링크를 확인할 수 있습니다.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    스토리지 탐색기를 사용하여 데이터 파일을 확인할 수도 있습니다. Data Factory 파이프라인 실행과 상호 연결되기 위해 이 예제에서는 데이터 팩터리의 파이프라인 실행 ID를 출력 폴더에 추가합니다. 이렇게 하면 각 실행을 통해 생성된 파일을 다시 추적할 수 있습니다.

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
