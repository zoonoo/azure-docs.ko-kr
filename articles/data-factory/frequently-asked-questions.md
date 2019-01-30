---
title: 'Azure Data Factory: 질문과 대답 | Microsoft 문서'
description: Azure Data Factory에 대한 질문과 대답입니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: be0cdeed81c66e1a848b44d2429c1c67bce9b4f3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024096"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory FAQ
이 아티클에서는 Azure Data Factory에 대한 질문과 대답을 제공합니다.  

## <a name="what-is-azure-data-factory"></a>Azure 데이터 팩터리란 무엇인가요? 
Data Factory는 데이터의 이동과 변환을 자동화하는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 장비를 작동하여 원자재를 완제품으로 변형하는 공장처럼 Azure Data Factory는 원시 데이터를 수집하여 바로 사용할 수 있는 정보로 변환하는 기존 서비스를 오케스트레이션합니다. 

Azure Data Factory를 사용하여 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동하는 데이터 기반 워크플로를 만들 수 있습니다. 또한 Azure HDInsight, Azure Data Lake Analytics 및 SSIS(SQL Server Integration Services) 같은 계산 서비스를 사용하여 데이터를 처리하고 변환할 수 있습니다. 

Data Factory를 사용하여 Azure 기반 클라우드 서비스에서 데이터 처리를 실행하거나 SSIS, SQL Server 및 Oracle과 같은 자체 호스팅 계산 환경을 활용할 수 있습니다. 필요한 작업을 수행하는 파이프라인을 만든 후에 정기적(예: 매시간, 매일 또는 매주)으로 실행되도록 예약하거나, 기간 예약을 사용하거나, 이벤트 발생 시 파이프라인을 트리거할 수 있습니다. 자세한 내용은 [Azure Data Factory 소개](introduction.md)를 참조하세요.

### <a name="control-flows-and-scale"></a>제어 흐름 및 크기 조정 
최신 데이터 웨어하우스에서 다양한 통합 흐름 및 패턴을 지원하기 위해 Data Factory를 사용하면 조건적 실행을 비롯한 전체 제어 흐름을 포함한 유동 데이터 파이프라인 모델링, 데이터 파이프라인에서 분기를 활성화하고 이 흐름 내에서 명시적으로 매개 변수를 전달할 수 있습니다. 또한 제어 흐름은 복사 작업을 통한 대규모 데이터 이동을 비롯하여 외부 실행 엔진 및 데이터 흐름 기능에 대한 작업 디스패치를 통해 변환하는 데이터를 포괄합니다.

Data Factory는 데이터 통합에 필요하고 요청 시 또는 일정에 따라 반복해서 디스패치할 수 있는 모든 흐름 스타일을 자유롭게 모델링하도록 합니다. 이 모델이 사용할 수 있는 일반적인 흐름은 다음과 같습니다.   

- 제어 흐름:
    - 파이프라인 내에서 시퀀스의 작업을 체이닝합니다.
    - 파이프라인 내에서 작업을 분기합니다.
    - 매개 변수
        - 파이프라인 수준에서 매개 변수를 정의하고 요청 시 또는 트리거에서 파이프라인을 호출하는 동안 인수를 전달합니다.
        - 파이프라인에 전달되는 인수를 작업에 사용할 수 있습니다.
    - 사용자 지정 상태 전달
        - 상태를 포함한 작업 출력을 파이프라인의 이후 작업에서 사용할 수 있습니다.
    - 컨테이너 루프화
        - For-each 
- 트리거 기반 흐름:
    - 요청 시 또는 벽시계 시간에 따라 파이프라인을 트리거할 수 있습니다.
- 델타 흐름:
    - 차원 또는 참조 테이블을 온-프레미스 또는 클라우드의 관계형 저장소에서 이동하여 데이터를 레이크에 로드하는 동시에 매개 변수를 사용하고 데이터 복사용 높은 워터마크를 정의합니다. 

자세한 내용은 [자습서: 제어 흐름](tutorial-control-flow.md)을 참조하세요.

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>코드 제한 없는 파이프라인을 사용하여 대규모 데이터 변환
새로운 브라우저 기반 도구 환경을 통해 코드 제한 없이 파이프라인을 작성하고 최신 대화형 웹 기반 환경에서 배포할 수 있습니다.

