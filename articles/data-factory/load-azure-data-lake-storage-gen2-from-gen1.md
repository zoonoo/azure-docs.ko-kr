---
title: Azure 데이터 레이크 스토리지 Gen1에서 Gen2로 데이터 복사
description: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사
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
ms.date: 05/13/2019
ms.openlocfilehash: ea0a9de5dde02b45ae34e3e98e24b31fd01fa713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235818"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사

Azure 데이터 레이크 저장소 Gen2Azure [Blob 저장소에](../storage/blobs/storage-blobs-introduction.md)내장된 빅 데이터 분석에 전념하는 기능 집합입니다. 파일 시스템과 개체 저장소 패러다임을 모두 사용하여 데이터와 인터페이스하는 데 사용할 수 있습니다.

현재 Azure Data Lake Storage Gen1을 사용하는 경우 Azure 데이터 팩터리를 사용하여 데이터 레이크 저장소 Gen1에서 Gen2로 데이터를 복사하여 Azure 데이터 레이크 저장소 Gen2를 평가할 수 있습니다.

Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 이 서비스를 사용하여 풍부한 온-프레미스 및 클라우드 기반 데이터 저장소의 데이터로 호수를 채우고 분석 솔루션을 구축할 때 시간을 절약할 수 있습니다. 지원되는 커넥터 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)의 표를 참조하십시오.

Azure Data Factory는 스케일 아웃, 관리되는 데이터 이동 솔루션을 제공합니다. 데이터 팩터리의 확장 아키텍처로 인해 높은 처리량으로 데이터를 수집할 수 있습니다. 자세한 내용은 [활동 복사 성능](copy-activity-performance.md)을 참조하십시오.

