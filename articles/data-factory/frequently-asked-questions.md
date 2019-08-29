---
title: 'Azure Data Factory: 질문과 대답 | Microsoft 문서'
description: Azure Data Factory에 대한 질문과 대답입니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: c4836d519556e5a031f81279fef4891ba8d47c05
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141580"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory FAQ
이 아티클에서는 Azure Data Factory에 대한 질문과 대답을 제공합니다.  

## <a name="what-is-azure-data-factory"></a>Azure 데이터 팩터리란 무엇인가요? 
Data Factory는 데이터의 이동과 변환을 자동화하는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 장비를 작동하여 원자재를 완제품으로 변형하는 공장처럼 Azure Data Factory는 원시 데이터를 수집하여 바로 사용할 수 있는 정보로 변환하는 기존 서비스를 오케스트레이션합니다. 

Azure Data Factory를 사용하여 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동하는 데이터 기반 워크플로를 만들 수 있습니다. Azure HDInsight, Azure Data Lake Analytics 및 SQL Server Integration Services (SSIS) Integration runtime과 같은 계산 서비스를 사용 하 여 데이터를 처리 하 고 변환할 수 있습니다. 

Data Factory를 사용하여 Azure 기반 클라우드 서비스에서 데이터 처리를 실행하거나 SSIS, SQL Server 및 Oracle과 같은 자체 호스팅 컴퓨팅 환경을 활용할 수 있습니다. 필요한 작업을 수행 하는 파이프라인을 만든 후 정기적 (예: 매시간, 매일 또는 매주), 시간 창 일정에 따라 실행 되도록 예약 하거나 이벤트 발생 시 파이프라인을 트리거할 수 있습니다. 자세한 내용은 [Azure Data Factory 소개](introduction.md)를 참조하세요.

### <a name="control-flows-and-scale"></a>제어 흐름 및 크기 조정 
최신 데이터 웨어하우스의 다양 한 통합 흐름 및 패턴을 지원 하기 위해 Data Factory는 유연한 데이터 파이프라인 모델링을 가능 하 게 합니다. 여기에는 조건부 실행, 데이터 파이프라인의 분기, 이러한 흐름 내에서 그리고 이러한 흐름 간에 명시적으로 매개 변수를 전달 하는 기능을 포함 하는 모든 제어 흐름 프로그래밍 패러다임이 수반 됩니다. 또한 제어 흐름은 복사 작업을 통해 작업 디스패치를 통해 데이터를 규모에 따라 데이터 이동을 포함 하는 외부 실행 엔진 및 데이터 흐름 기능으로 변환 합니다.

Data Factory는 데이터 통합에 필요하고 요청 시 또는 일정에 따라 반복해서 디스패치할 수 있는 모든 흐름 스타일을 자유롭게 모델링하도록 합니다. 이 모델이 사용할 수 있는 일반적인 흐름은 다음과 같습니다.   

- 제어 흐름:
    - 활동은 파이프라인 내의 순서로 함께 연결 될 수 있습니다.
    - 활동은 파이프라인 내에서 분기할 수 있습니다.
    - 매개 변수
        - 파이프라인 수준에서 매개 변수를 정의할 수 있으며 요청 시 또는 트리거에서 파이프라인을 호출 하는 동안 인수를 전달할 수 있습니다.
        - 파이프라인에 전달되는 인수를 작업에 사용할 수 있습니다.
    - 사용자 지정 상태 전달:
        - 상태를 포함 한 작업 출력은 파이프라인의 후속 작업에서 사용 될 수 있습니다.
    - 반복 컨테이너:
        - Foreach 활동은 루프의 지정 된 활동 컬렉션을 반복 합니다. 
- 트리거 기반 흐름:
    - 요청 시 또는 벽시계 시간에 따라 파이프라인을 트리거할 수 있습니다.
