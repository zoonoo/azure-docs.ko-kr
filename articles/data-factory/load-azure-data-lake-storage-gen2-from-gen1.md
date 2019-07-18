---
title: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사
description: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068983"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사

Azure Data Lake 저장소 Gen2로 빌드되는 빅 데이터 분석 전용 기능 집합이 [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md)합니다. 파일 시스템 및 개체 저장소 패러다임을 사용 하 여 데이터를 사용 하 여 인터페이스를 사용할 수 있습니다.

현재 Azure Data Lake 저장소 Gen1를 사용 하는 경우에 Azure Data Factory를 사용 하 여 데이터 레이크 저장소 Gen1에서 Gen2로 데이터를 복사 하 여 Azure Data Lake 저장소 Gen2를 평가할 수 있습니다.

Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 서비스를 사용 하 여 다양 한 온-프레미스에서에서 데이터 레이크를 채우는 데 및 클라우드 기반 데이터 저장소 및 분석 솔루션을 작성 하는 경우 시간을 절약 합니다. 목록은 지원 되는 커넥터의 표를 참조 하십시오 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)합니다.

Azure Data Factory는 스케일 아웃, 관리되는 데이터 이동 솔루션을 제공합니다. Data Factory의 스케일 아웃 아키텍처에 따라 높은 처리량으로 데이터를 수집할 수 것입니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)합니다.

