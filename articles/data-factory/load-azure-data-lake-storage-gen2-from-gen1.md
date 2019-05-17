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
ms.openlocfilehash: d6e09ec1f070f9ee0f4162524e4bd80d1f81adc3
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560650"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사

Azure Data Lake Storage Gen2는 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)를 기반으로 하는 빅 데이터 분석 전용의 기능 세트입니다. 이를 사용하면 파일 시스템 및 개체 저장소 패러다임을 모두 사용하여 데이터를 조작할 수 있습니다.

현재 Azure Data Lake Storage Gen1을 사용 중인 경우 Azure Data Factory를 사용하여 Data Lake Storage Gen1에서 Gen2로 데이터를 복사하여 Gen2의 새로운 기능을 평가할 수 있습니다.

Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 분석 솔루션을 빌드할 때 서비스를 사용하여 풍부한 온-프레미스 및 크라우드 기반 데이터 저장소의 데이터로 레이크를 채우고 시간을 절약할 수 있습니다. 지원되는 커넥터의 자세한 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 스케일 아웃, 관리되는 데이터 이동 솔루션을 제공합니다. ADF의 스케일 아웃 아키텍처로 인해 높은 처리량으로 데이터를 수집할 수 있습니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조하세요.

이 문서에서는 Data Factory 데이터 복사 도구를 사용하여 _Azure Data Lake Storage Gen1_에서 _Azure Data Lake Storage Gen2_로 데이터를 복사하는 방법을 보여줍니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터가 있는 Azure Data Lake Storage Gen1 계정
* Data Lake Storage Gen2가 사용하도록 설정된 Azure Storage 계정: 저장소 계정에 없다면 [계정을 만들](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽 메뉴에서 **리소스 만들기** > **분석** > **Data Factory**를 차례로 선택합니다.
   
   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지에서 다음 그림에 표시된 필드의 값을 제공합니다. 
      
   ![새 데이터 팩터리 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **이름**: Azure Data Factory의 전역적으로 고유 이름을 입력합니다. "데이터 팩터리 이름 \"LoadADLSDemo\"를 사용할 수 없습니다" 오류가 발생하면 데이터 팩터리의 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory**라는 이름을 사용할 수 있습니다. 데이터 팩터리를 다시 만들어 봅니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
    * **버전**: **V2**를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 

3. **만들기**를 선택합니다.
4. 만들기가 완료되면 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다. 
   
   ![데이터 팩터리 홈페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 데이터 통합 애플리케이션을 시작합니다.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 데이터 로드

1. **시작** 페이지에서 **데이터 복사** 타일을 선택하여 데이터 복사 도구를 시작합니다. 

   ![데이터 복사 도구 타일](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. **속성** 페이지에서 **작업 이름** 필드를 **CopyFromADLSGen1ToGen2**로 지정하고 **다음**을 선택합니다.

    ![속성 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 **+ 새 연결 만들기**를 클릭합니다.

    ![원본 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    커넥터 갤러리에서 **Azure Data Lake Storage Gen1**을 선택하고, **계속**을 선택합니다.
    
    ![원본 데이터 저장소 Azure Data Lake Storage Gen1 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. **Azure Data Lake Storage Gen1 연결 지정** 페이지에서 다음 단계를 수행합니다.
   1. 계정 이름으로 Data Lake Storage Gen1을 선택하고 **Tenant**를 지정하거나 유효한지 검사합니다.
   2. **연결 테스트**를 클릭하여 설정의 유효성을 검사한 다음, **마침**을 선택합니다.
   3. 새 연결이 생성되었다고 표시됩니다. **다음**을 선택합니다.
   
   > [!IMPORTANT]
   > 이 연습에서는 Azure 리소스용 관리 ID를 사용하여 Data Lake Storage Gen1을 인증합니다. [다음 지침](connector-azure-data-lake-store.md#managed-identity)에 따라 Azure Data Lake Storage Gen1에서 MSI에 적절한 권한을 부여합니다.
   
   ![Azure Data Lake Storage Gen1 계정 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. **입력 파일 또는 폴더 선택** 페이지에서, 복사하려는 폴더 및 파일로 이동합니다. 폴더/파일을 선택하고 **선택**을 선택합니다.

    ![입력 파일 또는 폴더 선택](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. **재귀적으로 파일 복사** 및 **이진 복사** 옵션을 선택하여 복사 동작을 지정합니다. **다음**을 선택합니다.

    ![출력 폴더 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. **대상 데이터 스토리지** 페이지에서 **+ 새 연결 만들기**를 클릭한 다음, **Azure Data Lake Storage Gen2**를 선택하고 **계속**을 선택합니다.

    ![대상 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. **Azure Data Lake Storage Gen2 연결 지정** 페이지에서 다음 단계를 수행합니다.

   1. "스토리지 계정 이름" 드롭다운 목록에서 Data Lake Storage Gen2 계정을 선택합니다.
   2. **마침**을 선택하여 연결을 만듭니다. 그런 후 **다음**을 선택합니다.
   
   ![Azure Data Lake Storage Gen2 계정 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. 에 **출력 파일 또는 폴더 선택** 페이지에서 입력 **copyfromadlsgen1** 출력 폴더 이름 및 선택 **다음**합니다. ADF 만들어집니다 해당 ADLS Gen2 파일 시스템 및 하위 폴더를 복사 하는 동안 존재 하지 않는 경우.

    ![출력 폴더 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. **설정** 페이지에서 **다음**을 선택하여 기본 설정을 사용합니다.

11. **요약** 페이지에서 설정을 검토하고, **다음**을 선택합니다.

    ![요약 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. **배포 페이지**에서 **모니터**를 선택하여 파이프라인을 모니터링합니다.

    ![배포 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다.

    ![파이프라인 실행 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 전환하려면 위쪽의 **파이프라인** 링크를 선택합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다. 

    ![작업 실행 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. 각 복사 작업의 실행 세부 정보를 모니터링하려면 작업 모니터링 보기의 **작업** 아래에서 **세부 정보** 링크(안경 이미지)를 선택합니다. 원본에서 싱크로 복사되는 데이터 볼륨, 데이터 처리량, 해당 기간의 실행 단계, 사용되는 구성 등의 세부 정보를 모니터링할 수 있습니다.

    ![작업 실행 세부 정보 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. 데이터가 Data Lake Storage Gen2 계정에 복사되었는지 확인합니다.

## <a name="best-practices"></a>모범 사례

일반적 Gen2로 업그레이드 Azure Data Lake 저장소 (ADLS) Gen1에서 평가, 참조 [빅 데이터 분석 솔루션을 Azure 데이터 레이크 저장소 Gen1에서 Azure 데이터 레이크 저장소 Gen2로 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)합니다. 다음 섹션에서는 ADF를 사용 하 여 Gen2로 Gen1에서 데이터 업그레이드에 대 한 모범 사례를 소개 합니다.

### <a name="data-partition-for-historical-data-copy"></a>기록 데이터 복사본에 대 한 데이터 파티션

- ADLS Gen1에서 총 데이터 크기의 경우 보다 작은 **30TB** 파일의 수 이며 보다 작거나 **1 백만**, 단일 복사 작업 실행의 모든 데이터를 복사할 수 있습니다.
- 더 큰 크기의 데이터를 복사 하려면 있거나 하는 데이터를 분할 하 제안 된 일괄 처리에서 데이터 마이그레이션을 관리 하 고 각각의 특정 시간 기간 내에 완료 하는 유연성을 원하는 경우 경우에서 줄일 수도 있습니다 모든 예기치 않은 iss의 위험 사용자 교육 합니다.

종단 간 솔루션을 확인 하 고 사용자 환경에서 복사 처리량을 테스트 하기 위해는 PoC (개념 증명)를 사용 하는 것이 좋습니다. PoC를 수행 하는 주요 단계: 

1. ADLS Gen1에서 ADLS Gen2부터 복사 성능 기준선을 가져오려면 몇 Tb의 데이터를 복사 하는 단일 복사 작업을 사용 하 여 하나의 ADF 파이프라인 만들기 [데이터 통합 단위 (DIUs)](copy-activity-performance.md#data-integration-units) 128로 합니다. 
2. 1 단계에서 얻게 복사 처리량을 기준, 전체 데이터 마이그레이션에 필요한 예상된 시간으로 계산 합니다. 
3. (선택 사항) 제어 테이블을 만들고 마이그레이션할 파일을 분할 하는 파일 필터를 정의 합니다. 다음 파일을 분할 하는 방법. 

    - 와일드 카드 필터 (제안)를 사용 하 여 폴더 이름 또는 폴더 이름으로 분했습니다 
    - 파일의 마지막 수정된 시간을 기준으로 분했습니다 

### <a name="network-bandwidth-and-storage-io"></a>네트워크 대역폭 및 저장소 I/O 

마이그레이션 중 ADLS Gen1에서 정상적인 비즈니스 작업에 영향을 주지 하기 위해 저장소 I/O 사용량을 관리할 수 있도록 ADLS Gen1에서 데이터를 읽고 ADLS Gen2로 데이터를 쓰는 ADF 복사 작업의 동시성을 제어할 수 있습니다.

### <a name="permissions"></a>권한 

데이터 팩터리의 [ADLS Gen1 커넥터](connector-azure-data-lake-store.md) Azure 리소스 인증;에 대 한 서비스 주체 및 Id 관리를 지원 합니다. [ADLS Gen2 커넥터](connector-azure-data-lake-storage.md) 지원 계정 키, 서비스 주체 및 Azure 리소스 인증에 대 한 Id 관리 합니다. Data Factory를 이동할 수 및 모든 파일/Acl, 필요에 따라 부여 해야 높은 계정에 대 한 충분 한 권한을 복사 하도록 모든 파일을 액세스/읽기/쓰기를 제공 하 고 하기로 선택한 경우 Acl을 설정 합니다. 마이그레이션 기간 중 super 사용자/소유자 역할을 부여 하는 것이 좋습니다. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1에서 Acl을 유지 합니다.

데이터 파일과 함께 Acl 데이터 레이크 저장소 Gen1에서 Gen2로 업그레이드 하는 경우 복제 하려는 경우 가리킵니다 [유지 Acl 데이터 레이크 저장소 Gen1에서](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)합니다. 

### <a name="incremental-copy"></a>증분 복사 

여러 가지 방법으로 ADLS Gen1에서 새롭거나 업데이트 된 파일만 로드를 사용할 수 있습니다.

- 분할 된 폴더 또는 파일 이름, 예: / 2019/05/13/여 새롭거나 업데이트 된 파일을 로드 *;
- LastModifiedDate;에서 새롭거나 업데이트 된 파일 로드
- 모든 타사 도구/솔루션에 따라 새롭거나 업데이트 된 파일을 식별 한 다음 매개 변수 또는 테이블/파일을 통해 파일 또는 폴더 이름을 ADF 파이프라인에 전달 합니다.  

증분 로드를 위해 적절 한 빈도 ADLS Gen1 파일의 총 수 및 로드할 때마다 새롭거나 업데이트 된 파일의 볼륨에 따라 달라 집니다.  

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [복사 작업 개요](copy-activity-overview.md)
> [Azure Data Lake 저장소 Gen1 커넥터](connector-azure-data-lake-store.md)
> [Azure Data Lake 저장소 Gen2 커넥터](connector-azure-data-lake-storage.md)