- 델타 흐름:
    - 매개 변수를 사용 하면 관계형 저장소 (온-프레미스 또는 클라우드)에서 차원 또는 참조 테이블을 이동 하 여 데이터를 lake로 로드 하는 동안 델타 복사에 대 한 최고 수 위 표시를 정의할 수 있습니다. 

자세한 내용은 [자습서: 제어 흐름](tutorial-control-flow.md)을 참조하세요.

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>코드 없는 파이프라인을 사용 하 여 대규모로 변환 된 데이터
새로운 브라우저 기반 도구 환경을 통해 코드 제한 없이 파이프라인을 작성하고 최신 대화형 웹 기반 환경에서 배포할 수 있습니다.

Visual 데이터 개발자 및 데이터 엔지니어를 위해 Data Factory 웹 UI는 파이프라인을 빌드하는 데 사용할 수 있는 코드 없는 디자인 환경입니다. Visual Studio Online Git과 완벽 하 게 통합 되며, 디버깅 옵션을 사용 하 여 CI/CD 및 반복적인 개발을 위한 통합 기능을 제공 합니다.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>고급 사용자를 위한 다양 한 플랫폼 간 Sdk
Data Factory V2는 선호 하는 IDE를 사용 하 여 파이프라인을 작성, 관리 및 모니터링 하는 데 사용할 수 있는 풍부한 Sdk 집합을 제공 합니다.
* Python SDK
* PowerShell CLI
* C# SDK

사용자는 문서화 된 REST Api를 사용 하 여 Data Factory V2와 인터페이스 할 수도 있습니다.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>시각적 도구를 사용 하 여 반복적인 개발 및 디버깅
Azure Data Factory 비주얼 도구를 사용 하면 반복적인 개발 및 디버깅이 가능 합니다. 파이프라인을 만들고 한 줄의 코드를 작성 하지 않고 파이프라인 캔버스에서 **디버그** 기능을 사용 하 여 테스트 실행을 수행할 수 있습니다. 파이프라인 캔버스의 **출력** 창에서 테스트 실행 결과를 볼 수 있습니다. 테스트를 성공적으로 실행 한 후에는 파이프라인에 더 많은 활동을 추가 하 고 반복적으로 디버깅을 계속할 수 있습니다. 또한 진행 중인 테스트 실행을 취소할 수 있습니다. 

**디버그**를 선택 하기 전에 데이터 팩터리 서비스에 변경 내용을 게시할 필요가 없습니다. 이 기능은 개발, 테스트 또는 프로덕션 환경에서 데이터 팩터리 워크플로를 업데이트 하기 전에 새로운 추가 또는 변경 내용이 예상 대로 작동 하는지 확인 하려는 경우에 유용 합니다. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Azure에 SSIS 패키지를 배포 하는 기능 
SSIS 워크로드를 이동하려는 경우 Data Factory를 만들고 Azure-SSIS Integration Runtime을 프로비전할 수 있습니다. Azure SSIS integration runtime은 클라우드에서 SSIS 패키지를 실행 하기 위한 전용 Azure Vm (노드)의 완전히 관리 되는 클러스터입니다. 단계별 지침은 [Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md) 자습서를 참조하세요. 
 
### <a name="sdks"></a>SDK
고급 사용자로 서 프로그래밍 인터페이스를 찾고 있는 경우 즐겨 사용 하는 IDE를 사용 하 여 파이프라인을 작성, 관리 또는 모니터링 하는 데 사용할 수 있는 다양 한 Sdk를 Data Factory 제공 합니다. .NET, PowerShell, Python 및 REST를 비롯한 언어를 지원합니다.

### <a name="monitoring"></a>모니터링
브라우저 사용자 인터페이스에서 PowerShell, SDK 또는 시각적 개체 모니터링 도구를 통해 Data Factories를 모니터링할 수 있습니다. 효율적이 고 효과적인 방식으로 주문형, 트리거 기반 및 클록 기반 사용자 지정 흐름을 모니터링 하 고 관리할 수 있습니다. 기존 태스크를 취소 하 고, 한 눈에 실패를 확인 하 고, 드릴 다운 하 여 자세한 오류 메시지를 확인 하 고, 문제를 디버그 합니다. 

