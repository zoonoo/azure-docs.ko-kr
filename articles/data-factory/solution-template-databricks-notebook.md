---
title: Azure Databricks 변환
description: 솔루션 템플릿을 사용하여 Azure Data Factory의 Databricks 노트북을 통해 데이터를 변환하는 방법을 알아봅니다.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: e771bc152ab50f907a8f2ad384e887c00d3f627a
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933946"
---
# <a name="transformation-with-azure-databricks"></a>Azure Databricks 변환

이 자습서에서는 Data Factory의 **유효성 검사**, **복사**및 **노트북** 작업을 포함 하는 종단 간 파이프라인을 만듭니다.

-   **유효성 검사** 작업은 복사 및 분석 작업을 트리거하기 전에 원본 데이터 집합이 다운스트림 사용을 위해 준비 되었는지 확인 하는 데 사용 됩니다.

-   **복사** 작업은 원본 파일/데이터 집합을 싱크 저장소에 복사 합니다. Spark에서 데이터 세트를 직접 사용할 수 있도록 싱크 스토리지가 Databricks 노트북에 DBFS로 탑재됩니다.

-   **Databricks 노트북** 활동은 데이터 집합을 변환 하는 Databricks 노트북을 트리거하고 처리 된 폴더/s q m DW에 추가 합니다.

이 템플릿을 단순하게 유지하기 위해 템플릿은 예약된 트리거를 만들지 않습니다. 필요한 경우 추가할 수 있습니다.

![1](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>사전 요구 사항

1. **싱크**로 사용할, `sinkdata`라는 컨테이너 및 **Blob Storage 계정**을 만듭니다. 나중에 템플릿에서 참조되므로 **스토리지 계정 이름**, **컨테이너 이름** 및 **액세스 키**를 적어 둡니다.

2. **Azure Databricks 작업 영역**이 있는지 확인하거나, 새 작업 영역을 만듭니다.

3. **변환을 위해 노트북을 가져옵니다**. 
    1. Azure Databricks에서 Databricks 작업 영역으로 **변환** 노트북을 가져오는 다음 스크린샷을 참조 하세요. 이는 아래와 동일한 위치에 있을 필요는 없지만 나중에 선택 하는 경로는 명심 하십시오.
   
       ![2](media/solution-template-Databricks-notebook/import-notebook.png)    
    
    1. "다음에서 가져오기: **url**"을 선택 하 고 텍스트 상자에 다음 URL을 입력 합니다.
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/import-from-url.png)    

4. 이제 저장소 연결 정보를 사용 하 여 **변환** 노트북을 업데이트 해 보겠습니다. 위의 가져온 노트북에서 **명령 5** (아래 코드 조각에 표시 된 것 처럼)로 이동 하 고 `<storage name>`및 `<access key>`을 사용자의 저장소 연결 정보로 바꿉니다. 이 계정이 앞에서 만든 것과 동일한 스토리지 계정이고 `sinkdata` 컨테이너를 포함하는지 확인합니다.

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

5.  Databricks에 액세스할 Data Factory에 대해 **Databricks 액세스 토큰**을 생성합니다. 나중에 Databricks 연결 된 서비스를 만드는 데 사용 하기 위해 **액세스 토큰을 저장** 합니다 .이 서비스는 ' dapi32db32cbb4w6eee18b7d87e45exxxxxx ' 처럼 보입니다.

    ![4](media/solution-template-Databricks-notebook/user-setting.png)

    ![5](media/solution-template-Databricks-notebook/generate-new-token.png)

## <a name="how-to-use-this-template"></a>이 템플릿을 사용 하는 방법

