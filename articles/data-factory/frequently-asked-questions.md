---
title: 'Azure 데이터 팩터리: 자주 묻는 질문 '
description: Azure Data Factory에 대한 질문과 대답입니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 8d0b49b73ef6b67653fbf32db1174880a51d432d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412956"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory FAQ

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 아티클에서는 Azure Data Factory에 대한 질문과 대답을 제공합니다.  

## <a name="what-is-azure-data-factory"></a>Azure 데이터 팩터리란 무엇인가요? 
Data Factory는 데이터의 이동 과 변환을 자동화하는 완전히 관리되는 클라우드 기반 의 데이터 통합 ETL 서비스입니다. 장비를 작동하여 원자재를 완제품으로 변형하는 공장처럼 Azure Data Factory는 원시 데이터를 수집하여 바로 사용할 수 있는 정보로 변환하는 기존 서비스를 오케스트레이션합니다. 

Azure Data Factory를 사용하여 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동하는 데이터 기반 워크플로를 만들 수 있습니다. 또한 데이터 흐름을 사용하여 데이터를 처리하고 변환할 수 있습니다. 또한 ADF는 Azure HDInsight, Azure Databricks 및 SQL Server 통합 서비스(SSIS) 통합 런타임과 같은 계산 서비스를 사용하여 수동 코딩변환에 대한 외부 컴퓨팅 엔진을 지원합니다. 

Data Factory를 사용하여 Azure 기반 클라우드 서비스에서 데이터 처리를 실행하거나 SSIS, SQL Server 및 Oracle과 같은 자체 호스팅 컴퓨팅 환경을 활용할 수 있습니다. 필요한 작업을 수행하는 파이프라인을 만든 후 주기적으로 실행되도록 예약하거나(예: 시간별, 일별 또는 매주), 시간 창 예약 또는 이벤트 발생에서 파이프라인을 트리거할 수 있습니다. 자세한 내용은 [Azure Data Factory 소개](introduction.md)를 참조하세요.

### <a name="control-flows-and-scale"></a>제어 흐름 및 크기 조정 
최신 데이터 웨어하우스의 다양한 통합 흐름과 패턴을 지원하기 위해 Data Factory는 유연한 데이터 파이프라인 모델링을 지원합니다. 여기에는 조건부 실행, 데이터 파이프라인의 분기 및 이러한 흐름 내에서 매개 변수를 명시적으로 전달하는 기능이 포함된 전체 제어 흐름 프로그래밍 패러다임이 수반됩니다. 제어 흐름에는 외부 실행 엔진으로의 활동 디스패치를 통해 데이터를 변환하는 것도 포함되며, 복사 활동을 통해 대규모 데이터 이동을 비롯한 데이터 흐름 기능도 포함됩니다.

Data Factory는 데이터 통합에 필요하고 요청 시 또는 일정에 따라 반복해서 디스패치할 수 있는 모든 흐름 스타일을 자유롭게 모델링하도록 합니다. 이 모델이 사용할 수 있는 일반적인 흐름은 다음과 같습니다.   

- 제어 흐름:
    - 활동은 파이프라인 내의 시퀀스에서 함께 연결할 수 있습니다.
    - 활동은 파이프라인 내에서 분기할 수 있습니다.
    - 매개 변수
        - 매개 변수는 파이프라인 수준에서 정의할 수 있으며 요청 시 또는 트리거에서 파이프라인을 호출하는 동안 인수를 전달할 수 있습니다.
        - 파이프라인에 전달되는 인수를 작업에 사용할 수 있습니다.
    - 사용자 지정 상태 전달:
        - 상태를 포함한 활동 출력은 파이프라인의 후속 활동에 의해 사용될 수 있습니다.
    - 반복 컨테이너:
        - foreach 활동은 루프에서 지정된 활동 컬렉션을 반복합니다. 
- 트리거 기반 흐름:
    - 요청 시 또는 벽시계 시간에 따라 파이프라인을 트리거할 수 있습니다.