### <a name="new-features-for-ssis-in-data-factory"></a>Data Factory의 SSIS에 대 한 새로운 기능
2017 Data Factory의 초기 공개 미리 보기 릴리스는 SSIS에 대해 다음과 같은 기능을 추가 했습니다.

-   프로젝트/패키지의 SSISDB (SSIS 데이터베이스)를 호스트 하는 Azure SQL Database의 세 가지 구성/변형 지원:
-   가상 네트워크 서비스 끝점을 사용 하 여 SQL Database
-   Managed Instance
-   Elastic Pool
-   Azure SSIS 통합 런타임을 가상 네트워크 서비스를 사용 하 여 SQL Database에 대해 구성 된 가상 네트워크에 삽입/조인할 수 있는 기존 가상 네트워크를 기반으로 하는 Azure Resource Manager 가상 네트워크에 대 한 지원은 나중에 사용 되지 않습니다. 엔드포인트/MI/온-프레미스 데이터 액세스 자세한 내용은 [AZURE SSIS integration runtime을 가상 네트워크에 가입](join-azure-ssis-integration-runtime-virtual-network.md)을 참조 하세요.
-   Azure 리소스에 대 한 Data Factory 관리 id로 azure AD 인증을 허용 하 여 SSISDB에 연결할 수 있는 azure AD (Azure Active Directory) 인증 및 SQL 인증 지원
-   Azure 하이브리드 혜택 옵션을 사용 하 여 비용을 크게 절감할 수 있도록 온-프레미스 SQL Server 라이선스 가져오기 지원
-   고급/프리미엄 기능, 추가 구성 요소/확장을 설치 하기 위한 사용자 지정 설치 인터페이스 및 파트너 에코 시스템을 사용할 수 있는 Azure SSIS 통합 런타임의 Enterprise Edition에 대 한 지원입니다. 자세한 내용은 [Enterprise Edition, 사용자 지정 설정 및 ADF의 SSIS에 대 한 타사 확장성](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)을 참조 하세요. 
-   Data Factory에서 SSIS를 보다 긴밀 하 게 통합 하 여 Data Factory 파이프라인에서 SSIS 패키지 작업을 호출 하 고 실행 하 고 SSMS를 통해 예약할 수 있습니다. 자세한 내용은 [ADF 파이프라인에서 SSIS 작업을 사용 하 여 ETL/ELT 워크플로 현대화 및 확장](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)(영문)을 참조 하세요.


## <a name="what-is-the-integration-runtime"></a>Integration runtime 이란?
Integration runtime은 다양 한 네트워크 환경에서 다음과 같은 데이터 통합 기능을 제공 하기 위해 Azure Data Factory 사용 하는 계산 인프라입니다.

- **데이터 이동**: 데이터 이동의 경우 integration runtime은 기본 제공 커넥터, 형식 변환, 열 매핑, 성능과 확장성이 뛰어난 데이터 전송에 대 한 지원을 제공 하는 동시에 원본 및 대상 데이터 저장소 간에 데이터를 이동 합니다.
- **Dispatch 활동**: 변환의 경우 통합 런타임은 SSIS 패키지를 고유 하 게 실행 하는 기능을 제공 합니다.
- **SSIS 패키지 실행**: Integration runtime은 기본적으로 관리 되는 Azure 계산 환경에서 SSIS 패키지를 실행 합니다. Integration runtime은 Azure HDInsight, Azure Machine Learning, SQL Database 및 SQL Server와 같은 다양 한 계산 서비스에서 실행 되는 변환 작업을 디스패치 하 고 모니터링 하는 기능도 지원 합니다.

