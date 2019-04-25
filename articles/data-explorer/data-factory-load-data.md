---
title: Azure 데이터 탐색기로 Azure Data Factory에서 데이터 복사
description: 이 항목에서는 Azure Data Factory 복사 도구를 사용 하 여 Azure 데이터 탐색기를 (부하) 데이터 수집 방법
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 64856d53168a7676cf279da2d8675ce81e1985f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60447877"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure 데이터 탐색기로 데이터 복사 

Azure 데이터 탐색기는 대용량 IoT 장치, 응용 프로그램 및 웹 사이트 등 여러 소스에서 스트리밍 데이터에서 실시간 분석을 위해 빠르고 완전히 관리 되는 데이터 분석 서비스입니다. 반복적으로 데이터를 탐색 하 고 패턴 및 제품을 개선, 고객 환경을 개선에 대 한 오류를 식별 합니다. 장치를 모니터링 하 고 작업을 향상 합니다. 새로운 질문을 살펴보고, 단 몇 분 만에 답변을 확인할 수 있습니다. Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 서비스를 사용 하 여 기존 시스템에서 Azure 데이터 탐색기 데이터베이스를 데이터로 채우면 시간을 절약할를 분석 솔루션을 빌드할 때입니다.

Azure Data Factory는 Azure 데이터 탐색기로 데이터를 로드 하기 위한 다음과 같은 이점을 제공 합니다.

* **간편한 설정**: 스크립팅이 필요 없는 직관적인 5 단계 마법사.
* **다양 한 데이터 저장소 지원**: 다양 한 온-프레미스 및 클라우드 기반 데이터 저장소에 대 한 기본 제공 지원 합니다. 자세한 목록은 [지원되는 데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 표를 참조하세요.
* **보안 및 규정 준수**: 데이터가는 HTTPS 또는 ExpressRoute를 통해 전송 됩니다. 글로벌 서비스가 제공되므로 데이터가 지리적 경계를 벗어나지 않습니다.
* **고성능**: 최대 1 GB/s 데이터 Azure 데이터 탐색기로 로드 속도입니다. 자세한 내용은 [복사 작업 성능](/azure/data-factory/copy-activity-performance)을 참조하세요.

이 문서에서는 Amazon S3에서 Azure 데이터 탐색기로 데이터를 로드 하려면 Data Factory 데이터 복사 도구를 사용 하는 방법을 보여 줍니다. 와 같은 다른 데이터 저장소에서 데이터를 복사 하는 유사한 단계를 따르면 [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)하십시오 [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery)하십시오[Oracle](/azure/data-factory/connector-oracle), 및 [파일 시스템](/azure/data-factory/connector-file-system)입니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스를](create-cluster-database-portal.md)
* 데이터 원본입니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 선택 된 **리소스 만들기** 포털의 왼쪽 위 모서리에 있는 단추 (+) > **Analytics** > **Data Factory**합니다.

   ![새 데이터 팩터리 만들기](media/data-factory-load-data/create-adf.png)

1. 에 **새 데이터 팩터리** 페이지에서 다음 필드에 대 한 값을 제공 하 고 선택한 **만들기**합니다.

    ![새 데이터 팩터리 페이지](media/data-factory-load-data/my-new-data-factory.png)

    **설정**  | **필드 설명**
    |---|---|
    | **Name** | 데이터 팩터리의 전역적으로 고유한 이름을 입력 합니다. 오류가 발생 하는 경우 *"데이터 팩터리 이름 \"LoadADXDemo\" 를 사용할 수 없습니다"* 를 데이터 팩터리에 대해 다른 이름을 입력 합니다. Data Factory 아티팩트 명명 규칙에 대 한 참조 [Data Factory 명명 규칙](/azure/data-factory/naming-rules)합니다.|
    | **구독** | 데이터 팩터리를 만들 Azure 구독을 선택합니다. |
    | **리소스 그룹** | 선택 **새로 만들기** 새 리소스 그룹의 이름을 입력 합니다. 선택 **기존 항목 사용**기존 리소스 그룹이 있는 경우. |
    | **버전** | **V2**를 선택합니다. |
    | **위치**: | 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용 되는 데이터 저장소는 다른 위치 또는 지역 수 있습니다. |
    | | |

1. 만들기 프로세스를 모니터링 하려면 도구 모음에서 알림을 선택 합니다. 만들기가 완료 되 면 사용자가 만든 데이터 팩터리로 이동 합니다. 합니다 **Data Factory** 홈 페이지가 열립니다.

   ![데이터 팩터리 홈페이지](media/data-factory-load-data/data-factory-home-page.png)

1. 선택 된 **작성 및 모니터링** 타일을 별도 탭에서 응용 프로그램을 시작 합니다.

