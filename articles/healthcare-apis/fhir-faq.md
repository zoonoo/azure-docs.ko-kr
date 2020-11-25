---
title: Azure의 FHIR 서비스에 대 한 Faq-FHIR 용 Azure API
description: Fhir Api 및 버전 지원에 대 한 데이터의 저장소 위치와 같이 FHIR 용 Azure API에 대 한 질문과 대답을 확인 하세요.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 744bcfe196629f76c5505f36ef9c8c224fefe601
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95909070"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>FHIR 용 Azure API에 대 한 질문과 대답

## <a name="azure-api-for-fhir-the-basics"></a>FHIR 용 Azure API: 기본 사항

### <a name="what-is-fhir"></a>FHIR 이란?
신속한 의료 상호 운용성 리소스 (FHIR-"화재")는 서로 다른 상태 시스템 간의 의료 데이터 교환을 가능 하 게 하는 상호 운용성 표준입니다. 이 표준은 HL7 조직에서 개발 되었으며 전 세계 의료 기관에서 채택 하 고 있습니다. 최신 버전의 FHIR은 4, 4 (릴리스 4)로 제공 됩니다. FHIR 용 Azure API는 4 ~ 4를 지원 하 고 이전 버전 STU3 (평가판 사용에 대 한 표준 3)도 지원 합니다. FHIR에 대 한 자세한 내용은 [HL7.org](http://hl7.org/fhir/summary.html)를 참조 하세요.

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Azure에 저장 된 FHIR Api의 데이터 인지 여부

예, 데이터는 Azure의 관리 되는 데이터베이스에 저장 됩니다. FHIR 용 Azure API는 기본 데이터 저장소에 대 한 직접 액세스를 제공 하지 않습니다.

### <a name="what-identity-provider-do-you-support"></a>지원 되는 id 공급자는 무엇 인가요?

현재 id 공급자로 Microsoft Azure Active Directory을 지원 합니다.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>FHIR 용 Azure API에 대 한 RPO (복구 지점 목표) 란 무엇 인가요?
FHIR 용 Azure API는 지 속성 공급자로 Cosmos DB에 의해 지원 됩니다. 이로 인해 서비스에 대 한 RPO는 [Cosmos DB (단일 지역)](https://docs.microsoft.com/azure/cosmos-db/consistency-levels) 과 같고 240 분 < 됩니다.

### <a name="what-fhir-version-do-you-support"></a>어떤 FHIR 버전을 지원 하나요?

Azure API for FHIR (PaaS) 및 Azure 용 FHIR 서버 (오픈 소스) 모두에서 4.0.0 및 3.0.1 버전을 지원 합니다.

자세한 내용은 [지원 되는 기능](fhir-features-supported.md)을 참조 하세요. [HL7 FHIR의 버전 기록](https://hl7.org/fhir/R4/history.html)에서 fhir 버전 (즉, STU3 to 4) 사이에 변경 된 내용에 대해 알아보세요.

FHIR 용 azure IoT 커넥터 (미리 보기)는 현재 FHIR 버전 4만 지원 하며, FHIR 용 Azure API의 4 개 인스턴스에서만 표시 됩니다.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>' Azure 용 Microsoft FHIR 서버 '와 ' FHIR 용 Azure API '의 차이점은 무엇 인가요?

FHIR 용 Azure API는 호스트 되 고 관리 되는 버전의 Azure 용 오픈 소스 Microsoft FHIR 서버입니다. 관리 서비스에서 Microsoft는 모든 유지 관리 및 업데이트를 제공 합니다. 

Azure 용 FHIR 서버를 실행 하면 기본 서비스에 직접 액세스할 수 있지만, 사용 하지 않는 데이터를 저장 하는 경우 서버를 유지 관리 하 고 업데이트 하는 데 필요한 모든 준수 작업을 담당 합니다.

개발 관점에서 관리 되는 서비스에만 적용 되지 않는 모든 기능은 처음에는 Azure 용 오픈 소스 Microsoft FHIR 서버에 배포 됩니다. 오픈 소스에서 유효성을 검사 한 후에는 FHIR 솔루션에 대 한 PaaS Azure API로 릴리스됩니다. 오픈 소스와 PaaS의 릴리스 간 시간은 기능의 복잡성과 기타 로드맵 우선 순위에 따라 달라 집니다. 이는 FHIR 용 Azure IoT 커넥터 (미리 보기)와 같은 모든 서비스에 대해 동일한 프로세스입니다.

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Azure API for FHIR에 릴리스 되는 항목은 어디에서 확인할 수 있나요?

Azure API for FHIR에 릴리스 하는 항목 중 일부를 보려면 오픈 소스 FHIR 서버 [릴리스](https://github.com/microsoft/fhir-server/releases) 를 참조 하세요. 11 월 2020부터 오픈 소스 항목이 관리 서비스로 릴리스되는 경우 Azure-API-FHIR로 태그가 지정 된 항목을 포함 합니다. 이러한 기능은 일반적으로 2 주 후에 오픈 소스의 릴리스 페이지에 제공 됩니다. 자신의 환경에서 테스트 하려는 경우 [여기] 빌드를 테스트 하는 방법에 대 한 지침도 포함 되어 https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) 있습니다. 관리 되는 추가 서비스 업데이트를 가장 잘 공유 하는 방법을 평가 하 고 있습니다.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>어떤 지역이 Azure API for FHIR을 사용할 수 있나요?

현재 [여러 지리적 지역](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)에서 공개 및 정부에 대 한 일반 공급이 있습니다. Microsoft의 정부 클라우드 서비스에 대 한 자세한 내용은 [FedRAMP에서 Azure 서비스](../azure-government/compliance/azure-services-in-fedramp-auditscope.md)를 확인 하세요.

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Azure API for FHIR에 릴리스 되는 항목은 어디에서 확인할 수 있나요?

Azure API for FHIR에 릴리스 하는 항목 중 일부를 보려면 오픈 소스 FHIR 서버 [릴리스](https://github.com/microsoft/fhir-server/releases) 를 참조 하세요. 관리 서비스로 릴리스되는 경우에는 Azure-API-FHIR로 항목에 태그를 설정 하 고, 일반적으로 2 주 후에 오픈 소스의 릴리스 페이지에서 사용할 수 있습니다. 사용자 환경에서 테스트 하려는 경우 [여기](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) 에서 빌드를 테스트 하는 방법에 대 한 지침도 포함 되어 있습니다. 관리 되는 추가 서비스 업데이트를 가장 잘 공유 하는 방법을 평가 하 고 있습니다.

### <a name="what-is-smart-on-fhir"></a>FHIR의 SMART 란?

FHIR의 SMART (대체 가능한 의료 응용 프로그램 및 재사용 가능한 기술)는 파트너 응용 프로그램을 FHIR 서버와 통합 하는 개방형 사양 집합으로, 전자적 상태 레코드 및 상태 정보 교환 등의 기타 상태 IT 시스템입니다. SMART on FHIR 응용 프로그램을 만들어 응용 프로그램을 서로 다른 시스템의 다양 한에서 액세스 하 고 활용할 수 있는지 확인할 수 있습니다.
FHIR 용 인증 및 Azure API. SMART에 대해 자세히 알아보려면 [스마트 상태](https://smarthealthit.org/)를 참조 하세요.

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>데이터베이스에서 실행 되는 FHIR 버전은 어디에서 찾을 수 있나요? 

기능 문에 표시 되는 정확한 Fhir 버전은 "Fstrversion" 속성 아래에서 찾을 수 있습니다.

## <a name="fhir-implementations-and-specifications"></a>구현 및 사양

### <a name="can-i-create-a-custom-fhir-resource"></a>사용자 지정 FHIR 리소스를 만들 수 있나요?

사용자 지정 FHIR 리소스를 허용 하지 않습니다. 사용자 지정 FHIR 리소스가 필요한 경우에는 확장을 사용 하 여 [기본 리소스](http://www.hl7.org/fhir/basic.html) 위에 사용자 지정 리소스를 빌드할 수 있습니다. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>FHIR 용 Azure API에서 지원 되는 [확장](https://www.hl7.org/fhir/extensibility.html) 은 무엇 인가요?

모든 유효한 FHIR JSON 데이터를 서버에 로드할 수 있습니다. 확장을 정의 하는 구조 정의를 저장 하려는 경우이를 구조체 정의 리소스로 저장할 수 있습니다. 현재는 확장을 검색할 수 없습니다.

### <a name="what-is-the-limit-on-_count"></a>_Count에 대 한 제한은 무엇 인가요?

_Count에 대 한 현재 제한은 100입니다. _Count를 100 이상으로 설정 하면 100 레코드만 표시 되는 번들에 경고가 표시 됩니다.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>그룹 내보내기 기능에 제한이 있나요?

그룹 내보내기의 경우 그룹 [리소스](https://www.hl7.org/fhir/group.html)의 모든 특성이 아니라 그룹의 포함 된 참조만 내보냅니다.

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>FHIR 용 Azure API에 번들을 게시할 수 있나요?

현재 [batch 번들](https://www.hl7.org/fhir/valueset-bundle-type.html) 의 게시를 지원 하지만 FHIR 용 Azure API에는 트랜잭션 번들 게시를 지원 하지 않습니다. SQL에서 지원 되는 오픈 소스 FHIR 서버를 사용 하 여 트랜잭션 번들을 게시할 수 있습니다.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Azure API for FHIR에서 단일 환자에 대 한 모든 리소스를 얻으려면 어떻게 해야 하나요?

FHIR 용 Azure API에서 [컴파트먼트 검색](https://www.hl7.org/fhir/compartmentdefinition.html) 을 지원 합니다. 이렇게 하면 특정 환자와 관련 된 모든 리소스를 가져올 수 있습니다. 올바른 now 구획에는 환자와 관련 된 모든 리소스가 포함 되어 있으므로, 결과에 환자 리소스가 필요한 경우에도 환자를 가져오도록 검색 해야 합니다.

이에 대 한 몇 가지 예는 다음과 같습니다.

* 환자 받기/<id>/*
* 환자/ <id> /CHGET
* 환자/ <id> /c? 코드 = 8302-2

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>FHIR 용 Azure API에서 리소스를 검색할 때의 기본 정렬은 무엇 인가요?

마지막으로 업데이트 한 날짜의 정렬을 지원 합니다. _sort = _lastUpdated. 지원 되는 다른 검색 매개 변수에 대 한 자세한 내용은 [지원 되는 기능 페이지](./fhir-features-supported.md#search)를 참조 하세요.

### <a name="how-does-export-work"></a>$export 작동 방법

$export은 FHIR 사양 ()의 일부 https://hl7.org/fhir/uv/bulkdata/export/index.html 입니다. FHIR 서비스가 관리 되는 id 및 저장소 계정으로 구성 되 고 관리 id에 해당 저장소 계정에 대 한 액세스 권한이 있는 경우 FHIR API에서 $export를 간단히 호출할 수 있으며 모든 FHIR 리소스는 저장소 계정으로 내보내집니다. 자세한 내용은 [$export에서 문서](./export-data.md)를 참조 하세요.

## <a name="using-azure-api-for-fhir"></a>FHIR 용 Azure API 사용

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Azure API에 대 한 log analytics를 사용 하도록 설정 어떻게 할까요?

진단 로깅을 사용 하도록 설정 하 고 이러한 로그에 대 한 샘플 쿼리를 검토 합니다. 감사 로그 및 샘플 쿼리를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [이 섹션](./enable-diagnostic-logging.md)을 참조 하세요. 로그에 추가 정보를 포함 하려면 [사용자 지정 HTTP 헤더를 사용 하 여](./use-custom-headers.md)확인 합니다.

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>워크플로 내에서 Azure API for FHIR을 사용 하는 몇 가지 예는 어디에서 확인할 수 있나요?

[상태 아키텍처 GitHub 페이지](https://github.com/microsoft/health-architectures)에서 사용할 수 있는 참조 아키텍처 컬렉션이 있습니다.

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>웹 응용 프로그램을 Azure API에 연결 하는 예제는 어떻게 볼 수 있나요?

예제 응용 프로그램 및 시나리오를 포함 하는 [상태 아키텍처 GitHub 페이지가](https://github.com/microsoft/health-architectures) 있습니다. 웹 응용 프로그램을 Azure API for FHIR에 연결 하는 방법을 보여 줍니다.  

## <a name="azure-api-for-fhir-features-and-services"></a>Azure API for FHIR 기능 및 서비스 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>기본 키가 아닌 개인 키를 사용 하 여 데이터를 암호화 하는 방법이 있나요?

예, FHIR 용 Azure API는 고객이 관리 하는 키를 구성 하 여 Cosmos DB 지원을 활용할 수 있도록 합니다. 개인 키를 사용 하 여 데이터를 암호화 하는 방법에 대 한 자세한 내용은 [이 섹션](./customer-managed-key.md)을 확인 하세요.

## <a name="azure-api-for-fhir-preview-features"></a>FHIR 용 Azure API: 미리 보기 기능

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>FHIR (미리 보기) 용 Azure IoT Connector에 대 한 크기 조정 용량을 구성할 수 있나요?

Azure IoT Connector for FHIR은 공개 미리 보기 중에 무료로 제공 되므로 크기 조정 용량이 고정 되 고 제한 됩니다. 공개 미리 보기에서 사용할 수 있는 FHIR 용 Azure IoT 커넥터 구성은 초당 약 200 메시지의 처리량을 제공 합니다. 일부 형식의 리소스 용량 구성은 GA (일반 공급)에서 사용할 수 있게 됩니다.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>FHIR 용 Azure API에서 개인 링크를 사용 하도록 설정 하는 경우 FHIR (미리 보기) 용 Azure IoT 커넥터를 설치할 수 없는 이유는 무엇 인가요?

FHIR 용 Azure IoT Connector는 지금은 개인 링크 기능을 지원 하지 않습니다. 따라서 FHIR 용 Azure API에서 개인 링크를 사용 하도록 설정한 경우 FHIR 용 Azure IoT 커넥터를 설치할 수 없으며 그 반대의 경우도 마찬가지입니다. FHIR 용 Azure IoT Connector를 GA (일반 공급)에 사용할 수 있는 경우이 제한이 사라집니다.