- 델타 흐름:
    - 매개변수는 온-프레미스 또는 클라우드에서 차원 또는 참조 테이블을 이동하는 동안 델타 복사본에 대한 하이 워터 마크를 정의하여 데이터를 호수에 로드하는 데 사용할 수 있습니다. 

자세한 내용은 [자습서: 제어 흐름](tutorial-control-flow.md)을 참조하세요.

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>코드 없는 파이프라인으로 대규모로 변환되는 데이터
새로운 브라우저 기반 도구 환경을 통해 코드 제한 없이 파이프라인을 작성하고 최신 대화형 웹 기반 환경에서 배포할 수 있습니다.

시각적 데이터 개발자와 데이터 엔지니어의 경우 Data Factory 웹 UI는 파이프라인을 빌드하는 데 사용할 코드 없는 디자인 환경입니다. Visual Studio Online Git과 완벽하게 통합되어 있으며 CI/CD 및 디버깅 옵션으로 반복개발을 위한 통합을 제공합니다.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>고급 사용자를 위한 풍부한 크로스 플랫폼 SDK
데이터 팩터리 V2는 다음과 같은 즐겨 찾는 IDE를 사용하여 파이프라인을 작성, 관리 및 모니터링하는 데 사용할 수 있는 풍부한 SDK 집합을 제공합니다.
* Python SDK
* 파워쉘 CLI
* C# SDK

또한 사용자는 문서화된 REST API를 사용하여 데이터 팩터리 V2와 인터페이스할 수 있습니다.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>시각적 도구를 사용하여 반복 개발 및 디버깅
Azure Data Factory 시각적 도구를 사용하면 반복적인 개발 및 디버깅을 수행할 수 있습니다. 한 줄의 코드를 작성하지 않고 파이프라인 캔버스에서 **디버그** 기능을 사용하여 파이프라인을 만들고 테스트 실행을 수행할 수 있습니다. 파이프라인 캔버스의 **출력** 창에서 테스트 실행 결과를 볼 수 있습니다. 테스트 실행이 성공하면 파이프라인에 더 많은 활동을 추가하고 반복적인 방식으로 디버깅을 계속할 수 있습니다. 테스트 실행이 진행 중이면 취소할 수도 있습니다. 

**디버그**를 선택하기 전에 변경 내용을 데이터 팩터리 서비스에 게시할 필요가 없습니다. 이는 개발, 테스트 또는 프로덕션 환경에서 데이터 팩터리 워크플로를 업데이트하기 전에 새 추가 또는 변경 내용이 예상대로 작동하는지 확인하려는 시나리오에서 유용합니다. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>SSIS 패키지를 Azure에 배포하는 기능 
SSIS 워크로드를 이동하려는 경우 Data Factory를 만들고 Azure-SSIS Integration Runtime을 프로비전할 수 있습니다. Azure-SSIS 통합 런타임은 클라우드에서 SSIS 패키지를 실행하기 위해 전용으로 운영되는 Azure VM(노드)의 완전히 관리되는 클러스터입니다. 단계별 지침은 [Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md) 자습서를 참조하세요. 
 
### <a name="sdks"></a>SDK
고급 사용자이고 프로그래밍 방식 인터페이스를 찾고 있는 경우 Data Factory는 즐겨 찾는 IDE를 사용하여 파이프라인을 작성, 관리 또는 모니터링하는 데 사용할 수 있는 풍부한 SDK 집합을 제공합니다. .NET, PowerShell, Python 및 REST를 비롯한 언어를 지원합니다.

### <a name="monitoring"></a>모니터링
브라우저 사용자 인터페이스에서 PowerShell, SDK 또는 시각적 개체 모니터링 도구를 통해 Data Factories를 모니터링할 수 있습니다. 효율적이고 효과적인 방식으로 온디맨드, 트리거 기반 및 클럭 기반 사용자 지정 흐름을 모니터링하고 관리할 수 있습니다. 컨텍스트 전환이나 화면 사이를 앞뒤로 이동하지 않고 기존 작업을 취소하고, 오류를 한눈에 확인하고, 자세한 오류 메시지를 받고, 모든 창을 유리창에서 디버깅합니다. 