## <a name="load-data-into-azure-data-explorer"></a>Azure 데이터 탐색기로 데이터 로드

다양 한 유형의 데이터를 로드할 수 있습니다 [데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) Azure 데이터 탐색기로 합니다. 이 항목에서는 Amazon S3에서 데이터를 로드 합니다.

두 가지 방법으로 Azure Data Factory를 사용 하 여 Azure 데이터 탐색기로 데이터를 로드할 수 있습니다.

* Azure Data Factory 사용자 인터페이스- [ **작성자** 탭](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory **데이터 복사** 도구](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) 이 문서에 사용 합니다.

### <a name="copy-data-from-amazon-s3-source"></a>Amazon S3에서 데이터 복사 (원본)

1. 에 **시작 해 보겠습니다** 페이지에서 합니다 **데이터 복사** 데이터 복사 도구를 시작 하려면 타일입니다.

   ![데이터 복사 도구 타일](media/data-factory-load-data/copy-data-tool-tile.png)

1. 에 **속성** 페이지에서 지정 **작업 이름** 선택한 **다음**합니다.

    ![원본 속성에서 복사](media/data-factory-load-data/copy-from-source.png)

1. 에 **원본 데이터 저장소** 페이지에서 클릭 **+ 새 연결 만들기**합니다.

    ![원본 데이터 연결 만들기](media/data-factory-load-data/source-create-connection.png)

