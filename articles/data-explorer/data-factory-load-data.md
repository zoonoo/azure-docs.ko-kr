---
title: Azure Data Factory에서 Azure 데이터 탐색기로 데이터 복사
description: 이 항목에서는 Azure Data Factory 복사 도구를 사용 하 여 데이터를 Azure 데이터 탐색기 수집 (로드) 하는 방법에 대해 알아봅니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: b3bd9b800da4f096639d02c78b718216441621a9
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803975"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure 데이터 탐색기에 데이터 복사 

Azure 데이터 탐색기는 응용 프로그램, 웹 사이트 및 IoT 장치와 같은 많은 원본에서 많은 양의 데이터 스트리밍을 실시간으로 분석 하기 위한 완전히 관리 되는 데이터 분석 서비스입니다. 반복적으로 데이터를 탐색 하 고 패턴과 비정상을 식별 하 여 제품을 개선 하 고, 고객 환경을 개선 하 고, 장치를 모니터링 하 고, 작업을 향상 새로운 질문을 살펴보고, 단 몇 분 만에 답변을 확인할 수 있습니다. Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 서비스를 사용 하 여 Azure 데이터 탐색기 데이터베이스를 기존 시스템의 데이터로 채우고 분석 솔루션을 빌드할 때 시간을 절약할 수 있습니다.

Azure Data Factory는 Azure 데이터 탐색기에 데이터를 로드 하는 다음과 같은 이점을 제공 합니다.

* **간편한 설정**: 스크립팅이 필요 없는 직관적인 5 단계 마법사.
* **다양한 데이터 저장소 지원**: 다양한 온-프레미스 및 클라우드 기반 데이터 저장소에 대한 기본 지원을 제공합니다. 자세한 목록은 [지원되는 데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 표를 참조하세요.
* **보안 및 규정 준수**: 데이터는 HTTPS 또는 ExpressRoute를 통해 전송됩니다. 글로벌 서비스가 제공되므로 데이터가 지리적 경계를 벗어나지 않습니다.
* **고성능**: Azure 데이터 탐색기에 대 한 최대 1gb/s 데이터 로드 속도입니다. 자세한 내용은 [복사 작업 성능](/azure/data-factory/copy-activity-performance)을 참조하세요.

이 문서에서는 Data Factory 데이터 복사 도구를 사용 하 여 Amazon s 3에서 Azure 데이터 탐색기로 데이터를 로드 하는 방법을 보여 줍니다. 비슷한 단계에 따라 [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google 이상 쿼리](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle)및 [파일 시스템과](/azure/data-factory/connector-file-system)같은 다른 데이터 저장소에서 데이터를 복사할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md)
* 데이터 원본입니다.

## <a name="create-a-data-factory"></a>data factory 만들기

1. 포털 > **분석** > **Data Factory**의 왼쪽 위 모서리에 있는 **리소스 만들기** 단추 (+)를 선택 합니다.

   ![새 데이터 팩터리 만들기](media/data-factory-load-data/create-adf.png)

1. **새 데이터 팩터리** 페이지에서 다음 필드에 대 한 값을 입력 한 다음 **만들기**를 선택 합니다.

    ![새 데이터 팩터리 페이지](media/data-factory-load-data/my-new-data-factory.png)

    **설정**  | **필드 설명**
    |---|---|
    | **이름** | 데이터 팩터리에 대 한 전역적으로 고유한 이름을 입력 합니다. *" \"Data factory 이름 loadadxdemo\" 를 사용할 수 없습니다."* 오류가 표시 되 면 데이터 팩터리에 대해 다른 이름을 입력 합니다. Data Factory 아티팩트의 명명 규칙은 [Data Factory 명명 규칙](/azure/data-factory/naming-rules)을 참조 하세요.|
    | **구독** | 데이터 팩터리를 만들 Azure 구독을 선택합니다. |
    | **리소스 그룹** | **새로 만들기** 를 선택 하 고 새 리소스 그룹의 이름을 입력 합니다. 기존 리소스 그룹이 있는 경우 **기존 사용**을 선택 합니다. |
    | **Version** | **V2**를 선택합니다. |
    | **위치** | 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. Data factory에서 사용 하는 데이터 저장소는 다른 위치 또는 지역에 있을 수 있습니다. |
    | | |

1. 만들기 프로세스를 모니터링 하려면 도구 모음에서 알림을 선택 합니다. 만들기가 완료 되 면 만든 데이터 팩터리로 이동 합니다. **Data Factory** 홈 페이지가 열립니다.

   ![데이터 팩터리 홈페이지](media/data-factory-load-data/data-factory-home-page.png)

1. **작성자 & 모니터** 타일을 선택 하 여 별도의 탭에서 응용 프로그램을 시작 합니다.

## <a name="load-data-into-azure-data-explorer"></a>Azure 데이터 탐색기에 데이터 로드

