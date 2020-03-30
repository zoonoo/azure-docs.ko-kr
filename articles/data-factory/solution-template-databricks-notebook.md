---
title: Azure 데이터 브릭으로 변환
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
ms.openlocfilehash: 9a05b09f958d741fa56c586fbc7f5c5908dbbce6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384384"
---
# <a name="transformation-with-azure-databricks"></a>Azure 데이터 브릭으로 변환

이 자습서에서는 Azure Data Factory에서 **유효성 검사,** **데이터 복사**및 전자 **필기장** 활동을 포함하는 종단 간 파이프라인을 만듭니다.

- **유효성 검사는** 복사 및 분석 작업을 트리거하기 전에 원본 데이터 집합이 다운스트림 소비에 대비할 수 있도록 합니다.

- **데이터를 복사하면** 원본 데이터 집합이 Azure Databricks 전자 필기장에 DBFS로 탑재된 싱크 저장소에 복제됩니다. 이러한 방식으로 Spark에서 데이터 집합을 직접 사용할 수 있습니다.

- **노트북은** 데이터 집합을 변환하는 Databricks 노트북을 트리거합니다. 또한 처리된 폴더 또는 Azure SQL 데이터 웨어하우스에 데이터 집합을 추가합니다.

간단히 하기 위해 이 자습서의 템플릿은 예약된 트리거를 만들지 않습니다. 필요한 경우 추가할 수 있습니다.