### <a name="new-features-for-ssis-in-data-factory"></a>데이터 팩토리의 SSIS를 위한 새로운 기능
2017년 초기 공개 미리 보기 릴리스 이후 데이터 팩터리는 SSIS에 다음과 같은 기능을 추가했습니다.

-    프로젝트/패키지의 SSIS 데이터베이스(SSISDB)를 호스트하기 위해 Azure SQL Database의 세 가지 구성/변형을 추가로 지원합니다.
-    가상 네트워크 서비스 끝점이 있는 SQL 데이터베이스
-    관리되는 인스턴스
-    탄력적 풀
-    나중에 더 이상 사용되지 않을 클래식 가상 네트워크 위에 있는 Azure Resource Manager 가상 네트워크에 대한 지원으로, 가상 네트워크 서비스 엔드포인트/MI/온-프레미스 데이터 액세스를 사용하여 SQL Database용으로 구성된 가상 네트워크에 Azure-SSIS 통합 런타임을 삽입/조인할 수 있습니다. 자세한 내용은 [Azure-SSIS 통합 런타임을 가상 네트워크에 조인하는](join-azure-ssis-integration-runtime-virtual-network.md)것을 참조하세요.
-    Azure Active Directory(Azure AD) 인증 및 SQL 인증을 지원하여 SSISDB에 연결하여 Azure 리소스에 대해 데이터 팩터리 관리 ID를 사용하여 Azure AD 인증을 허용합니다.
-    Azure 하이브리드 혜택 옵션에서 상당한 비용 절감을 위해 자체 온-프레미스 SQL Server 라이선스 가져오기 지원
-    고급/프리미엄 기능, 추가 구성 요소/확장을 설치하는 사용자 지정 설정 인터페이스 및 파트너 에코시스템을 사용할 수 있는 Azure-SSIS 통합 런타임의 엔터프라이즈 에디션 지원입니다. 자세한 내용은 [ADF의 SSIS에 대한 엔터프라이즈 에디션, 사용자 지정 설정 및 타사 확장성도](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)참조하십시오. 
-    데이터 팩터리 파이프라인에서 일류 실행 SSIS 패키지 활동을 호출/트리거하고 SSMS를 통해 예약할 수 있는 데이터 팩터리의 SSIS를 보다 심층통합합니다. 자세한 내용은 [ADF 파이프라인의 SSIS 활동을 통해 ETL/ELT 워크플로우를 현대화하고 확장하십시오.](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)


## <a name="what-is-the-integration-runtime"></a>통합 런타임이란 무엇입니까?
통합 런타임은 Azure Data Factory가 다양한 네트워크 환경에서 다음과 같은 데이터 통합 기능을 제공하는 데 사용하는 계산 인프라입니다.

- **데이터 이동**: 데이터 이동의 경우 통합 런타임은 원본과 대상 데이터 저장소 간에 데이터를 이동하는 동시에 기본 제공 커넥터, 형식 변환, 열 매핑 및 성능 및 확장 가능한 데이터 전송에 대한 지원을 제공합니다.
- **디스패치 활동**: 변환의 경우 통합 런타임은 기본적으로 SSIS 패키지를 실행하는 기능을 제공합니다.
- **SSIS 패키지 실행**: 통합 런타임은 관리되는 Azure 계산 환경에서 SSIS 패키지를 기본적으로 실행합니다. 통합 런타임은 Azure HDInsight, Azure 기계 학습, SQL Database 및 SQL Server와 같은 다양한 계산 서비스에서 실행되는 변환 활동 의 디스패치 및 모니터링도 지원합니다.

데이터를 이동하고 변환하는 데 필요한 경우 통합 런타임의 하나 또는 여러 인스턴스를 배포할 수 있습니다. 통합 런타임은 Azure 공용 네트워크 또는 개인 네트워크(온-프레미스, Azure 가상 네트워크 또는 Amazon Web Services 가상 사설 클라우드 [VPC]])에서 실행할 수 있습니다. 

