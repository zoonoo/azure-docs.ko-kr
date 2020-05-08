---
title: Azure Databricks를 통한 변환
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
ms.date: 04/27/2020
ms.openlocfilehash: 2503c26ac0348739bbf117c3538af797833ce8b8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857650"
---
# <a name="transformation-with-azure-databricks"></a>Azure Databricks를 통한 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Azure Data Factory의 **유효성 검사**, **데이터 복사**및 **노트북** 작업을 포함 하는 종단 간 파이프라인을 만듭니다.

- **유효성 검사** 를 사용 하면 복사 및 분석 작업을 트리거하기 전에 원본 데이터 집합이 다운스트림 사용에 대해 준비 됩니다.

- **데이터 복사** 는 Azure Databricks 노트북에서 dbfs로 탑재 된 싱크 저장소로 원본 데이터 집합을 복제 합니다. 이러한 방식으로 Spark에서 데이터 집합을 직접 사용할 수 있습니다.

- **노트북** 은 데이터 집합을 변환 하는 Databricks 노트북을 트리거합니다. 또한 처리 된 폴더 또는 Azure SQL Data Warehouse에 데이터 집합을 추가 합니다.

간단히 하기 위해이 자습서의 템플릿은 예약 된 트리거를 만들지 않습니다. 필요한 경우 하나를 추가할 수 있습니다.