시각적 데이터 개발자 및 데이터 엔지니어의 경우 ADF 웹 UI는 파이프라인을 빌드하는 데 사용하는 코드 제한 없는 디자인 환경입니다. 이 기능은 Visual Studio Online Git와 완전히 통합되고 CI/CD에 대한 통합 및 디버깅 옵션을 포함하는 반복 개발을 제공합니다.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>고급 사용자를 위한 풍부한 플랫폼 간 SDK
고급 사용자로서 프로그래밍 인터페이스를 찾는다면 ADF V2는 즐겨 찾는 IDE를 사용하여 파이프라인을 작성, 관리 및 모니터링할 수 있는 풍부한 SDK 집합을 제공합니다.
1.  Python SDK
2.  Powershell CLI
3.  C# SDK 사용자는 ADF V2를 사용하여 인터페이스에 대해 문서화된 REST API를 활용할 수도 있습니다.

### <a name="iterative-development-and-debugging-using-visual-tools"></a>시각적 도구를 사용하는 반복 개발 및 디버깅
ADF(Azure Data Factory) 시각적 도구를 사용하면 반복 개발 및 디버깅을 수행할 수 있습니다. 파이프라인 캔버스에서 디버그 기능을 사용하여 코드를 한 줄도 작성하지 않고 파이프라인을 만들고 테스트를 실행할 수 있습니다. 파이프라인 캔버스의 출력 창에서 테스트 실행 결과를 볼 수 있습니다. 테스트 실행이 성공하면 파이프라인에 작업을 더 추가하고 반복적으로 디버깅을 계속할 수 있습니다. 진행 중인 테스트 실행을 취소할 수도 있습니다. 디버그를 클릭하기 전에 데이터 팩터리 서비스에 대한 변경 내용을 게시하지 않아도 됩니다. 개발, 테스트 또는 프로덕션 환경에서 데이터 팩터리 워크플로를 업데이트하기 전에 새로운 추가 기능 및 변경 내용이 예상대로 작동하는지 확인하려는 시나리오에서 유용합니다. 

### <a name="deploy-ssis-packages-to-azure"></a>Azure에 SSIS 패키지 배포 
SSIS 워크로드를 이동하려는 경우 Data Factory를 만들고 Azure-SSIS Integration Runtime을 프로비전할 수 있습니다. Azure-SSIS Integration Runtime은 클라우드에서 SSIS 패키지 실행을 전담하는 완전히 관리되는 Azure VM(노드) 클러스터입니다. 단계별 지침은 [Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md) 자습서를 참조하세요. 
 
### <a name="sdks"></a>SDK
고급 사용자로서 프로그래밍 인터페이스를 찾는다면 ADF는 즐겨 찾는 IDE를 사용하여 파이프라인을 저작, 관리 또는 모니터링할 수 있는 풍부한 SDK 집합을 제공합니다. .NET, PowerShell, Python 및 REST를 비롯한 언어를 지원합니다.

### <a name="monitoring"></a>모니터링
브라우저 사용자 인터페이스에서 PowerShell, SDK 또는 시각적 개체 모니터링 도구를 통해 Data Factories를 모니터링할 수 있습니다. 주문형 트리거 기반 및 클록 기반 사용자 지정 흐름을 효율적이고 효과적인 방식으로 모니터링하고 관리할 수 있습니다. 기존 작업을 취소하고, 한 눈에 오류를 확인하고, 자세한 오류 메시지를 가져오기 위해 드릴다운하고, 콘텍스트 전환이나 스크린 앞뒤로 이동하지 않고 단일 창에서 모든 문제를 디버깅합니다. 

### <a name="new-features-for-ssis-in-adf"></a>ADF에서 SSIS의 새로운 기능
2017년 초기 공개 미리 보기 릴리스 이후 Data Factory는 SSIS에 대해 다음과 같은 기능을 추가했습니다.

-   프로젝트/패키지의 SSIS 카탈로그(SSISDB)를 호스트하도록 Azure SQL Database(DB)에 세 가지 추가 구성/변형을 지원합니다.
-   VNet 서비스 엔드포인트를 포함한 Azure SQL DB
-   MI(Managed Instance)
-   탄력적 풀
-   나중에 사용되지 않는 클래식 VNet에서 Azure Resource Manager VNet(Virtual Network)에 대한 지원 – 이기 능을 통해 VNet 서비스 엔드포인트/MI/온-프레미스 데이터 액세스를 포함한 Azure SQL DB에 대해 구성된 VNet에 Azure-SSIS IR(Integration Runtime)을 주입/조인할 수 있습니다. https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network를 참조하세요. 
-   SSISDB 연결을 위한 SQL 인증을 기반으로 AAD(Azure Active Directory) 인증 지원 - 이 기능을 통해 Azure 리소스용 ADF 관리 ID를 통한 AAD 인증을 사용할 수 있습니다.
-   AHB(Azure Hybrid Benefit) 옵션에서 상당한 비용을 절약하기 위해 고유한 온-프레미스 SQL Server 라이선스를 가져오도록 지원합니다.
-   고급/프리미엄 기능인 추가 구성 요소/확장 및 타사 에코시스템을 설치하는 사용자 지정 설정을 사용할 수 있는 Azure-SSIS IR의 Enterprise Edition에 대한 지원입니다. https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/를 참조하세요. 
-   ADF에서 SSIS의 심층 통합을 통해 ADF 파이프라인에서 최고 수준의 SSIS 패키지 실행 작업을 호출/트리거하고 SSMS를 통해 예약할 수 있습니다. https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/를 참조하세요. 