자세한 내용은 [Azure 데이터 팩터리의 통합 런타임을](concepts-integration-runtime.md)참조하십시오.

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>통합 런타임의 수 제한은 얼마입니까?
Data Factory에서 사용할 수 있는 Integration Runtimes 인스턴스의 수에는 엄격한 제한이 없습니다. 하지만 Integration Runtime이 SSIS 패키지 실행을 위해 구독당 사용할 수 있는 VM 코어 수는 제한되어 있습니다. 자세한 내용은 [Data Factory 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)을 참조하세요.

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory의 최상위 개념은 무엇인가요?
Azure 구독에는 하나 이상의 Azure Data Factory 인스턴스(또는 Data Factory)가 있을 수 있습니다. Azure Data Factory는 함께 작동하여 데이터를 이동하고 변환하는 단계를 사용하여 데이터 기반 워크플로를 작성할 수 있는 플랫폼으로서 함께 작동하는 네 가지 핵심 구성 요소로 구성됩니다.

### <a name="pipelines"></a>파이프라인
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 작업 단위를 수행하는 작업의 논리적 그룹입니다. 파이프라인의 활동이 모여 작업을 수행합니다. 예를 들어 Azure Blob에서 데이터를 수집한 다음 HDInsight 클러스터에서 Hive 쿼리를 실행하여 데이터를 분할하는 작업 그룹이 파이프라인에 포함될 수 있습니다. 이점은 각 작업을 개별적으로 관리하는 대신, 파이프라인을 사용하여 여러 작업을 하나의 집합으로 관리할 수 있다는 것입니다. 파이프라인의 작업을 서로 연결하여 순차적으로 작동하거나 독립적으로 병렬 작동할 수 있습니다.

### <a name="data-flows"></a>데이터 흐름
데이터 흐름은 백 엔드 스파크 서비스의 규모로 데이터를 변환하는 데이터 팩터리에서 시각적으로 빌드하는 개체입니다. 프로그래밍이나 스파크 내부를 이해할 필요가 없습니다. 그래프(매핑) 또는 스프레드시트(랭글링)를 사용하여 데이터 변환 의도를 디자인하기만 하면 됩니다.

### <a name="activities"></a>활동
작업은 파이프라인의 처리 단계를 나타냅니다. 예를 들어 복사 활동을 사용하여 한 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 작업을 사용할 수 있습니다. Data Factory는 데이터 이동 작업, 데이터 변환 작업 및 제어 작업이라는 세 종류의 작업을 지원합니다.

### <a name="datasets"></a>데이터 세트
데이터 세트는 데이터 저장소 내의 데이터 구조를 나타내며, 사용자가 활동에서 입력 또는 출력으로 사용하려는 데이터를 가리키거나 참조할 뿐입니다. 

### <a name="linked-services"></a>연결된 서비스
연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 연결된 서비스는 데이터 원본에 대한 연결을 정의하고 데이터 집합은 데이터 구조를 나타냅니다. 예를 들어 Azure Storage 연결 서비스는 Azure Storage 계정에 연결할 연결 문자열을 지정합니다. Azure Blob 데이터 집합은 Blob 컨테이너와 데이터가 포함된 폴더를 지정합니다.

연결된 서비스는 Data Factory에서 다음 두 가지 용도로 사용됩니다.

- 온-프레미스 SQL Server 인스턴스, Oracle 데이터베이스 인스턴스 또는 Azure Blob Storage 계정을 포함하지만 여기에 국한되지 않는 *데이터 저장소*를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록에 대해서는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 참조하세요.
- 활동의 실행을 호스팅할 수 있는 *컴퓨팅 리소스*를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 변환 작업 및 지원되는 컴퓨팅 환경 목록은 [Azure Data Factory에서 데이터 변환](transform-data.md)을 참조하세요.

### <a name="triggers"></a>트리거
트리거는 파이프라인 실행을 시작할 시기를 결정하는 처리 단위를 나타냅니다. 다양한 유형의 이벤트에 대한 다른 종류의 트리거가 있습니다. 