![파이프라인 다이어그램](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>사전 요구 사항

- 싱크로 사용 하기 위해 라는 `sinkdata` 컨테이너가 있는 Azure Blob storage 계정.

  저장소 계정 이름, 컨테이너 이름 및 액세스 키를 적어 둡니다. 이러한 값은 나중에 템플릿에서 필요 합니다.

- Azure Databricks 작업 영역입니다.

## <a name="import-a-notebook-for-transformation"></a>변환을 위해 노트북 가져오기

Databricks 작업 영역으로 **변환** 노트북을 가져오려면 다음을 수행 합니다.

1. Azure Databricks 작업 영역에 로그인 하 고 **가져오기**를 선택 합니다.
       ![작업 영역](media/solution-template-Databricks-notebook/import-notebook.png) 을 가져오는 메뉴 명령 작업 영역 경로는 표시 된 것과 다를 수 있지만 나중에 사용할 수 있습니다.
1. **다음에서 가져오기: URL**을 선택 합니다. 텍스트 상자에을 입력 `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`합니다.

   ![노트북 가져오기에 대 한 선택 항목](media/solution-template-Databricks-notebook/import-from-url.png)

1. 이제 저장소 연결 정보를 사용 하 여 **변환** 노트북을 업데이트 해 보겠습니다.

   가져온 노트북에서 다음 코드 조각과 같이 **명령 5** 로 이동 합니다.

   - 및 `<storage name>` `<access key>` 를 사용자 고유의 저장소 연결 정보로 바꿉니다.
   - `sinkdata` 컨테이너에 저장소 계정을 사용 합니다.

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

1. Databricks에 액세스할 Data Factory에 대해 **Databricks 액세스 토큰**을 생성합니다.
   1. Databricks 작업 영역에서 오른쪽 위에 있는 사용자 프로필 아이콘을 선택 합니다.
   1. **사용자 설정**을 선택 합니다.
    ![사용자 설정에 대 한 메뉴 명령](media/solution-template-Databricks-notebook/user-setting.png)
   1. **액세스 토큰** 탭에서 **새 토큰 생성** 을 선택 합니다.
   1. **생성**을 선택합니다.

    !["생성" 단추](media/solution-template-Databricks-notebook/generate-new-token.png)

   나중에 Databricks 연결 된 서비스를 만드는 데 사용 하기 위해 *액세스 토큰을 저장* 합니다. 액세스 토큰은와 `dapi32db32cbb4w6eee18b7d87e45exxxxxx`유사 합니다.

## <a name="how-to-use-this-template"></a>이 템플릿을 사용 하는 방법

1. **Azure Databricks 템플릿으로 변환** 으로 이동 하 고 다음 연결에 대 한 새 연결 된 서비스를 만듭니다.

   ![연결 설정](media/solution-template-Databricks-notebook/connections-preview.png)

    - 원본 **Blob 연결** -원본 데이터에 액세스 합니다.

       이 연습에서는 원본 파일을 포함 하는 공용 blob 저장소를 사용할 수 있습니다. 구성에 대 한 다음 스크린샷을 참조 합니다. 다음 **SAS URL** 을 사용 하 여 원본 저장소 (읽기 전용 액세스)에 연결 합니다.

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![인증 방법 및 SAS URL에 대 한 선택 항목](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **대상 Blob 연결** -복사 된 데이터를 저장 합니다.

       **새 연결 된 서비스** 창에서 싱크 저장소 blob을 선택 합니다.

       ![새 연결 된 서비스로 싱크 저장소 blob](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** -Databricks 클러스터에 연결 합니다.

        이전에 생성 한 액세스 키를 사용 하 여 Databricks 연결 된 서비스를 만듭니다. *대화형 클러스터가* 있는 경우 선택 하도록 선택할 수 있습니다. 이 예에서는 **새 작업 클러스터** 옵션을 사용 합니다.

        ![클러스터에 연결 하기 위한 선택 항목](media/solution-template-Databricks-notebook/databricks-connection.png)

1. **이 템플릿 사용**을 선택 합니다. 파이프라인이 생성 된 것을 볼 수 있습니다.

    ![파이프라인 만들기](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>파이프라인 소개 및 구성

새 파이프라인에서 대부분의 설정은 기본값을 사용 하 여 자동으로 구성 됩니다. 파이프라인의 구성을 검토 하 고 필요한 변경 작업을 수행 합니다.

1. **유효성 검사** 작업 **가용성 플래그**에서 원본 **데이터 집합** 값이 이전에 만든으로 `SourceAvailabilityDataset` 설정 되어 있는지 확인 합니다.

   ![원본 데이터 집합 값](media/solution-template-Databricks-notebook/validation-settings.png)

1. **데이터 복사** 작업 **파일-blob**에서 **원본** 및 **싱크** 탭을 선택 합니다. 필요한 경우 설정을 변경 합니다.

   - **원본** 탭 ![원본 탭](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **싱크** 탭 ![싱크 탭](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. **노트북** 활동 **변환**에서 필요에 따라 경로와 설정을 검토 하 고 업데이트 합니다.

   **Databricks 연결 된 서비스** 는 Databricks 연결 된 서비스에 대해 채워진 값과 같이 이전 단계의 값으로 ![미리 채워야 합니다.](media/solution-template-Databricks-notebook/notebook-activity.png)

   **노트북** 설정을 확인 하려면:
  
    1. **설정** 탭을 선택 합니다. **전자 필기장 경로**의 경우 기본 경로가 올바른지 확인 합니다. 올바른 전자 필기장 경로를 찾아서 선택 해야 할 수 있습니다.

       ![전자 필기장 경로](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. **기본 매개 변수** 선택기를 확장 하 고 다음 스크린샷에 표시 된 것과 일치 하는 매개 변수를 확인 합니다. 이러한 매개 변수는 Data Factory에서 Databricks 노트북으로 전달 됩니다.

       ![기본 매개 변수](media/solution-template-Databricks-notebook/base-parameters.png)

1. **파이프라인 매개 변수가** 다음 스크린샷에 표시 된 것과 일치 하는지 확인 합니다. ![파이프라인 매개 변수](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. 데이터 집합에 연결 합니다.

    >[!NOTE]
    >아래 데이터 집합에서 파일 경로가 템플릿에 자동으로 지정 되었습니다. 변경 해야 하는 경우 연결 오류가 발생 하는 경우 **컨테이너** 와 **디렉터리** 에 대 한 경로를 지정 해야 합니다.

   - **SourceAvailabilityDataset** -원본 데이터를 사용할 수 있는지 확인 합니다.

     ![SourceAvailabilityDataset에 대 한 연결 된 서비스 및 파일 경로 선택](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **Sourcefilesdataset** -원본 데이터에 액세스 합니다.

       ![SourceFilesDataset의 연결 된 서비스 및 파일 경로에 대 한 선택](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **Destinationfilesdataset** -데이터를 싱크 대상 위치로 복사 합니다. 다음 값을 사용합니다.

     - **Linked service** - `sinkBlob_LS`이전 단계에서 만든 연결 된 서비스입니다.

     - **파일 경로** - `sinkdata/staged_sink`입니다.

       ![DestinationFilesDataset의 연결 된 서비스 및 파일 경로에 대 한 선택 항목](media/solution-template-Databricks-notebook/destination-dataset.png)

1. **디버그** 를 선택 하 여 파이프라인을 실행 합니다. Databricks 로그에 대 한 링크를 통해 더 자세한 Spark 로그를 확인할 수 있습니다.

    ![출력에서 Databricks 로그에 연결](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Azure Storage 탐색기를 사용 하 여 데이터 파일을 확인할 수도 있습니다.

    > [!NOTE]
    > Data Factory 파이프라인 실행과의 상관 관계를 위해이 예제에서는 파이프라인 실행 ID를 데이터 팩터리에 있는 출력 폴더에 추가 합니다. 이렇게 하면 각 실행에 의해 생성 된 파일을 추적 하는 데 도움이 됩니다.
    > ![추가 된 파이프라인 실행 ID](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