## <a name="what-is-integration-runtime"></a>Integration Runtime이란?
Integration Runtime은 서로 다른 네트워크 환경 간에 다음과 같은 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 계산 인프라입니다.

- **데이터 이동**: 데이터 이동을 위해, 통합 런타임은 데이터를 소스와 대상 데이터 저장소 간에 데이터를 이동하면서 기본 제공 커넥터, 형식 변환, 열 매핑, 성능과 확장성이 뛰어난 데이터 전송에 대한 지원을 제공합니다.
- **Dispatch 활동**: 변환을 위해, 통합 런타임은 SSIS 패키지를 고유하게 실행하는 기능을 제공합니다.
- **SSIS 패키지 실행**: SSIS 패키지를 관리되는 Azure 컴퓨팅 환경에서 고유하게 실행합니다. 또한 Integration Runtime은 Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server 등 다양한 계산 서비스에서 실행하는 변환 활동에 대한 발송 및 모니터링을 지원합니다.

데이터를 이동 및 변환하는 데 필요한 만큼 하나 이상의 Integration Runtime 인스턴스를 배포할 수 있습니다. Integration Runtime은 Azure 공용 네트워크 또는 개인 네트워크(온-프레미스, Azure Virtual Network 또는 Amazon Web Services VPC(가상 사설 클라우드))에서 실행할 수 있습니다. 

