---
title: "Azure Data Factory를 사용하여 Azure Data Lake Store로 데이터 로드 | Microsoft Docs"
description: "Azure Data Factory를 사용하여 Azure Data Lake Store로 데이터 복사"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 3f73cd65b0ceb3148ce8ceb83d7b4e1be1280077
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Store로 데이터 로드

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)는 빅 데이터 분석 작업을 위한 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. Azure 데이터 레이크를 사용하면 작동 및 예비 분석에 대해 한 곳에서 모든 크기, 형식 및 수집 속도의 데이터를 캡처할 수 있습니다.

Azure Data Factory는 완벽하게 관리되는 클라우드 기반 데이터 통합 서비스이며, 레이크를 기존 시스템에서 나온 데이터로 채우는 데 사용되므로 분석 솔루션을 빌드하는 동안 소중한 시간을 절약할 수 있습니다. Azure Data Factory를 사용하여 Azure Data Lake Store로 데이터를 로드하는 방법의 주요 이점은 다음과 같습니다.

* **간편한 설정**: 스크립팅이 필요하지 않은 직관적인 5단계 마법사.
* **풍부한 데이터 저장소 지원**: 풍부한 온-프레미스 및 클라우드 기반 데이터 저장소에 대한 기본 제공 지원은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표의 자세한 목록을 참조하세요.
* **보안 및 호환**: 데이터가 HTTPS 또는 ExpressRoute를 통해 전송되고 글로벌 서비스를 제공하므로 데이터가 지리적 경계를 벗어나지 않음
* **고성능**: Azure Data Lake Store로 최대 1GBps의 데이터 로딩 속도를 제공합니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조하세요.

이 문서에서는 Data Factory 복사 데이터 도구를 사용하여 **Amazon S3의 데이터를 Azure Data Lake Store로 로드**하는 방법을 설명합니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

> [!NOTE]
>  Azure Data Lake Store 간에 데이터를 복사하는 Data Factory 기능에 대한 일반적인 내용은 [Azure Data Factory를 사용하여 Azure Data Lake Store 간 데이터 복사](connector-azure-data-lake-store.md) 문서를 참조하세요.
>
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 복사 작업](v1/data-factory-data-movement-activities.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
* **Azure Data Lake Store**. Data Lake Store 계정이 없는 경우 새로 만드는 단계는 [Data Lake Store 계정 만들기](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account) 문서를 참조하세요.
* **Amazon S3**. 이 문서는 Amazon S3에서 데이터를 복사하는 방법을 보여 줍니다. 다음과 같은 유사한 단계를 수행하여 다른 데이터 저장소를 사용할 수 있습니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**을 클릭하고 **Data Factory**를 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 아래 스크린샷에 표시된 대로 값을 제공합니다. 
      
     ![새 데이터 팩터리 페이지](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **이름.** 데이터 팩터리의 고유한 이름을 입력합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialDataFactory) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
            `Data factory name "LoadADLSDemo" is not available`

    * **구독.** 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
    * **리소스 그룹.** 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
    * **버전.** **V2(미리 보기)**를 선택합니다.
    * **위치.** 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용하는 데이터 저장소(Azure Data Lake Store, Azure Storage, Azure SQL Database 등)는 다른 위치/지역에 있을 수 있습니다.

3. **만들기**를 클릭합니다.
4. 만들기가 완료된 다음 데이터 팩터리로 이동하면 이미지와 같은 **Data Factory** 페이지가 표시됩니다. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 데이터 통합 응용 프로그램을 시작합니다. 
   
   ![데이터 팩터리 홈페이지](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>Azure Data Lake Store에 데이터 로드

1. 시작 페이지에서 **데이터 복사** 타일을 클릭하여 데이터 복사 도구를 실행합니다. 

   ![데이터 복사 도구 타일](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. 데이터 복사 도구의 **속성** 페이지에서 **작업 이름**에 대해 **CopyFromAmazonS3ToADLS**를 지정하고 **다음**을 클릭합니다. 

    ![데이터 복사 도구 - 속성 페이지](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 **Amazon S3**을 선택하고 **다음**을 클릭합니다.

    ![원본 데이터 저장소 페이지](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. **Amazon S3 연결 지정** 페이지에서 다음 단계를 수행합니다. 
    1. **액세스 키 ID**를 지정합니다.
    2. **비밀 액세스 키**를 지정합니다.
    3. **다음**을 클릭합니다. 

        ![Amazon S3 계정 지정](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. **입력 파일 또는 폴더 선택** 페이지에서 복사할 폴더/파일로 이동하여 선택하고 **선택**을 클릭한 후 **다음**을 클릭합니다. 

    ![입력 파일 또는 폴더 선택](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. **대상 데이터 저장소** 페이지에서 **Azure Data Lake Store**를 선택하고 **다음**을 클릭합니다. 

    ![대상 데이터 저장소 페이지](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. 이 페이지에서 **재귀적으로 파일 복사** 및 **이진 복사**(있는 그대로 파일 복사) 옵션을 선택하여 복사 동작을 선택합니다. **다음**을 클릭합니다.

    ![출력 폴더 지정](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. **Data Lake Store 연결 지정** 페이지에서 다음 단계를 수행합니다. 

    1. **Data Lake Store 계정 이름**에 대해 Data Lake Store를 선택합니다.
    2. **테넌트**, **서비스 주체 ID** 및 **서비스 주체 키**를 포함한 서비스 주체 정보를 지정합니다.
    3. **다음**을 클릭합니다. 

    > [!IMPORTANT]
    > 이 연습에서는 **서비스 주체**를 사용하여 Data Lake Store를 인증합니다. [여기](connector-azure-data-lake-store.md#using-service-principal-authentication)의 지침을 따라 Azure Data Lake Store에서 서비스 주체에게 적절한 권한을 부여해야 합니다.

    ![Azure Data Lake Store 계정을 지정합니다.](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. **출력 파일 또는 폴더 선택** 페이지에서 **copyfroms3**을 지정한 후 **다음**을 클릭합니다. 

    ![출력 폴더 지정](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. **설정** 페이지에서 **다음**을 클릭합니다. 

    ![설정 페이지](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. **요약** 페이지에서 설정을 검토하고 **다음**을 클릭합니다.

    ![요약 페이지](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. **배포 페이지**에서 **모니터**를 클릭하여 파이프라인(작업)을 모니터링합니다.

    ![배포 페이지](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에서 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 표시됩니다. 

    ![파이프라인 실행 모니터링](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 클릭합니다. 파이프라인에는 하나의 활동(복사 활동)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 다시 전환하려면 위쪽의 **파이프라인** 링크를 클릭합니다. **새로 고침**을 클릭하여 목록을 새로 고칩니다. 

    ![작업 실행 모니터링](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. 활동 모니터링 보기의 **작업** 아래에 있는 **세부 정보** 링크를 클릭하여 각 복사 작업의 실행 세부 정보를 추가로 모니터링할 수 있습니다. 여기에는 원본에서 싱크로 복사되는 데이터의 양, 처리량, 해당 기간 동안 진행하는 단계 및 사용된 구성을 포함한 정보가 표시됩니다.

    ![활동 실행 세부 정보 모니터링](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. 데이터가 Azure Data Lake Store에 복사되었는지 확인합니다. 

    ![Data Lake Store 출력 확인](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하여Azure Data Lake Store 지원에 대해 알아봅니다. 

> [!div class="nextstepaction"]
>[Azure Data Lake Store 커넥터](connector-azure-data-lake-store.md)