1. 선택 **Amazon S3**를 선택한 후 **계속**

    ![새 연결된 서비스](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 에 **새 연결 된 서비스 (Amazon S3)** 페이지에서 다음 단계를 수행 합니다.

    ![Amazon S3 연결 된 서비스 지정](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * 지정할 **이름** 새 서비스를 연결 합니다.
    * 선택 **통합 런타임을 통해 연결** 드롭다운 목록에서 값입니다.
    * **액세스 키 ID** 값을 지정합니다.
    * **비밀 액세스 키** 값을 지정합니다.
    * 선택 **연결 테스트** 만든 연결 된 서비스 연결을 테스트 합니다.
    * **마침**을 선택합니다.

1. 에 **원본 데이터 저장소** 페이지에서 새 AmazonS31 연결이 표시 됩니다. **다음**을 선택합니다.

   ![원본 데이터 저장소 만든된 연결](media/data-factory-load-data/source-data-store-created-connection.png)

1. 에 **입력된 파일 또는 폴더 선택** 페이지:

    1. 복사 하려는 폴더/파일을 찾습니다. 폴더/파일을 선택 합니다.
    1. 필요에 따라 복사 동작을 선택 합니다. 유지할 **이진 복사** 선택 취소 합니다.
    1. **다음**을 선택합니다.

    ![입력 파일 또는 폴더 선택](media/data-factory-load-data/source-choose-input-file.png)

1. 에 **파일 형식 설정** 페이지 선택 하 고 파일에 대 한 관련 설정을 **다음**합니다.

   ![입력 파일 또는 폴더 선택](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Azure 데이터 탐색기 (대상)로 데이터 복사

Azure 데이터 탐색기의 새 연결 된 서비스는 아래에 지정 된 Azure 데이터 탐색기 대상 테이블 (싱크)에 데이터를 복사 생성 됩니다.

1. 에 **대상 데이터 저장소** 페이지에서 사용할 수 있는 기존 데이터 저장소 연결 또는 새 데이터 저장소를 클릭 하 여 지정 **+ 새 연결 만들기**합니다.

    ![대상 데이터 저장소 페이지](media/data-factory-load-data/destination-create-connection.png)

1. 에 **새 연결 된 서비스** 페이지에서 **Azure Data Explorer**를 선택한 후 **계속**

    ![Azure 데이터 탐색기-새 연결 된 서비스를 선택 합니다.](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 에 **새 연결 된 서비스 (Azure Data Explorer)** 페이지에서 다음 단계를 수행 합니다.

    ![ADX 새 연결 된 서비스](media/data-factory-load-data/adx-new-linked-service.png)

   * 선택 **이름을** 연결 된 서비스를 Azure 데이터 탐색기입니다.
   * **계정 선택 방법**: 
        * 선택 된 **에서 Azure 구독** 라디오 단추를 선택 하 **Azure 구독** 계정. 다음을 선택 하면 **클러스터**합니다. 참고 드롭다운 목록에는 사용자에 게 속한 목록 클러스터만 됩니다.
        * 또는 선택할 **수동으로 입력** 라디오 단추를 입력 하 **끝점**.
    * 지정 된 **테 넌 트**합니다.
    * 입력 **서비스 주체 ID**합니다.
    * 선택 **서비스 주체 키** enter 단추 **서비스 주체 키**합니다.
    * 선택 하면 **데이터베이스** 드롭다운 메뉴에서. 또는 선택 **편집** 확인란을 선택 하 고 데이터베이스 이름을 입력 합니다.
    * 선택 **연결 테스트** 만든 연결 된 서비스 연결을 테스트 합니다. 경우에 녹색 확인 표시가 설치를 연결할 수 있습니다 **연결 성공** 나타납니다.
    * 선택 **완료** 연결 된 서비스 만들기를 완료 합니다.

    > [!NOTE]
    > 서비스 주체는 Azure 데이터 탐색기 서비스에 액세스 하려면 Azure Data Factory에서 사용 됩니다. 서비스 주체에 대해 [Azure Active Directory (Azure AD) 서비스 주체 만들기](/azure/azure-stack/azure-stack-create-service-principals#manage-service-principal-for-azure-ad)합니다. 사용 하지 않는 합니다 **Azure Key Vault** 메서드.

1. 합니다 **대상 데이터 저장소** 열립니다. 만든 Azure Data Explorer 데이터 연결을 사용할 수 있습니다. 선택 **다음** 연결을 구성 합니다.

    ![ADX 대상 데이터 저장소](media/data-factory-load-data/destination-data-store.png)

1. **테이블 매핑**대상 테이블 이름을 설정 하 고 선택 **다음**합니다.

    ![대상 데이터 집합 테이블 매핑](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 에 **열 매핑** 페이지:
    * 첫 번째 매핑의 ADF에 의해 수행 됩니다 [ADF 스키마 매핑](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * 설정 된 **열 매핑** Azure Data Factory 대상 테이블에 대 한 합니다. 기본 매핑은 원본에서 ADF 대상 테이블에 표시 됩니다.
        * 열 매핑을 정의할 필요가 없는 열을 선택 취소 합니다.
    * 이 테이블 형식 데이터는 Azure 데이터 탐색기로 수집 하는 경우 두 번째 매핑이 발생 합니다. 매핑은에 따라 수행 됩니다 [CSV 매핑 규칙](/azure/kusto/management/mappings#csv-mapping)합니다. 원본 데이터를 CSV 형식으로 없습니다, 경우에 ADF가 데이터를 테이블 형식으로 변환, CSV 매핑은이 단계에만 관련 매핑을 따라서 note 합니다.
        * 아래 **Azure 데이터 탐색기 (Kusto) 싱크 속성** 관련 추가 **수집 매핑 이름** (선택 사항) 하므로 해당 열 매핑을 사용할 수 있습니다.
        * 하는 경우 **수집 매핑 이름** 에 정의 된 "이름 으로" 매핑 순서 지정 되지 않은 **열 매핑** 섹션 발생 합니다. "이름 으로" 매핑이 실패 하면 Azure 데이터 탐색기 (지도에서 위치 기본값으로) "에서 열 위치" 순서로 데이터를 수집 하려고 합니다.
    * 선택 **다음**

    ![대상 데이터 집합 열 매핑](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. **설정** 페이지에서:
    * 관련 설정 **내결함성 설정과 오류**합니다.
    * **성능 설정**: 적용 사용 준비 되지 않습니다. **고급 설정** 비용 고려 사항을 포함 합니다. 경우에 없는 특정 요구가 그대로 둡니다.
    * **다음**을 선택합니다.

    ![데이터 설정 복사](media/data-factory-load-data/copy-data-settings.png)

1. **요약**에서 설정을 검토 하 고 선택 **다음**합니다.

    ![요약 데이터를 복사 합니다.](media/data-factory-load-data/copy-data-summary.png)

1. 에 **배포 페이지**:
    * 선택 **모니터** 으로 전환 합니다 **모니터** 탭 및 파이프라인 (진행 중, 오류 및 데이터 흐름)의 상태를 확인 합니다.
    * 선택 **파이프라인 편집** 연결 된 서비스, 데이터 집합 및 파이프라인을 편집할 수 있습니다.
    * 선택 **완료** 완전 한 복사본 데이터 작업

    ![배포 페이지](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>다음 단계

* 에 대 한 자세한 합니다 [Azure Data Explorer 커넥터](/azure/data-factory/connector-azure-data-explorer) Azure Data Factory에서.

* 연결 된 서비스, 데이터 집합 및 파이프라인을 편집 하는 방법에 대 한 자세한 정보는 [Data Factory UI](/azure/data-factory/quickstart-create-data-factory-portal)합니다.

* 에 대 한 자세한 [Azure 데이터 탐색기 쿼리](/azure/data-explorer/web-query-data) 데이터를 쿼리 합니다.