데이터를 이동 하 고 변환 하는 데 필요한 만큼 하나 이상의 통합 런타임 인스턴스를 배포할 수 있습니다. Integration runtime은 Azure 공용 네트워크 또는 개인 네트워크 (온-프레미스, Azure Virtual Network 또는 Amazon Web Services 가상 사설 클라우드 [VPC])에서 실행할 수 있습니다. 

자세한 내용은 [Azure Data Factory의 Integration Runtime](concepts-integration-runtime.md)을 참조하세요.

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>통합 런타임의 수 제한은 얼마입니까?
Data Factory에서 사용할 수 있는 Integration Runtimes 인스턴스의 수에는 엄격한 제한이 없습니다. 하지만 Integration Runtime이 SSIS 패키지 실행을 위해 구독당 사용할 수 있는 VM 코어 수는 제한되어 있습니다. 자세한 내용은 [Data Factory 제한](../azure-subscription-service-limits.md#data-factory-limits)을 참조하세요.

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory의 최상위 개념은 무엇인가요?
Azure 구독에는 하나 이상의 Azure Data Factory 인스턴스(또는 Data Factory)가 있을 수 있습니다. Azure Data Factory는 함께 작동하여 데이터를 이동하고 변환하는 단계를 사용하여 데이터 기반 워크플로를 작성할 수 있는 플랫폼으로서 함께 작동하는 네 가지 핵심 구성 요소로 구성됩니다.

### <a name="pipelines"></a>파이프라인
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 작업 단위를 수행하는 작업의 논리적 그룹입니다. 파이프라인의 활동이 모여 작업을 수행합니다. 예를 들어 Azure Blob에서 데이터를 수집한 다음 HDInsight 클러스터에서 Hive 쿼리를 실행하여 데이터를 분할하는 작업 그룹이 파이프라인에 포함될 수 있습니다. 이점은 각 작업을 개별적으로 관리하는 대신, 파이프라인을 사용하여 여러 작업을 하나의 집합으로 관리할 수 있다는 것입니다. 파이프라인의 작업을 서로 연결하여 순차적으로 작동하거나 독립적으로 병렬 작동할 수 있습니다.

### <a name="activities"></a>활동
작업은 파이프라인의 처리 단계를 나타냅니다. 예를 들어 복사 작업을 사용 하 여 한 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 작업을 사용할 수 있습니다. Data Factory는 데이터 이동 작업, 데이터 변환 작업 및 제어 작업이라는 세 종류의 작업을 지원합니다.

### <a name="datasets"></a>데이터 세트
데이터 세트는 데이터 저장소 내의 데이터 구조를 나타내며, 사용자가 활동에서 입력 또는 출력으로 사용하려는 데이터를 가리키거나 참조할 뿐입니다. 

### <a name="linked-services"></a>연결된 서비스
연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 이러한 방식으로 생각 하면 됩니다. 연결 된 서비스는 데이터 원본에 대 한 연결을 정의 하 고 데이터 집합은 데이터의 구조를 나타냅니다. 예를 들어 Azure Storage 연결 서비스는 Azure Storage 계정에 연결할 연결 문자열을 지정합니다. 그리고 Azure blob 데이터 집합은 blob 컨테이너 및 데이터가 포함 된 폴더를 지정 합니다.

연결된 서비스는 Data Factory에서 다음 두 가지 용도로 사용됩니다.

- 온-프레미스 SQL Server 인스턴스, Oracle 데이터베이스 인스턴스 또는 Azure Blob Storage 계정을 포함하지만 여기에 국한되지 않는 *데이터 저장소*를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록에 대해서는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 참조하세요.
- 활동의 실행을 호스팅할 수 있는 *컴퓨팅 리소스*를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 변환 작업 및 지원되는 컴퓨팅 환경 목록은 [Azure Data Factory에서 데이터 변환](transform-data.md)을 참조하세요.

### <a name="triggers"></a>트리거
트리거는 파이프라인 실행을 시작할 시기를 결정하는 처리 단위를 나타냅니다. 다양한 유형의 이벤트에 대한 다른 종류의 트리거가 있습니다. 

### <a name="pipeline-runs"></a>파이프라인 실행
파이프라인 실행은 파이프라인 실행의 인스턴스입니다. 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 파이프라인 실행을 인스턴스화합니다. 인수는 수동으로 또는 트리거 정의 내에서 전달할 수 있습니다.

### <a name="parameters"></a>매개 변수
매개 변수는 읽기 전용 구성의 키-값 쌍입니다. 파이프라인에서 매개 변수를 정의하고 실행 컨텍스트에서 실행하는 동안 정의된 매개 변수에 대한 인수를 전달합니다. 실행 컨텍스트는 트리거 또는 수동으로 실행하는 파이프라인에서 생성됩니다. 파이프라인 내의 작업은 매개 변수 값을 사용합니다.

데이터 집합은 강력한 형식의 매개 변수 이며 다시 사용 하거나 참조할 수 있는 엔터티입니다. 작업은 데이터 집합을 참조할 수 있으며 데이터 집합 정의에 정의 된 속성을 사용할 수 있습니다.

또한 연결된 서비스도 데이터 저장소 또는 컴퓨팅 환경에 대한 연결 정보를 포함하는 강력한 형식 매개 변수입니다. 또한 다시 사용 하거나 참조할 수 있는 엔터티입니다.

### <a name="control-flows"></a>제어 흐름
제어 흐름은 파이프라인 수준에서 정의하는 시퀀스, 분기 및 매개 변수의 작업 연결과 요청 시 또는 트리거에서 파이프라인을 호출할 때 전달하는 인수를 포함하는 파이프라인 작업을 조율합니다. 제어 흐름에는 사용자 지정 상태 전달 및 반복 컨테이너 (즉, foreach 반복기)도 포함 됩니다.


Data Factory 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)
- [파이프라인 및 활동](concepts-pipelines-activities.md)
- [통합 런타임](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory의 가격 책정 모델은 무엇입니까?
Azure Data Factory에 대한 가격 정보는 [Data Factory 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-factory/)를 참조하세요.

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Data Factory에 대한 최신 정보를 얻으려면 어떻게 해야 하나요?
Azure Data Factory에 대한 최신 정보를 얻으려면 다음 사이트로 이동하세요.

- [블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [설명서 홈페이지](/azure/data-factory)
- [제품 홈페이지](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>기술 심층 분석 

### <a name="how-can-i-schedule-a-pipeline"></a>파이프라인을 어떻게 예약할 수 있나요? 
스케줄러 트리거 또는 기간 트리거를 사용하여 파이프라인을 예약할 수 있습니다. 트리거는 벽 시계 달력 일정을 사용 합니다 .이 일정은 파이프라인을 주기적으로 예약 하거나 달력 기반 반복 패턴에서 사용할 수 있습니다 (예를 들어 월요일에 6:00 PM 및 목요일 오후 9:00 시에). 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>파이프라인 실행에 매개 변수를 전달할 수 있나요?
예, 매개 변수는 Data Factory의 최상위 수준 개념입니다. 파이프라인 수준에서 매개 변수를 정의하고 요청 시 실행되는 파이프라인 실행 중에 또는 트리거를 사용하여 인수를 전달할 수 있습니다.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>파이프라인 매개 변수의 기본값을 정의할 수 있습니까? 
예. 파이프라인의 매개 변수에 대한 기본값을 정의할 수 있습니다. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>파이프라인의 작업이 파이프라인 실행에 전달된 인수를 사용할 수 있습니까? 
예. 파이프라인 내의 각 작업은 `@parameter` 구문으로 실행되는 파이프라인에 전달된 매개 변수 값을 사용할 수 있습니다. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>활동 출력 속성이 다른 활동에서 사용될 수 있습니까? 
예. 활동 출력은 `@activity` 구문으로 후속 활동에서 사용될 수 있습니다.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>활동 출력의 Null 값을 정상적으로 처리하려면 어떻게 해야 합니까? 
식에서 `@coalesce` 구문을 사용하여 Null 값을 정상적으로 처리할 수 있습니다. 

## <a name="mapping-data-flows"></a>데이터 흐름 매핑

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>데이터 흐름을 만드는 데 사용 하는 Data Factory 버전은 무엇 인가요?
Data Factory V2 버전을 사용 하 여 데이터 흐름을 만듭니다.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>데이터 흐름을 사용한 이전 비공개 미리 보기 고객 이었던 데이터 흐름에 Data Factory V2 preview 버전을 사용 했습니다.
이 버전은 이제 사용 되지 않습니다. 데이터 흐름에 Data Factory V2를 사용 합니다.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>비공개 미리 보기에서 데이터 흐름과 관련 된 공개 미리 보기로 변경 된 내용은 무엇 인가요?
이제는 사용자 고유의 Azure Databricks 클러스터를 가져올 필요가 없습니다. Data Factory에서 클러스터 만들기 및 해체를 관리 합니다. Blob 데이터 집합 및 Azure Data Lake Storage Gen2 데이터 집합은 분리 된 텍스트 및 Apache Parquet 데이터 집합으로 구분 됩니다. 이러한 파일을 저장 하는 데에도 Data Lake Storage Gen2 및 Blob 저장소를 사용할 수 있습니다. 해당 저장소 엔진에 적절 한 연결 된 서비스를 사용 합니다.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>비공개 미리 보기 팩터리를 Data Factory V2로 마이그레이션할 수 있나요?

예. [지침을 따릅니다](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>데이터 흐름 논리 문제를 해결 하는 데 도움이 필요 합니다. 도움을 받기 위해 제공 해야 하는 정보는 무엇 인가요?

Microsoft에서 데이터 흐름에 대 한 지원을 제공 하거나 문제를 해결 하는 경우 DSL 코드 계획을 제공 하세요. 이렇게 하려면 다음 단계를 수행합니다.

1. 데이터 흐름 디자이너의 오른쪽 위 모서리에서 **코드** 를 선택 합니다. 이렇게 하면 데이터 흐름에 대 한 편집 가능한 JSON 코드가 표시 됩니다.
2. 코드 보기의 오른쪽 위 모서리에서 **요금제** 를 선택 합니다. 이 토글은 JSON에서 읽기 전용 형식의 DSL 스크립트 계획으로 전환 됩니다.
3. 이 스크립트를 복사 하 여 붙여넣거나 텍스트 파일에 저장 합니다.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Data Factory에서 다른 80 데이터 집합 형식을 사용 하 여 데이터에 액세스 어떻게 할까요??

데이터 흐름 매핑 기능을 사용 하면 현재 Azure Blob storage 또는 Azure Data Lake Storage Gen2에서 분리 된 텍스트 파일을 Azure SQL Database Azure SQL Data Warehouse 고, Blob storage에서 파일을 Parquet 하거나 원본 및 싱크에 대해 기본적으로 Data Lake Storage Gen2 수 있습니다. 

복사 작업을 사용 하 여 다른 커넥터에서 데이터를 준비 하 고 데이터 흐름 작업을 실행 하 여 데이터를 준비한 후 데이터를 변환 합니다. 예를 들어 파이프라인이 먼저 Blob storage에 복사 된 다음 데이터 흐름 작업은 원본에서 데이터 집합을 사용 하 여 해당 데이터를 변환 합니다.

## <a name="next-steps"></a>다음 단계
데이터 팩터리를 만드는 단계별 지침은 다음 자습서를 참조하세요.

- [빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md)
- [자습서: 클라우드에 데이터 복사](tutorial-copy-data-dot-net.md)