### <a name="pipeline-runs"></a>파이프라인 실행
파이프라인 실행은 파이프라인 실행의 인스턴스입니다. 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 파이프라인 실행을 인스턴스화합니다. 인수는 수동으로 또는 트리거 정의 내에서 전달할 수 있습니다.

### <a name="parameters"></a>매개 변수
매개 변수는 읽기 전용 구성의 키-값 쌍입니다.파이프라인에서 매개 변수를 정의하고 실행 컨텍스트에서 실행하는 동안 정의된 매개 변수에 대한 인수를 전달합니다. 실행 컨텍스트는 트리거 또는 수동으로 실행하는 파이프라인에서 생성됩니다. 파이프라인 내의 작업은 매개 변수 값을 사용합니다.

데이터 집합은 강력하게 입력된 매개 변수이며 재사용하거나 참조할 수 있는 엔터티입니다. 활동은 데이터 집합을 참조할 수 있으며 데이터 집합 정의에 정의된 속성을 사용할 수 있습니다.

또한 연결된 서비스도 데이터 저장소 또는 컴퓨팅 환경에 대한 연결 정보를 포함하는 강력한 형식 매개 변수입니다. 또한 다시 사용하거나 참조할 수 있는 엔터티이기도 합니다.

### <a name="control-flows"></a>제어 흐름
제어 흐름은 파이프라인 수준에서 정의하는 시퀀스, 분기 및 매개 변수의 작업 연결과 요청 시 또는 트리거에서 파이프라인을 호출할 때 전달하는 인수를 포함하는 파이프라인 작업을 조율합니다. 제어 흐름에는 사용자 지정 상태 전달 및 반복 컨테이너(즉, foreach 반복기)도 포함됩니다.


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
스케줄러 트리거 또는 기간 트리거를 사용하여 파이프라인을 예약할 수 있습니다. 트리거는 월 시계 달력 일정을 사용하여 파이프라인을 주기적으로 또는 일정 기반 반복 패턴으로 예약할 수 있습니다(예: 월요일 오후 6:00 PM 및 목요일 오후 9:00). 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>파이프라인 실행에 매개 변수를 전달할 수 있나요?
예. 매개 변수는 데이터 팩터리의 최상위 개념입니다. 파이프라인 수준에서 매개 변수를 정의하고 요청 시 실행되는 파이프라인 실행 중에 또는 트리거를 사용하여 인수를 전달할 수 있습니다.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>파이프라인 매개 변수의 기본값을 정의할 수 있습니까? 
예. 파이프라인의 매개 변수에 대한 기본값을 정의할 수 있습니다. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>파이프라인의 작업이 파이프라인 실행에 전달된 인수를 사용할 수 있습니까? 
예. 파이프라인 내의 각 작업은 `@parameter` 구문으로 실행되는 파이프라인에 전달된 매개 변수 값을 사용할 수 있습니다. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>활동 출력 속성이 다른 활동에서 사용될 수 있습니까? 
예. 활동 출력은 `@activity` 구문으로 후속 활동에서 사용될 수 있습니다.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>활동 출력의 Null 값을 정상적으로 처리하려면 어떻게 해야 합니까? 
식에서 `@coalesce` 구문을 사용하여 Null 값을 정상적으로 처리할 수 있습니다. 

## <a name="mapping-data-flows"></a>데이터 흐름 매핑

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>데이터 흐름 논리를 해결하는 데 도움이 필요합니다. 도움을 받으려면 어떤 정보를 제공해야 하나요?

Microsoft에서 데이터 흐름에 대한 도움말 또는 문제 해결을 제공하는 경우 데이터 흐름 스크립트를 제공하십시오. 데이터 흐름 그래프의 코드 숨미는 스크립트입니다. ADF UI에서 데이터 흐름을 연 다음 오른쪽 상단 모서리에 있는 "스크립트" 단추를 클릭합니다. 이 스크립트를 복사하여 붙여넣거나 텍스트 파일에 저장합니다.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>데이터 팩터리에서 다른 90개의 데이터 집합 형식을 사용하여 데이터에 액세스하려면 어떻게 해야 합니까?