이 문서에서는 Data Factory 복사 데이터 도구를 사용 하 여 Azure Data Lake 저장소 Gen2로 Azure 데이터 레이크 저장소 Gen1에서 데이터를 복사 하는 방법을 보여 줍니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터가 있는 Azure Data Lake Storage Gen1 계정
* 사용 하도록 설정 하는 Data Lake 저장소 Gen2를 사용 하 여 azure Storage 계정. 저장소 계정에 없다면 [계정을 만들](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽된 메뉴에서 선택 **리소스 만들기** > **데이터 + 분석** > **Data Factory**합니다.
   
   ![새 창에서 데이터 팩터리 선택](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 에 **새 데이터 팩터리** 페이지에서 다음 이미지에 표시 되는 필드에 대 한 값을 제공 합니다. 
      
   ![새 데이터 팩터리 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **이름**: Azure Data Factory의 전역적으로 고유 이름을 입력합니다. "데이터 팩터리 이름 \"LoadADLSDemo\"를 사용할 수 없습니다" 오류가 발생하면 데이터 팩터리의 다른 이름을 입력합니다. 예를 들어 _**yourname**_ **ADFTutorialDataFactory**를 사용합니다. 데이터 팩터리를 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택 합니다. 선택할 수도 있습니다는 **새로 만들기** 옵션 및 리소스 그룹의 이름을 입력 합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요. 
    * **버전**: **V2**를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용 되는 데이터 저장소에서 다른 위치 및 지역 수 있습니다. 

3. **만들기**를 선택합니다.
4. 만들기가 완료 되 면 데이터 팩터리로 이동 합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다. 
   
   ![데이터 팩터리 홈페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. 선택 된 **작성 및 모니터링** 타일을 별도 탭에서 데이터 통합 응용 프로그램을 시작 합니다.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 데이터 로드

1. 에 **시작** 페이지에서를 **데이터 복사** 타일 복사 데이터 도구를 시작 합니다. 

   ![데이터 복사 도구 타일](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. 에 **속성** 페이지에서 지정 **CopyFromADLSGen1ToGen2** 에 대 한 합니다 **작업 이름** 필드입니다. **다음**을 선택합니다.

    ![속성 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. 에 **원본 데이터 저장소** 페이지에서 **+ 새 연결 만들기**합니다.

    ![원본 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. 선택 **Azure Data Lake 저장소 Gen1** 에서 선택한 커넥터 갤러리 **계속**합니다.
    
    ![원본 데이터 저장소 Azure Data Lake Storage Gen1 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. 에 **Azure Data Lake 저장소 Gen1 지정 연결** 페이지에서 다음이 단계를 수행 합니다.

   a. 계정 이름으로 Data Lake Storage Gen1을 선택하고 **Tenant**를 지정하거나 유효한지 검사합니다.
  
   b. 선택 **연결 테스트** 설정을 확인할 수 있습니다. 그런 다음, **마침**을 선택합니다.
  
   c. 새 연결을 만든 것이 표시 됩니다. **다음**을 선택합니다.
   
   > [!IMPORTANT]
   > 이 연습에서는 인증에 Azure 데이터 레이크 저장소 Gen1 Azure 리소스에 대 한 관리 되는 id를 사용 합니다. 관리 되는 id를 Azure 데이터 레이크 저장소 Gen1에서 적절 한 사용 권한을 부여 하려면 다음과 [이러한 지침](connector-azure-data-lake-store.md#managed-identity)합니다.
   
   ![Azure Data Lake Storage Gen1 계정 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. 에 **입력된 파일 또는 폴더 선택** 페이지에서 복사 하려는 파일과 폴더를 이동 합니다. 폴더 또는 파일을 선택 하 고 선택 **선택**합니다.

    ![입력 파일 또는 폴더 선택](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. 선택 하 여 복사 동작을 지정 합니다 **재귀적으로 파일 복사** 하 고 **이진 복사** 옵션입니다. **다음**을 선택합니다.

    ![출력 폴더 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. 에 **대상 데이터 저장소** 페이지에서 **+ 새 연결 만들기** > **Azure Data Lake 저장소 Gen2**  >   **계속**합니다.

    ![대상 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. 에 **Azure Data Lake 저장소 Gen2 지정 연결** 페이지에서 다음이 단계를 수행 합니다.

   a. Data Lake 저장소 Gen2 지원 계정을 선택 합니다 **저장소 계정 이름** 드롭 다운 목록.
   
   b. **마침**을 선택하여 연결을 만듭니다. 그런 후 **다음**을 선택합니다.
   
   ![Azure Data Lake Storage Gen2 계정 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. 에 **출력 파일 또는 폴더 선택** 페이지에서 입력 **copyfromadlsgen1** 출력 폴더 이름 및 선택 **다음**합니다. Data Factory 없는 경우 복사 하는 동안 해당 Azure Data Lake 저장소 Gen2 파일 시스템 및 하위 폴더를 만듭니다.

    ![출력 폴더 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. 에 **설정을** 페이지에서 **다음** 기본 설정을 사용 하도록 합니다.

12. 에 **요약** 페이지에서 설정을 검토 하 고 선택 **다음**합니다.

    ![요약 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. 에 **배포 페이지**를 선택 **모니터** 파이프라인을 모니터링 하려면.

    ![배포 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다.

    ![파이프라인 실행 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 전환하려면 위쪽의 **파이프라인** 링크를 선택합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다. 

    ![작업 실행 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. 각 복사 작업의 실행 세부 정보를 모니터링하려면 작업 모니터링 보기의 **작업** 아래에서 **세부 정보** 링크(안경 이미지)를 선택합니다. 데이터 볼륨에 복사할 원본에서 싱크로, 데이터 처리량, 해당 기간의 실행 단계 및 사용 하는 구성을 같은 세부 정보를 모니터링할 수 있습니다.

    ![작업 실행 세부 정보 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Azure Data Lake 저장소 Gen2 계정으로 데이터 복사 되는지 확인 합니다.

## <a name="best-practices"></a>모범 사례

참조의 일반적 Azure 데이터 레이크 저장소 Gen2로 Azure Data Lake 저장소 Gen1에서 업그레이드 평가 [빅 데이터 분석 솔루션을 Azure 데이터 레이크 저장소 Gen1에서 Azure 데이터 레이크 저장소 Gen2로 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)합니다. 다음 섹션에서는 Data Factory를 사용 하 여 Data Lake 저장소 Gen2로 Data Lake 저장소 Gen1에서 데이터 업그레이드를 위한 모범 사례를 소개 합니다.

### <a name="data-partition-for-historical-data-copy"></a>기록 데이터 복사본에 대 한 데이터 파티션

- 데이터 레이크 저장소 Gen1의 총 데이터 크기 보다 작은 30TB 이며 파일 수가 보다 작거나 1 백만, 경우에 단일 복사 작업 실행에 모든 데이터를 복사할 수 있습니다.
- 더 많은 데이터를 복사 하려면 있거나 일괄 처리에서 데이터 마이그레이션을 관리 하 고 각각의 특정 시간 프레임 내에 완료 하는 유연성을 원하는 경우 데이터를 분할 합니다. 또한 분할 하면 예기치 않은 문제가 위험이 줄어듭니다.

개념 증명을 사용 하 여 종단 간 솔루션을 확인 하 고 사용자 환경에서 복사 처리량 테스트. 주요 개념 단계: 

1. 데이터 레이크 저장소 Gen1에서 복사 성능 기준을 확보 하려면 Data Lake 저장소 Gen2로 몇 Tb의 데이터를 복사 하는 단일 복사 작업을 사용 하 여 하나의 Data Factory 파이프라인을 만듭니다. 시작 [데이터 통합 단위 (DIUs)](copy-activity-performance.md#data-integration-units) 128로 합니다. 
2. 1 단계에서 표시 하는 복사 처리량을 기준, 전체 데이터 마이그레이션에 필요한 예상된 시간으로 계산 합니다. 
3. (선택 사항) 제어 테이블을 만들고 마이그레이션할 파일을 분할 하는 파일 필터를 정의 합니다. 파일을 분할 하는 방법은 다음과 같습니다. 하 

    - 폴더 이름 또는 와일드 카드 필터를 사용 하 여 폴더 이름으로 분할 합니다. 이 방법을 사용 하는 것이 좋습니다.
    - 파티션 파일의 마지막 수정 시간입니다.

### <a name="network-bandwidth-and-storage-io"></a>네트워크 대역폭 및 저장소 I/O 

데이터 레이크 저장소 Gen1에서 데이터를 읽고 Data Lake 저장소 Gen2로 데이터를 작성 하는 Data Factory 복사 작업의 동시성을 제어할 수 있습니다. 이러한 방식으로 해당 저장소에 Data Lake 저장소 Gen1 정상적인 비즈니스 작업을 마이그레이션하는 동안 영향을 주지 않으려면 I/O에서 사용을 관리할 수 있습니다.

### <a name="permissions"></a>권한 

Data Factory에서 합니다 [Data Lake 저장소 Gen1 커넥터](connector-azure-data-lake-store.md) 지 원하는 Azure 리소스 인증에 대 한 보안 주체 및 관리 되는 id 서비스입니다. 합니다 [Data Lake 저장소 Gen2 커넥터](connector-azure-data-lake-storage.md) 지원 계정 키, 서비스 주체 및 Azure 리소스 인증에 대 한 관리 되는 id입니다. 데이터 팩터리 이동 및 모든 파일을 복사 또는 액세스 제어 목록 (Acl)를 액세스, 읽고, 또는 모든 파일을 작성 하려는 경우 Acl을 설정 제공 하는 계정에 대 한 충분 한 권한이 높은 부여를 할 수 있도록 합니다. 부여 슈퍼 사용자 또는 소유자 역할을 마이그레이션 기간 중입니다. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1에서 Acl을 유지 합니다.

데이터 레이크 저장소 Gen1에서 Data Lake 저장소 Gen2로 업그레이드 하는 경우 데이터 파일과 함께 Acl을 복제 하려는 경우 참조 [Data Lake 저장소 Gen1에서 Acl 유지](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)합니다. 

### <a name="incremental-copy"></a>증분 복사 

데이터 레이크 저장소 Gen1에서 새롭거나 업데이트 된 파일만 로드 하려면 여러 가지 방법으로 사용할 수 있습니다.

- 분할 된 폴더 또는 파일 이름에서 새롭거나 업데이트 된 파일을 로드 합니다. 예로 2019/05/13 / *.
- LastModifiedDate 하 여 새롭거나 업데이트 된 파일을 로드 합니다.
- 타사 도구 또는 솔루션에 따라 새롭거나 업데이트 된 파일을 식별 합니다. 다음 매개 변수는 테이블 또는 파일을 통해 파일 또는 폴더 이름을 Data Factory 파이프라인으로 전달 합니다. 

증분 로드를 위해 적절 한 빈도 Azure 데이터 레이크 저장소 Gen1 파일의 총 수 및 로드할 때마다 새롭거나 업데이트 된 파일의 볼륨에 따라 달라 집니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [복사 작업 개요](copy-activity-overview.md)
> [Azure Data Lake 저장소 Gen1 커넥터](connector-azure-data-lake-store.md)
> [Azure Data Lake 저장소 Gen2 커넥터](connector-azure-data-lake-storage.md)