자세한 내용은 [Azure Data Factory의 Integration Runtime](concepts-integration-runtime.md)을 참조하세요.

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>통합 런타임의 수 제한은 얼마입니까?
Data Factory에서 사용할 수 있는 Integration Runtimes 인스턴스의 수에는 엄격한 제한이 없습니다. 하지만 Integration Runtime이 SSIS 패키지 실행을 위해 구독당 사용할 수 있는 VM 코어 수는 제한되어 있습니다. 자세한 내용은 [Data Factory 제한](../azure-subscription-service-limits.md#data-factory-limits)을 참조하세요.

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory의 최상위 개념은 무엇인가요?
Azure 구독에는 하나 이상의 Azure Data Factory 인스턴스(또는 Data Factory)가 있을 수 있습니다. Azure Data Factory는 함께 작동하여 데이터를 이동하고 변환하는 단계를 사용하여 데이터 기반 워크플로를 작성할 수 있는 플랫폼으로서 함께 작동하는 네 가지 핵심 구성 요소로 구성됩니다.

### <a name="pipelines"></a>파이프라인
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 작업 단위를 수행하는 작업의 논리적 그룹입니다. 파이프라인의 활동이 모여 작업을 수행합니다. 예를 들어 Azure Blob에서 데이터를 수집한 다음 HDInsight 클러스터에서 Hive 쿼리를 실행하여 데이터를 분할하는 작업 그룹이 파이프라인에 포함될 수 있습니다. 이점은 각 작업을 개별적으로 관리하는 대신, 파이프라인을 사용하여 여러 작업을 하나의 집합으로 관리할 수 있다는 것입니다. 파이프라인의 작업을 서로 연결하여 순차적으로 작동하거나 독립적으로 병렬 작동할 수 있습니다.

### <a name="activity"></a>작업
작업은 파이프라인의 처리 단계를 나타냅니다. 예를 들어, 하나의 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사하는 데 *복사* 작업을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 작업을 사용할 수 있습니다. Data Factory는 데이터 이동 작업, 데이터 변환 작업 및 제어 작업이라는 세 종류의 작업을 지원합니다.

### <a name="datasets"></a>데이터 세트
데이터 세트는 데이터 저장소 내의 데이터 구조를 나타내며, 사용자가 활동에서 입력 또는 출력으로 사용하려는 데이터를 가리키거나 참조할 뿐입니다. 

### <a name="linked-services"></a>연결된 서비스
연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 연결된 서비스는 데이터 원본에 연결을 정의하고 데이터 집합은 데이터 구조를 나타낸다고 생각하시면 됩니다. 예를 들어 Azure Storage 연결 서비스는 Azure Storage 계정에 연결할 연결 문자열을 지정합니다. 그리고 Azure Blob 데이터 집합은 Blob 컨테이너 및 데이터가 포함된 폴더를 지정합니다.

연결된 서비스는 Data Factory에서 다음 두 가지 용도로 사용됩니다.

- 온-프레미스 SQL Server 인스턴스, Oracle 데이터베이스 인스턴스 또는 Azure Blob Storage 계정을 포함하지만 여기에 국한되지 않는 *데이터 저장소*를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록에 대해서는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 참조하세요.
- 활동의 실행을 호스팅할 수 있는 *계산 리소스* 를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 변환 작업 및 지원되는 계산 환경 목록은 [Azure Data Factory에서 데이터 변환](transform-data.md)을 참조하세요.

### <a name="triggers"></a>트리거
트리거는 파이프라인 실행을 시작할 시기를 결정하는 처리 단위를 나타냅니다. 다양한 유형의 이벤트에 대한 다른 종류의 트리거가 있습니다. 

### <a name="pipeline-runs"></a>파이프라인 실행
파이프라인 실행은 파이프라인 실행의 인스턴스입니다. 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 파이프라인 실행을 인스턴스화합니다. 인수는 수동으로 또는 트리거 정의 내에서 전달할 수 있습니다.

### <a name="parameters"></a>매개 변수
매개 변수는 읽기 전용 구성의 키-값 쌍입니다. 파이프라인에서 매개 변수를 정의하고 실행 컨텍스트에서 실행하는 동안 정의된 매개 변수에 대한 인수를 전달합니다. 실행 컨텍스트는 트리거 또는 수동으로 실행하는 파이프라인에서 생성됩니다. 파이프라인 내의 작업은 매개 변수 값을 사용합니다.

데이터 집합은 다시 사용하거나 참조할 수 있는 강력한 형식의 매개 변수 및 엔터티입니다. 작업은 데이터 집합을 참조할 수 있으며 데이터 집합 정의에 정의된 속성을 사용할 수 있습니다.

또한 연결된 서비스도 데이터 저장소 또는 계산 환경에 대한 연결 정보를 포함하는 강력한 형식 매개 변수입니다. 다시 사용하거나 참조할 수 있는 엔터티이기도 합니다.

### <a name="control-flows"></a>제어 흐름
제어 흐름은 파이프라인 수준에서 정의하는 시퀀스, 분기 및 매개 변수의 작업 연결과 요청 시 또는 트리거에서 파이프라인을 호출할 때 전달하는 인수를 포함하는 파이프라인 작업을 조율합니다. 제어 흐름은 사용자 지정 상태 전달 및 컨테이너 루핑, 즉 For-each 반복기도 포함합니다.


Data Factory 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 집합 및 연결된 서비스](concepts-datasets-linked-services.md)
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
스케줄러 트리거 또는 기간 트리거를 사용하여 파이프라인을 예약할 수 있습니다. 트리거는 벽시계 캘린더 일정을 사용하며 사용자는 이를 통해 정기적으로 또는 캘린더 기반 반복 패턴(예: 매주 월요일 오후 6시 및 목요일 오후 9시)을 사용하여 파이프라인을 예약할 수 있습니다. 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>파이프라인 실행에 매개 변수를 전달할 수 있나요?
예, 매개 변수는 ADF에서 최상위 수준 개념입니다. 파이프라인 수준에서 매개 변수를 정의하고 요청 시 실행되는 파이프라인 실행 중에 또는 트리거를 사용하여 인수를 전달할 수 있습니다.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>파이프라인 매개 변수의 기본값을 정의할 수 있습니까? 
예. 파이프라인의 매개 변수에 대한 기본값을 정의할 수 있습니다. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>파이프라인의 작업이 파이프라인 실행에 전달된 인수를 사용할 수 있습니까? 
예. 파이프라인 내의 각 작업은 `@parameter` 구문으로 실행되는 파이프라인에 전달된 매개 변수 값을 사용할 수 있습니다. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>활동 출력 속성이 다른 활동에서 사용될 수 있습니까? 
예. 활동 출력은 `@activity` 구문으로 후속 활동에서 사용될 수 있습니다.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>활동 출력의 Null 값을 정상적으로 처리하려면 어떻게 해야 합니까? 
식에서 `@coalesce` 구문을 사용하여 Null 값을 정상적으로 처리할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
데이터 팩터리를 만드는 단계별 지침은 다음 자습서를 참조하세요.

- [빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md)
- [자습서: 클라우드에 데이터 복사](tutorial-copy-data-dot-net.md)