매핑 데이터 흐름 기능은 현재 Azure SQL Database, Azure SQL Data 웨어하우스, Azure Blob 저장소 또는 Azure Data Lake 저장소 Gen2의 구분된 텍스트 파일, Blob 저장소 또는 데이터 레이크 저장소 Gen2의 마루 파일을 기본적으로 소스 및 싱크에 사용할 수 있도록 합니다. 

복사 활동을 사용하여 다른 커넥터의 데이터를 스테이징한 다음 데이터 흐름 활동을 실행하여 데이터가 준비된 후 데이터를 변환합니다. 예를 들어 파이프라인이 먼저 Blob 저장소에 복사한 다음 데이터 흐름 활동은 원본의 데이터 집합을 사용하여 해당 데이터를 변환합니다.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>데이터 흐름에 대해 자체 호스팅 통합 런타임을 사용할 수 있습니까?

자체 호스팅 IR은 복사 활동과 함께 사용하여 온프레미 또는 VM 기반 데이터 원본 및 싱크에서 데이터를 가져오거나 이동할 수 있는 ADF 파이프라인 구문입니다. 먼저 복사를 사용하여 데이터를 단계화한 다음 변환을 위해 데이터 흐름을 한 다음 변환된 데이터를 온프레미 저장소로 다시 이동해야 하는 경우 후속 복사본을 정렬합니다.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>데이터 흐름 계산 엔진이 여러 테넌트에 서비스를 제공합니까?
클러스터는 공유되지 않습니다. 생산 실행에서 실행된 각 작업에 대한 격리를 보장합니다. 디버그 시나리오의 경우 한 사람이 하나의 클러스터를 얻고 모든 디버그는 해당 사용자가 시작한 해당 클러스터로 이동합니다.

## <a name="wrangling-data-flows"></a>데이터 흐름 랭글링

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>데이터 흐름을 랭글링하기 위해 지원되는 영역은 무엇입니까?

랭글링 데이터 흐름은 현재 다음 리전에서 생성된 데이터 팩토리에서 지원됩니다.

* 오스트레일리아 동부
* 캐나다 중부
* 인도 중부
* 미국 동부
* 미국 동부 2
* 일본 동부
* 북유럽
* 동남아시아
* 미국 중남부
* 영국 남부
* 미국 중서부
* 서유럽
* 미국 서부
* 미국 서부 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>랭글링 데이터 흐름의 제한 및 제약 조건은 무엇입니까?

데이터 집합 이름은 숫자 문자만 포함할 수 있습니다. 다음 데이터 저장소가 지원됩니다.

* 계정 키 인증을 사용하여 Azure Blob 저장소의 텍스트 데이터 집합 을 구분
* 계정 키 또는 서비스 주체 인증을 사용하여 Azure Data Lake Storage gen2의 구분된 텍스트 데이터 집합
* 서비스 주체 인증을 사용하여 Azure Data Lake Storage gen1의 DelimitedText 데이터 집합
* SQL 인증을 사용하는 Azure SQL 데이터베이스 및 데이터 웨어하우스입니다. 아래지원되는 SQL 유형을 참조하십시오. 데이터 웨어하우스에 대한 PolyBase 또는 스테이징 지원이 없습니다.

현재 연결된 서비스 키 볼트 통합은 데이터 흐름을 랭글링할 때 지원되지 않습니다.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>매핑과 랭글링 데이터 흐름의 차이점은 무엇입니까?

매핑 데이터 흐름은 코딩없이 대규모로 데이터를 변환하는 방법을 제공합니다. 일련의 변환을 구성하여 데이터 흐름 캔버스에서 데이터 변환 작업을 디자인할 수 있습니다. 임의 개수의 원본 변환으로 시작한 다음, 데이터 변환 단계를 수행합니다. 싱크대로 데이터 흐름을 완료하여 결과를 대상에 도착합니다. 데이터 흐름을 매핑하는 것은 싱크 및 원본에서 알려진 스키마와 알 수 없는 스키마를 사용하여 데이터를 매핑하고 변환하는 데 매우 적합합니다.