1.  Azure Databricks 템플릿으로 **변환** 으로 이동 합니다. 다음 연결에 대 한 연결 된 서비스를 새로 만듭니다. 
    
    ![연결 설정](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  원본 **Blob 연결** -원본 데이터에 액세스 합니다. 
        
        이 샘플의 소스 파일을 포함하는 공용 Blob Storage를 사용할 수 있습니다. 구성에 대 한 다음 스크린샷을 참조 하세요. 아래 **SAS URL** 을 사용 하 여 원본 저장소 (읽기 전용 액세스)에 연결 합니다. 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/source-blob-connection.png)

    1.  **대상 Blob 연결** – 데이터를로 복사 합니다. 
        
        싱크 연결 된 서비스에서 **필수 구성 요소** 1에서 만든 저장소를 선택 합니다.

        ![7](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    1.  **Azure Databricks** – Databricks 클러스터에 연결 하는 데 사용할 수 있습니다.

        **필수 구성 요소인** Databricks에서 생성 된 액세스 키를 사용 하 여 연결 된 서비스를 만듭니다. *대화형 클러스터*가 있는 경우 해당 클러스터를 선택할 수 있습니다. 이 예제에서는 *새 작업 클러스터* 옵션을 사용합니다.

        ![8](media/solution-template-Databricks-notebook/databricks-connection.png)

1. **이 템플릿 사용**을 선택 하면 아래와 같이 생성 된 파이프라인이 표시 됩니다.
    
    ![파이프라인 만들기](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>파이프라인 소개 및 구성

새로 만든 파이프라인에서 대부분의 설정은 기본값을 사용 하 여 자동으로 구성 됩니다. 구성을 확인 하 고 필요한 경우 사용자의 설정에 맞게 업데이트 합니다. 자세한 내용은 아래 지침 및 스크린샷을 참조 하세요.

1.  원본 가용성 검사를 수행 하기 위해 유효성 검사 작업 **가용성 플래그가** 생성 됩니다. 이전 단계에서 만든 *SourceAvailabilityDataset* 는 데이터 집합으로 선택 됩니다.

    ![12](media/solution-template-Databricks-notebook/validation-settings.png)

1.  원본에서 싱크로 데이터 집합을 복사 하기 위해 복사 작업 **파일-blob** 이 만들어집니다. 복사 작업의 원본 및 싱크 구성에 대해서는 아래 스크린샷을 참조하세요.

    ![13](media/solution-template-Databricks-notebook/copy-source-settings.png)

    ![14](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1.  전자 필기장 활동 **변환이** 만들어지고 이전 단계에서 만든 연결 된 서비스가 선택 됩니다.
    ![16](media/solution-template-Databricks-notebook/notebook-activity.png)

     1. **설정** 탭을 선택 합니다. *노트 경로의*경우 템플릿은 기본적으로 경로를 정의 합니다. **필수 구성 요소** 2에서 업로드 한 올바른 전자 필기장 경로를 찾아서 선택 해야 할 수 있습니다. 

         ![17](media/solution-template-Databricks-notebook/notebook-settings.png)
    
     1. 스크린샷에 표시 된 대로 생성 된 *기본 매개 변수* 를 확인 합니다. Data Factory에서 Databricks 노트북으로 전달 됩니다. 

         ![기본 매개 변수](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **파이프라인 매개 변수** 는 아래와 같이 정의 됩니다.

    ![15](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. 데이터 집합 설정
    1.  원본 데이터를 사용할 수 있는지 확인 하기 위해 **SourceAvailabilityDataset** 가 만들어집니다.

        ![9](media/solution-template-Databricks-notebook/source-availability-dataset.png)

    1.  **Sourcefilesdataset** -원본 데이터를 복사 하는 데 사용할입니다.

        ![10](media/solution-template-Databricks-notebook/source-file-dataset.png)

    1.  **Destinationfilesdataset** – 싱크/대상 위치로 복사 하는 데 사용할입니다.

        1.  연결 된 서비스-이전 단계에서 만든 *sinkBlob_LS* 입니다.

        2.  *Sinkdata/staged_sink*파일 경로입니다.

            ![11](media/solution-template-Databricks-notebook/destination-dataset.png)


1.  **디버그** 를 선택 하 여 파이프라인을 실행 합니다. 자세한 Spark 로그를 보려면 Databricks 로그 링크를 확인할 수 있습니다.

    ![18](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    스토리지 탐색기를 사용하여 데이터 파일을 확인할 수도 있습니다. Data Factory 파이프라인 실행과 상호 연결되기 위해 이 예제에서는 데이터 팩터리의 파이프라인 실행 ID를 출력 폴더에 추가합니다. 이렇게 하면 각 실행을 통해 생성된 파일을 다시 추적할 수 있습니다.

    ![19](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
