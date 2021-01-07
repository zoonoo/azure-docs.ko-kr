---
title: Azure Data Lake Storage Gen2에 데이터 로드
description: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 복사
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: ca9ca495f2b3449b5aeb933bbd8d312fc9341fd9
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554137"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 로드

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2는 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)를 기반으로 하는 빅 데이터 분석 전용의 기능 세트입니다. 이를 사용하면 파일 시스템 및 개체 스토리지 패러다임을 모두 사용하여 데이터를 조작할 수 있습니다.

ADF (Azure Data Factory)는 완전히 관리 되는 클라우드 기반 데이터 통합 서비스입니다. 분석 솔루션을 빌드할 때 서비스를 사용하여 풍부한 온-프레미스 및 크라우드 기반 데이터 저장소의 데이터로 레이크를 채우고 시간을 절약할 수 있습니다. 지원되는 커넥터의 자세한 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 스케일 아웃, 관리되는 데이터 이동 솔루션을 제공합니다. ADF의 스케일 아웃 아키텍처로 인해 높은 처리량으로 데이터를 수집할 수 있습니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조하세요.

이 아티클에서는 Data Factory 복사 데이터 도구를 사용하여 _Amazon Web Services S3 서비스_ 의 데이터를 _Azure Data Lake Storage Gen2_ 로 로드하는 방법을 설명합니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

>[!TIP]
>Azure Data Lake Storage Gen1에서 Gen2로 데이터를 복사하는 방법은 [이 연습](load-azure-data-lake-storage-gen2-from-gen1.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Data Lake Storage Gen2 사용 하도록 설정 된 Azure Storage 계정: 저장소 계정이 없는 경우 [계정을 만듭니다](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* 데이터를 포함하는 S3 버킷을 포함한 AWS 계정: 이 아티클에서는 Amazon S3에서 데이터를 복사하는 방법을 보여줍니다. 다음과 같은 유사한 단계를 수행하여 다른 데이터 저장소를 사용할 수 있습니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 **리소스**  >  **통합**  >  **Data Factory** 만들기를 선택 합니다.
   
   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지에서 다음 필드에 대 한 값을 제공 합니다.
 
    * **Name** : Azure Data Factory의 전역적으로 고유 이름을 입력합니다. "Data factory name *YourDataFactoryName* 를 사용할 수 없습니다." 오류가 표시 되 면 데이터 팩터리에 대해 다른 이름을 입력 합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory** 라는 이름을 사용할 수 있습니다. 데이터 팩터리를 다시 만들어 봅니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독** : 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹** : 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.  
    * **버전** : **V2** 를 선택합니다.
    * **위치** : 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 

3. **만들기** 를 선택합니다.

4. 만들기가 완료되면 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다. 
   
   ![데이터 팩터리 홈페이지](./media/doc-common-process/data-factory-home-page.png)

   **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 데이터 통합 애플리케이션을 시작합니다.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 데이터 로드

1. **시작** 페이지에서 **데이터 복사** 타일을 선택 하 여 데이터 복사 도구를 시작 합니다.

2. **속성** 페이지에서 **작업 이름** 필드에 대해 **CopyFromAmazonS3ToADLS** 를 지정 하 고 **다음** 을 선택 합니다.

    ![속성 페이지](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 **+ 새 연결 만들기** 를 클릭 합니다. 커넥터 갤러리에서 **Amazon S3** 을 선택 하 고 **계속** 을 선택 합니다.
    
    ![원본 데이터 저장소 s3 페이지](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. **새 연결 된 서비스 (Amazon S3)** 페이지에서 다음 단계를 수행 합니다.

   1. **액세스 키 ID** 값을 지정합니다.
   2. **비밀 액세스 키** 값을 지정합니다.
   3. **연결 테스트** 를 클릭하여 설정의 유효성을 검사한 다음, **만들기** 를 선택합니다.

      ![Amazon S3 계정 지정](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. 새 AmazonS3 연결이 생성 되는 것을 볼 수 있습니다. **다음** 을 선택합니다. 

5. **입력 파일 또는 폴더 선택** 페이지에서, 복사하려는 폴더 및 파일로 이동합니다. 폴더/파일을 선택한 다음 선택을 선택 **합니다.**

    ![입력 파일 또는 폴더 선택](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. **재귀적** 및 **이진 복사** 옵션을 선택 하 여 복사 동작을 지정 합니다. **다음** 을 선택합니다.

    ![스크린샷 이진 복사를 선택할 수 있는 입력 파일 또는 폴더를 선택 하는 방법을 보여 줍니다.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. **대상 데이터 저장소** 페이지에서 **+ 새 연결 만들기** 를 클릭 한 다음 **Azure Data Lake Storage Gen2** 를 선택 하 고 **계속** 을 선택 합니다.

    ![대상 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. **새 연결 된 서비스 (Azure Data Lake Storage Gen2)** 페이지에서 다음 단계를 수행 합니다.

   1. "스토리지 계정 이름" 드롭다운 목록에서 Data Lake Storage Gen2 지원 계정을 선택합니다.
   2. **만들기** 를 선택 하 여 연결을 만듭니다. **다음** 을 선택합니다.   

        ![Azure Data Lake Storage Gen2 계정 지정](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. **출력 파일 또는 폴더 선택** 페이지에서 출력 폴더 이름으로 **copyfroms3** 를 입력 하 고 **다음** 을 선택 합니다. ADF는 복사 중에 해당 하는 ADLS Gen2 파일 시스템 및 하위 폴더를 만듭니다 (없는 경우).

    ![스크린샷 입력 한 폴더 경로를 표시 합니다.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. **설정** 페이지에서 **다음** 을 선택 하 여 기본 설정을 사용 합니다.

    ![설정 페이지](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. **요약** 페이지에서 설정을 검토 하 고 **다음** 을 선택 합니다.

    ![요약 페이지](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. **배포 페이지** 에서 **모니터** 를 선택하여 파이프라인(작업)을 모니터링합니다. 
 
13. 파이프라인 실행이 성공적으로 완료 되 면 수동 트리거에서 트리거된 파이프라인 실행이 표시 됩니다. **파이프라인 이름** 열 아래의 링크를 사용하여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.

    ![파이프라인 실행 모니터링](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. 파이프라인 실행과 연결 된 활동 실행을 보려면 파이프라인 이름 열에서 **CopyFromAmazonS3ToADLS** 링크를 선택 합니다. 복사 작업에 대 한 자세한 내용을 보려면 작업 이름 열에서 **세부 정보** 링크 (안경 아이콘)를 선택 합니다. 원본에서 싱크로 복사 되는 데이터 볼륨, 데이터 처리량, 해당 기간의 실행 단계 및 사용 된 구성과 같은 세부 정보를 모니터링할 수 있습니다.
 
    ![작업 실행 모니터링](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![작업 실행 세부 정보 모니터링](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. 보기를 새로 고치려면 새로 고침을 선택합니다. 파이프라인 실행 보기로 돌아가려면 위쪽에 있는 **모든 파이프라인 실행** 을 선택합니다.

16. 데이터가 Data Lake Storage Gen2 계정에 복사되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [복사 작업 개요](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2 커넥터](connector-azure-data-lake-storage.md)