데이터 흐름을 랭글링하면 스파크 실행을 통해 대규모로 Power Query Online 매시업 편집기를 사용하여 민첩한 데이터 준비 및 탐색을 수행할 수 있습니다. 데이터 레이크가 증가함에 따라 때때로 데이터 집합을 탐색하거나 호수에서 데이터 집합을 만들어야 합니다. 알려진 대상에 매핑되지 않습니다. 데이터 흐름을 랭글링하는 것은 덜 형식적이고 모델 기반 분석 시나리오에 사용됩니다.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>전원 플랫폼 데이터 흐름과 랭글링 데이터 흐름의 차이점은 무엇입니까?

전원 플랫폼 데이터 흐름을 통해 사용자는 광범위한 데이터 원본에서 공통 데이터 서비스 및 Azure Data Lake로 데이터를 가져오고 변환하여 PowerApps 응용 프로그램, Power BI 보고서 또는 흐름 자동화를 빌드할 수 있습니다. 전원 플랫폼 데이터 흐름은 Power BI 및 Excel과 유사한 설정된 전원 쿼리 데이터 준비 환경을 사용합니다. 또한 Power Platform Dataflow를 사용하면 조직 내에서 쉽게 재사용할 수 있으며 오케스트레이션을 자동으로 처리할 수 있습니다(예: 이전 데이터 흐름을 새로 고칠 때 다른 데이터 흐름에 의존하는 데이터 흐름을 자동으로 새로 고칩니다).

Azure Data Factory(ADF)는 데이터 엔지니어와 시민 데이터 통합자가 복잡한 하이브리드 추출 변환 로드(ETL) 및 ELT(추출 로드 변환) 워크플로를 만들 수 있는 관리되는 데이터 통합 서비스입니다. ADF의 데이터 흐름을 어지럽히면 클라우드에서 데이터 준비를 간소화하고 인프라 관리가 필요 없는 모든 데이터 크기로 확장할 수 있는 코드 없는 서버리스 환경을 사용자에게 부여합니다. 파워 쿼리 데이터 준비 기술(파워 플랫폼 데이터 흐름, Excel, Power BI에도 사용)을 사용하여 데이터를 준비하고 모양을 만듭니다. 빅 데이터 통합의 모든 복잡성과 규모 문제를 처리하도록 구축된 랭글링 데이터 흐름을 통해 사용자는 스파크 실행을 통해 대규모로 데이터를 신속하게 준비할 수 있습니다. 사용자는 브라우저 기반 인터페이스를 사용하여 액세스 가능한 시각적 환경에서 복원력 있는 데이터 파이프라인을 구축하고 ADF가 Spark 실행의 복잡성을 처리할 수 있도록 할 수 있습니다. 파이프라인에 대한 일정을 작성하고 ADF 모니터링 포털에서 데이터 흐름 실행을 모니터링합니다. ADF의 풍부한 가용성 모니터링 및 경고를 통해 데이터 가용성 SLA를 쉽게 관리하고 내장된 지속적인 통합 및 배포 기능을 활용하여 관리되는 환경에서 흐름을 저장하고 관리할 수 있습니다. 경고를 설정하고 실행 계획을 확인하여 데이터 흐름을 조정할 때 논리가 계획대로 수행되고 있는지 확인합니다.

### <a name="supported-sql-types"></a>지원되는 SQL 유형

랭글링 데이터 흐름은 SQL에서 다음 데이터 형식을 지원합니다. 지원되지 않는 데이터 형식을 사용하는 경우 유효성 검사 오류가 발생합니다.

* short
* double
* real
* float
* char
* nchar
* varchar
* nvarchar
* integer
* int
* bit
* boolean
* smallint
* tinyint
* bigint
* long
* text
* date
* Datetime
* datetime2
* smalldatetime
* timestamp
* uniqueidentifier
* Xml

다른 데이터 형식은 나중에 지원됩니다.

## <a name="next-steps"></a>다음 단계
데이터 팩터리를 만드는 단계별 지침은 다음 자습서를 참조하세요.

- [빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md)
- [자습서: 클라우드에서 데이터 복사](tutorial-copy-data-dot-net.md)