이 문서에서는 데이터 팩터리 복사 데이터 도구를 사용하여 Azure Data Lake Storage Gen1의 데이터를 Azure Data Lake Storage Gen2로 복사하는 방법을 보여 주며, 이 문서에서는 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터가 있는 Azure Data Lake Storage Gen1 계정
* 데이터 레이크 저장소 Gen2를 사용하도록 설정한 Azure 저장소 계정입니다. 저장소 계정이 없는 경우 계정을 [만듭니다.](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 리소스 > **데이터 만들기 + 분석** > **데이터 팩터리** **만들기를**선택합니다.
   
   ![새 창의 데이터 팩터리 선택](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. 새 **데이터 팩터리** 페이지에서 다음 이미지에 표시된 필드에 대한 값을 제공합니다. 
      
   ![새 데이터 팩터리 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **이름**: Azure 데이터 팩터리의 전역 고유 이름을 입력합니다. "데이터 팩터리 이름 \"LoadADLSDemo\"를 사용할 수 없습니다" 오류가 발생하면 데이터 팩터리의 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory**를 사용합니다. 데이터 팩터리를 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. **새 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력할 수도 있습니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
    * **버전**: **V2**를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 

3. **만들기**를 선택합니다.
4. 생성이 완료되면 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다. 
   
   ![데이터 팩터리 홈페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. 모니터 **작성자 &** 선택하여 별도의 탭에서 데이터 통합 응용 프로그램을 시작합니다.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 데이터 로드

1. 시작 **하기** 페이지에서 데이터 **복사** 타일을 선택하여 복사 데이터 도구를 시작합니다. 

   ![데이터 도구 타일 복사](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. **속성** 페이지에서 **작업 이름** 필드에 대해 **CopyFromADLSGen1ToGen2를** 지정합니다. **다음**을 선택합니다.

    ![속성 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. 소스 **데이터 저장소** 페이지에서 **+ 새 연결 만들기를**선택합니다.

    ![원본 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. 커넥터 갤러리에서 **Azure Data Lake 저장소 Gen1을** 선택하고 **계속을**선택합니다.
    
    ![원본 데이터 저장소 Azure Data Lake Storage Gen1 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Azure **데이터 레이크 저장소 Gen1 연결 지정** 페이지에서 다음 단계를 수행합니다.

   a. 계정 이름으로 Data Lake Storage Gen1을 선택하고 **Tenant**를 지정하거나 유효한지 검사합니다.
  
   b. 설정의 유효성을 검사하려면 **테스트 연결을** 선택합니다. 그런 다음, **마침**을 선택합니다.
  
   다. 새 연결이 만들어졌습니다. **다음**을 선택합니다.
   
   > [!IMPORTANT]
   > 이 연습에서는 Azure 리소스에 대해 관리되는 ID를 사용하여 Azure Data Lake Storage Gen1을 인증합니다. Azure Data Lake Storage Gen1에서 관리되는 ID에 적절한 권한을 부여하려면 [다음 지침을](connector-azure-data-lake-store.md#managed-identity)따르십시오.
   
   ![Azure Data Lake Storage Gen1 계정 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. 입력 **파일 또는 폴더** 선택 페이지에서 복사할 폴더와 파일을 찾아봅습니다. 폴더 또는 파일을 선택하고 **을**선택합니다.

    ![입력 파일 또는 폴더 선택](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. 복사 파일 재귀 및 **이진 복사** 옵션을 선택하여 복사 **동작을 지정합니다.** **다음**을 선택합니다.

    ![출력 폴더 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. 대상 **데이터 저장소** 페이지에서 **+ 새 연결** > 만들기**Azure Data Lake 저장소 Gen2** > **계속을**선택합니다.

    ![대상 데이터 저장소 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Azure **데이터 레이크 저장소 Gen2 연결 지정** 페이지에서 다음 단계를 수행합니다.

   a. **저장소 계정 이름** 드롭다운 목록에서 데이터 레이크 스토리지 Gen2 지원 계정을 선택합니다.
   
   b. **마침**을 선택하여 연결을 만듭니다. 그런 다음 을 **선택합니다.**
   
   ![Azure Data Lake Storage Gen2 계정 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. 출력 **파일 또는 폴더 선택** 페이지에서 **copyfromadlsgen1을** 출력 폴더 이름으로 입력하고 **다음을 선택합니다.** 데이터 팩터리는 해당 Azure Data Lake Storage Gen2 파일 시스템과 하위 폴더가 존재하지 않는 경우 복사 중에 하위 폴더를 만듭니다.

    ![출력 폴더 지정](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. **설정** 페이지에서 **다음**을 선택하여 기본 설정을 사용합니다.

12. **요약** 페이지에서 설정을 검토하고 **다음**을 선택합니다.

    ![요약 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. 배포 **페이지에서**파이프라인을 모니터링하려면 **모니터를** 선택합니다.

    ![배포 페이지](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다.

    ![파이프라인 실행 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 전환하려면 위쪽의 **파이프라인** 링크를 선택합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다. 

    ![작업 실행 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. 각 복사 작업의 실행 세부 정보를 모니터링하려면 작업 모니터링 보기의 **작업** 아래에서 **세부 정보** 링크(안경 이미지)를 선택합니다. 원본에서 싱크로 복사된 데이터 볼륨, 데이터 처리량, 해당 기간이 있는 실행 단계 및 사용된 구성과 같은 세부 정보를 모니터링할 수 있습니다.

    ![작업 실행 세부 정보 모니터링](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. 데이터가 Azure Data Lake Storage Gen2 계정에 복사되었는지 확인합니다.

## <a name="best-practices"></a>모범 사례

일반적으로 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로업그레이드를 평가하려면 [Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 빅 데이터 분석 솔루션 업그레이드를](../storage/blobs/data-lake-storage-upgrade.md)참조하세요. 다음 섹션에서는 데이터 레이크 스토리지 Gen1에서 데이터 레이크 스토리지 Gen2로 데이터 업그레이드를 위해 데이터 팩터리를 사용하는 모범 사례를 소개합니다.

### <a name="data-partition-for-historical-data-copy"></a>기록 데이터 복사본에 대한 데이터 파티션

- Data Lake Storage Gen1의 총 데이터 크기가 30TB 미만이고 파일 수가 1백만 개 미만인 경우 단일 복사 활동 실행에서 모든 데이터를 복사할 수 있습니다.
- 복사할 데이터가 더 많은 경우 또는 일괄 처리로 데이터 마이그레이션을 관리하고 특정 시간 프레임 내에 데이터 마이그레이션을 완료할 수 있는 유연성을 원하는 경우 데이터를 분할합니다. 분할은 예기치 않은 문제의 위험을 줄입니다.

개념 증명을 사용하여 종단 간 솔루션을 확인하고 사용자 환경에서 복사 처리량을 테스트합니다. 주요 개념 증명 단계: 

1. 단일 복사 활동이 있는 하나의 데이터 팩터리 파이프라인을 만들어 데이터 레이크 저장소 Gen1에서 데이터 레이크 저장소 Gen2에 여러 개의 데이터를 복사하여 복사 성능 기준을 얻습니다. 데이터 [통합 단위(DIUs)를](copy-activity-performance.md#data-integration-units) 128로 시작합니다. 
2. 1단계에서 얻는 복사 처리량을 기반으로 전체 데이터 마이그레이션에 필요한 예상 시간을 계산합니다. 
3. (선택 사항) 제어 테이블을 만들고 마이그레이션할 파일을 분할할 파일 필터를 정의합니다. 파일을 분할하는 방법은 다음과 같은 것입니다. 

    - 와일드카드 필터가 있는 폴더 이름 또는 폴더 이름으로 분할합니다. 연결합니다(권장).
    - 파일의 마지막으로 수정된 시간으로 파티션합니다.

### <a name="network-bandwidth-and-storage-io"></a>네트워크 대역폭 및 스토리지 I/O 

데이터 레이크 스토리지 Gen1에서 데이터를 읽고 데이터를 Data Lake Storage Gen2에 쓰는 데이터 팩터리 복사 작업의 동시성을 제어할 수 있습니다. 이러한 방식으로 마이그레이션 하는 동안 Data Lake 저장소 Gen1의 정상적인 비즈니스 작업에 영향을 주지 않도록 해당 저장소 I/O에서 사용을 관리할 수 있습니다.

### <a name="permissions"></a>사용 권한 

데이터 팩터리에서 [Data Lake Storage Gen1 커넥터는](connector-azure-data-lake-store.md) Azure 리소스 인증에 대한 서비스 주체 및 관리되는 ID를 지원합니다. [Data Lake Storage Gen2 커넥터는](connector-azure-data-lake-storage.md) Azure 리소스 인증에 대한 계정 키, 서비스 주체 및 관리되는 ID를 지원합니다. Data Factory에서 필요한 모든 파일 또는 액세스 제어 목록(ACL)을 탐색하고 복사할 수 있도록 하려면 원하는 경우 모든 파일에 액세스, 읽기 또는 쓰기를 위해 제공한 계정에 대해 충분한 권한을 부여하고 ACL을 설정할 수 있습니다. 마이그레이션 기간 동안 수퍼 사용자 또는 소유자 역할을 부여합니다. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>데이터 레이크 스토리지 Gen1에서 ACL 보존

데이터 레이크 스토리지 Gen1에서 데이터 레이크 스토리지 Gen2로 업그레이드할 때 데이터 파일과 함께 ACL을 복제하려면 [데이터 레이크 스토리지 Gen1의 ACL 보존을](connector-azure-data-lake-storage.md#preserve-acls)참조하십시오. 

### <a name="incremental-copy"></a>증분 복사 

여러 가지 방법을 사용하여 Data Lake Storage Gen1의 새 파일또는 업데이트된 파일만 로드할 수 있습니다.

- 분할된 폴더 또는 파일 이름으로 새 파일또는 업데이트된 파일을 로드합니다. 예를 들어 /2019/05/13/*입니다.
- LastModifiedDate에서 새 파일또는 업데이트된 파일을 로드합니다.
- 타사 도구 또는 솔루션별로 새 파일또는 업데이트된 파일을 식별합니다. 그런 다음 매개 변수 또는 테이블 또는 파일을 통해 파일 또는 폴더 이름을 Data Factory 파이프라인에 전달합니다. 

증분 로드를 수행하는 적절한 빈도는 Azure Data Lake Storage Gen1의 총 파일 수와 매번 로드할 새 파일 또는 업데이트된 파일의 양에 따라 달라집니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [복사 활동 개요](copy-activity-overview.md)
> [Azure 데이터 호수 저장소 Gen1 커넥터](connector-azure-data-lake-store.md)
> [Azure 데이터 호수 저장소 Gen2 커넥터](connector-azure-data-lake-storage.md)