![파이프라인 다이어그램](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>사전 요구 사항

- 싱크로 사용하기 위해 호출된 `sinkdata` 컨테이너가 있는 Azure Blob 저장소 계정입니다.

  저장소 계정 이름, 컨테이너 이름 및 액세스 키를 기록합니다. 템플릿의 나중에 이러한 값이 필요합니다.

- Azure 데이터 브릭 작업 영역입니다.

## <a name="import-a-notebook-for-transformation"></a>변환을 위한 노트북 가져오기

**변환** 전자 필기장을 Databricks 작업 공간으로 가져오려면 다음을 수행합니다.

1. Azure Databricks 작업 영역에 로그인한 다음 **가져오기를**선택합니다.
       ![작업 영역가져오기](media/solution-template-Databricks-notebook/import-notebook.png) 메뉴 명령 작업 영역 경로는 표시된 경로와 다를 수 있지만 나중에 기억하십시오.
1. **에서 가져오기: URL을**선택합니다. 텍스트 상자에 를 `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`입력합니다.

   ![전자 필기장 가져오기 선택](media/solution-template-Databricks-notebook/import-from-url.png)

1. 이제 저장소 연결 정보로 **변환** 전자 필기장을 업데이트해 보겠습니다.

   가져온 전자 필기장에서 다음 코드 조각과 같이 **명령 5로** 이동합니다.

   - 사용자 `<storage name>` `<access key>` 고유의 저장소 연결 정보를 교체하고 사용자 고유의 저장소 연결 정보로 바꿉습니다.
   - 저장소 계정을 컨테이너와 `sinkdata` 함께 사용합니다.

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
   1. Databricks 작업 영역에서 오른쪽 상단에 있는 사용자 프로필 아이콘을 선택합니다.
   1. **사용자 설정 선택**.
    ![사용자 설정에 대한 메뉴 명령](media/solution-template-Databricks-notebook/user-setting.png)
   1. 액세스 토큰 탭에서 **새 토큰 생성을** **선택합니다.**
   1. **생성**을 선택합니다.

    !["생성" 버튼](media/solution-template-Databricks-notebook/generate-new-token.png)

   나중에 Databricks 링크된 서비스를 만드는 데 사용할 수 있는 *액세스 토큰을 저장합니다.* 액세스 토큰은 다음과 `dapi32db32cbb4w6eee18b7d87e45exxxxxx`같습니다.

## <a name="how-to-use-this-template"></a>이 템플릿을 사용하는 방법

1. **Azure Databricks** 템플릿을 사용하여 변환으로 이동하여 다음 연결을 위해 새 연결된 서비스를 만듭니다.

   ![연결 설정](media/solution-template-Databricks-notebook/connections-preview.png)

    - **원본 Blob 연결** - 원본 데이터에 액세스합니다.

       이 연습에서는 원본 파일이 포함된 공용 Blob 저장소를 사용할 수 있습니다. 구성에 대한 다음 스크린샷을 참조합니다. 다음 **SAS URL을** 사용하여 원본 저장소에 연결합니다(읽기 전용 액세스)

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![인증 방법 및 SAS URL에 대한 선택](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **대상 Blob 연결** - 복사된 데이터를 저장합니다.

       새 **연결 서비스** 창에서 싱크 저장소 Blob을 선택합니다.

       ![새 연결 서비스로 저장소 Blob 싱크](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure 데이터 브릭** - 데이터 브릭 클러스터에 연결합니다.

        이전에 생성한 액세스 키를 사용하여 Databricks 연결 서비스를 만듭니다. *대화형 클러스터가* 있는 경우 선택하도록 선택할 수 있습니다. 이 예제에서는 **새 작업 클러스터** 옵션을 사용합니다.

        ![클러스터연결선택](media/solution-template-Databricks-notebook/databricks-connection.png)

1. **이 템플릿 사용을**선택합니다. 생성된 파이프라인이 표시됩니다.

    ![파이프라인 만들기](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>파이프라인 도입 및 구성

새 파이프라인에서 대부분의 설정은 기본값으로 자동으로 구성됩니다. 파이프라인의 구성을 검토하고 필요한 사항을 변경합니다.

1. 유효성 **검사** 활동 **가용성 플래그에서**원본 **데이터 집합** 값이 `SourceAvailabilityDataset` 이전에 만든 값으로 설정되어 있는지 확인합니다.

   ![원본 데이터 집합 값](media/solution-template-Databricks-notebook/validation-settings.png)

1. 데이터 활동 **파일-투-Blob** **복사에서** **소스** 및 **싱크** 탭을 확인합니다. 필요한 경우 설정을 변경합니다.

   - **소스** ![탭 소스 탭](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **싱크** ![탭 싱크 탭](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. 전자 **필기장** 활동 **변환에서**필요에 따라 경로 및 설정을 검토하고 업데이트합니다.

   **데이터 브릭 링크 된 서비스는** 이전 단계의 값으로 미리 채워야 합니다. ![](media/solution-template-Databricks-notebook/notebook-activity.png)

   **노트북** 설정을 확인하려면 다음을 수행하십시오.
  
    1. **설정** 탭을 선택합니다. **전자 필기장 경로의**경우 기본 경로가 올바른지 확인합니다. 올바른 전자 필기장 경로를 찾아보고 선택해야 할 수 있습니다.

       ![노트북 경로](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. 기본 **매개 변수** 선택기의 확장을 하고 매개 변수가 다음 스크린샷에 표시된 것과 일치하는지 확인합니다. 이러한 매개 변수는 데이터 팩터리의 Databricks 노트북에 전달됩니다.

       ![기본 매개 변수](media/solution-template-Databricks-notebook/base-parameters.png)

1. **파이프라인 매개 변수가** 다음 스크린샷에 표시된 ![것과 일치하는지 확인합니다.](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. 데이터 집합에 연결합니다.

   - **소스가용성데이터 집합** - 원본 데이터를 사용할 수 있는지 확인합니다.

     ![SourceAvailabilityDataset에 대한 연결된 서비스 및 파일 경로에 대한 선택](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **소스파일데이터집합** - 소스 데이터에 액세스합니다.

       ![SourceFilesDataset에 대한 연결된 서비스 및 파일 경로에 대한 선택](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **대상파일데이터 집합** - 데이터를 싱크 대상 위치로 복사합니다. 다음 값을 사용합니다.

     - **이전** - `sinkBlob_LS`단계에서 만든 연결된 서비스입니다.

     - **파일 경로** - `sinkdata/staged_sink`.

       ![대상 파일데이터 집합에 대한 연결된 서비스 및 파일 경로에 대한 선택](media/solution-template-Databricks-notebook/destination-dataset.png)

1. 파이프라인을 실행하려면 **디버그를** 선택합니다. 자세한 스파크 로그에 대한 자세한 내용은 Databricks 로그에 대한 링크를 찾을 수 있습니다.

    ![출력에서 데이터 브릭 로그에 대한 링크](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Azure 저장소 탐색기를 사용하여 데이터 파일을 확인할 수도 있습니다.

    > [!NOTE]
    > 데이터 팩터리 파이프라인 실행과 상관 관계가 있는 경우 이 예제에서는 데이터 팩터리에서 출력 폴더로 파이프라인 실행 ID를 부화합니다. 이렇게 하면 각 실행에 의해 생성된 파일을 추적하는 데 도움이 됩니다.
    > ![추가된 파이프라인 실행 ID](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