여러 유형의 [데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 에서 Azure 데이터 탐색기로 데이터를 로드할 수 있습니다. 이 항목에서는 Amazon s 3에서 데이터를 로드 하는 방법을 자세히 설명 합니다.

Azure Data Factory를 사용 하 여 Azure 데이터 탐색기에 데이터를 로드 하는 방법에는 두 가지가 있습니다.

* Azure Data Factory 사용자 인터페이스- [ **작성자** 탭](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* 이 문서에서 사용 되는 [Azure Data Factory **데이터 복사** 도구](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) 입니다.

### <a name="copy-data-from-amazon-s3-source"></a>Amazon S3 (원본)에서 데이터 복사

1. **시작** 하기 페이지에서 **데이터 복사** 타일을 선택 하 여 데이터 복사 도구를 시작 합니다.

   ![데이터 복사 도구 타일](media/data-factory-load-data/copy-data-tool-tile.png)

1. **속성** 페이지에서 **작업 이름을** 지정 하 고 **다음**을 선택 합니다.

    ![원본 속성에서 복사](media/data-factory-load-data/copy-from-source.png)

1. **원본 데이터 저장소** 페이지에서 **+ 새 연결 만들기**를 클릭 합니다.

    ![원본 데이터 연결 만들기](media/data-factory-load-data/source-create-connection.png)

1. **Amazon S3**을 선택 하 고 **계속** 을 선택 합니다.

    ![새 연결된 서비스](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. **새 연결 된 서비스 (Amazon S3)** 페이지에서 다음 단계를 수행 합니다.

    ![Amazon S3 연결 된 서비스 지정](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * 새 연결 된 서비스의 **이름을** 지정 합니다.
    * 드롭다운에서 **통합 런타임 값을 통해 연결** 을 선택 합니다.
    * **액세스 키 ID** 값을 지정합니다.
    * **비밀 액세스 키** 값을 지정합니다.
    * **연결 테스트** 를 선택 하 여 만든 연결 된 서비스 연결을 테스트 합니다.
    * **마침**을 선택합니다.

1. **원본 데이터 저장소** 페이지에 새 AmazonS31 연결이 표시 됩니다. **다음**을 선택합니다.

   ![원본 데이터 저장소에서 연결을 만듦](media/data-factory-load-data/source-data-store-created-connection.png)

1. **입력 파일 또는 폴더 선택** 페이지에서 다음을 수행 합니다.

    1. 복사 하려는 폴더/파일을 찾습니다. 폴더/파일을 선택 합니다.
    1. 필요에 따라 복사 동작을 선택 합니다. **이진 복사** 를 선택 하지 않은 상태로 유지 합니다.
    1. **다음**을 선택합니다.

    ![입력 파일 또는 폴더 선택](media/data-factory-load-data/source-choose-input-file.png)

1. **파일 형식 설정** 페이지에서 파일에 대 한 관련 설정을 선택 하 고 **다음**을 클릭 합니다.

   ![입력 파일 또는 폴더 선택](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Azure 데이터 탐색기 (대상)에 데이터 복사

Azure 데이터 탐색기 새 연결 된 서비스를 만들어 아래 지정 된 Azure 데이터 탐색기 대상 테이블 (싱크)에 데이터를 복사 합니다.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Azure 데이터 탐색기 연결 된 서비스 만들기

1. **대상 데이터 저장소** 페이지에서 기존 데이터 저장소 연결을 사용 하거나 **+ 새 연결 만들기**를 클릭 하 여 새 데이터 저장소를 지정할 수 있습니다.

    ![대상 데이터 저장소 페이지](media/data-factory-load-data/destination-create-connection.png)

1. **새 연결 된 서비스** 페이지에서 **Azure 데이터 탐색기**를 선택 하 고 **계속** 을 선택 합니다.

    ![Azure 데이터 탐색기-새 연결 된 서비스 선택](media/data-factory-load-data/adx-select-new-linked-service.png)

1. **새 연결 된 서비스 (Azure 데이터 탐색기)** 페이지에서 다음 단계를 수행 합니다.

    ![ADX 새 연결 된 서비스](media/data-factory-load-data/adx-new-linked-service.png)

   * Azure 데이터 탐색기 연결 된 서비스의 **이름을** 선택 합니다.
   * **계정 선택 방법**에서 다음을 수행 합니다. 
        * **Azure 구독에서** 라디오 단추를 선택 하 고 **azure 구독** 계정을 선택 합니다. 그런 다음 **클러스터**를 선택 합니다. 드롭다운은 사용자에 속하는 클러스터만 나열 합니다.
        * 또는 **수동으로 입력** 라디오 단추를 선택 하 고 **끝점**을 입력 합니다.
    * **테 넌 트**를 지정 합니다.
    * **서비스 사용자 ID**를 입력 합니다.
    * **서비스 사용자 키** 단추를 선택 하 고 **서비스 사용자 키**를 입력 합니다.
    * 드롭다운 메뉴에서 **데이터베이스** 를 선택 합니다. 또는 **편집** 확인란을 선택 하 고 데이터베이스 이름을 입력 합니다.
    * **연결 테스트** 를 선택 하 여 만든 연결 된 서비스 연결을 테스트 합니다. 설치 프로그램에 연결할 수 있는 경우 녹색 확인 표시 **연결 성공** 이 표시 됩니다.
    * **마침** 을 선택 하 여 연결 된 서비스 만들기를 완료 합니다.

    > [!NOTE]
    > 서비스 주체는 Azure Data Factory에서 Azure 데이터 탐색기 서비스에 액세스 하는 데 사용 됩니다. 서비스 사용자의 경우 [Azure Active Directory (AZURE AD) 서비스 주체를 만듭니다](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). **Azure Key Vault** 메서드를 사용 하지 마십시오.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Azure 데이터 탐색기 데이터 연결 구성

1. **대상 데이터 저장소가** 열립니다. 만든 Azure 데이터 탐색기 데이터 연결을 사용할 수 있습니다. **다음** 을 선택 하 여 연결을 구성 합니다.

    ![ADX 대상 데이터 저장소](media/data-factory-load-data/destination-data-store.png)

1. **테이블 매핑**에서 대상 테이블 이름을 설정 하 고 **다음**을 선택 합니다.

    ![대상 데이터 집합 테이블 매핑](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. **열 매핑** 페이지에서 다음을 수행 합니다.
    * 첫 번째 매핑은 adf [스키마 매핑에](/azure/data-factory/copy-activity-schema-and-type-mapping) 따라 adf에 의해 수행 됩니다.
        * Azure Data Factory 대상 테이블에 대 한 **열 매핑을** 설정 합니다. 기본 매핑은 원본에서 ADF 대상 테이블에 표시 됩니다.
        * 열 매핑을 정의 하지 않아도 되는 열을 선택 취소 합니다.
    * 두 번째 매핑은이 테이블 형식 데이터를 Azure 데이터 탐색기로 수집 때 발생 합니다. 매핑은 [CSV 매핑 규칙](/azure/kusto/management/mappings#csv-mapping)에 따라 수행 됩니다. 원본 데이터가 CSV 형식이 아닌 경우에도 ADF는 데이터를 테이블 형식으로 변환 했으므로이 단계에서 CSV 매핑이 유일한 관련 매핑입니다.
        * **Azure 데이터 탐색기 (Kusto) 싱크 속성** 에서 열 매핑을 사용할 수 있도록 관련 된 수집 **매핑 이름** (선택 사항)을 추가 합니다.
        * 수집 **매핑 이름을** 지정 하지 않으면 **열 매핑** 섹션에서 정의한 "by name" 매핑 순서가 발생 합니다. "이름별" 매핑이 실패 하는 경우 Azure 데이터 탐색기는 "열 기준 위치" 순서 (기본 위치로 맵)로 데이터를 수집 하려고 합니다.
    * **다음**을 선택합니다.

    ![대상 데이터 집합 열 매핑](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. **설정** 페이지에서:
    * 관련 **내결함성 설정을**설정 합니다.
    * **성능 설정**: 준비 사용은 적용 되지 않습니다. **고급 설정** 에는 비용 고려 사항이 포함 됩니다. 특별 한 요구 사항이 없는 경우에는 그대로 둡니다.
    * **다음**을 선택합니다.

    ![데이터 설정 복사](media/data-factory-load-data/copy-data-settings.png)

1. **요약**에서 설정을 검토 하 고 **다음**을 선택 합니다.

    ![데이터 복사 요약](media/data-factory-load-data/copy-data-summary.png)

1. **배포 페이지**에서 다음을 수행 합니다.
    * 모니터 **를 선택 하** 여 **모니터** 탭으로 전환 하 고 파이프라인 상태 (진행률, 오류 및 데이터 흐름)를 확인 합니다.
    * **파이프라인 편집** 을 선택 하 여 연결 된 서비스, 데이터 집합 및 파이프라인을 편집 합니다.
    * **마침** 을 선택 하 여 데이터 복사 작업 완료

    ![배포 페이지](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>다음 단계

* Azure Data Factory의 [Azure 데이터 탐색기 커넥터](/azure/data-factory/connector-azure-data-explorer) 에 대해 알아봅니다.

* [DATA FACTORY UI](/azure/data-factory/quickstart-create-data-factory-portal)에서 연결 된 서비스, 데이터 집합 및 파이프라인을 편집 하는 방법에 대해 자세히 알아보세요.

* 데이터 쿼리를 위한 [Azure 데이터 탐색기 쿼리](/azure/data-explorer/web-query-data) 에 대해 알아봅니다.
