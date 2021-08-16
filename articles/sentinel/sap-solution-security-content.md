---
title: Azure Sentinel SAP 솔루션 - 보안 콘텐츠 참조 | Microsoft Docs
description: Azure Sentinel SAP 솔루션에서 제공하는 기본 제공 보안 콘텐츠에 대해 알아봅니다.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 05/12/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: dc5aa5da39e25a8db899b84a0410f07c480ee3e1
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109815280"
---
# <a name="azure-sentinel-sap-solution-security-content-reference-public-preview"></a>Azure Sentinel SAP 솔루션: 보안 콘텐츠 참조(공개 미리 보기)

이 문서에서는 [Azure Sentinel SAP 솔루션](sap-deploy-solution.md#deploy-sap-security-content)에 사용할 수 있는 보안 콘텐츠에 대해 자세히 설명합니다.

사용 가능한 보안 콘텐츠에는 기본 제공 통합 문서 및 기본 제공 분석 규칙이 포함됩니다. 검색, 검색 규칙, 위협 헌팅 및 대응 플레이 북에 사용할 SAP 관련 [관심 목록](watchlists.md)을 추가할 수도 있습니다.

> [!IMPORTANT]
> Azure Sentinel SAP 솔루션은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>


## <a name="sap---system-applications-and-products-workbook"></a>SAP - 시스템 애플리케이션 및 제품 통합 문서

[SAP - 시스템 애플리케이션 및 제품](sap-deploy-solution.md#deploy-sap-security-content) 통합 문서를 사용하여 SAP 데이터 커넥터를 통해 수집된 데이터를 시각화하고 모니터링합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/sap/sap-workbook.png" alt-text="SAP - 시스템 애플리케이션 및 제품 통합 문서.":::

자세한 내용은 [자습서: 데이터 시각화 및 모니터링](tutorial-monitor-your-data.md)을 참조하세요.

## <a name="built-in-analytics-rules"></a>기본 제공 분석 규칙

다음 표에는 Azure Sentinel 솔루션 마켓플레이스에서 배포된 Azure Sentinel SAP 솔루션에 포함된 기본 제공 [분석 규칙](sap-deploy-solution.md#deploy-sap-security-content)이 나열되어 있습니다.

### <a name="high-level-built-in-sap-solution-analytics-rules"></a>높은 수준의 기본 제공 SAP 솔루션 분석 규칙

|규칙 이름  |설명  |원본 작업  |전술  |
|---------|---------|---------|---------|
|**SAP - 높음 - 중요한 권한이 있는 사용자 변경**     |  중요한 권한이 있는 사용자의 변경 사항을 식별합니다.    <br> <br>[SAP - 권한이 있는 사용자](#users) 관심 목록에서 권한이 있는 사용자를 유지 관리합니다. |   `SU01`을 사용하여 사용자 세부 정보 / 권한을 변경합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그     |  권한 에스컬레이션, 자격 증명 액세스       |
|**SAP - 높음 - 클라이언트 구성 변경**     |     클라이언트 역할 또는 변경 기록 모드와 같은 클라이언트 구성에 대한 변경을 식별합니다.    |  `SCC4` 트랜잭션 코드를 사용하여 클라이언트 구성 변경을 수행합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그  |    방어 우회, 반출, 지속     |
|**SAP - 높음 - 디버깅 활동 중에 데이터가 변경됨**     | 디버깅 활동 중 런타임 데이터의 변경 사항을 식별합니다.         | 1. 디버그를 활성화합니다("/h"). <br>2. 변경할 필드를 선택하고 해당 값을 업데이트합니다.<br><br>**데이터 원본**: SAPcon - 감사 로그        |  실행 측면 이동       |
|**SAP - 높음 - 보안 감사 로그 비활성화**     | 보안 감사 로그의 비활성화를 식별합니다.        |  `SM19/RSAU_CONFIG`를 사용하여 보안 감사 로그를 사용하지 않도록 설정합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그 |   반출, 방어 우회, 지속      |
|**SAP - 높음 - 중요한 ABAP 프로그램 실행**     |중요한 ABAP 프로그램의 직접 실행을 식별합니다. <br><br>[SAP - 중요한 ABAP 프로그램](#programs) 관심 목록에서 ABAP 프로그램을 유지 관리합니다.        | `SE38`/`SA38`/`SE80`을 사용하여 프로그램을 직접 실행합니다. <br> <br>**데이터 원본**: SAPcon - 감사 로그      |     반출, 측면 이동, 실행    |
|**SAP - 높음 - 중요한 트랜잭션 코드 실행**     | 중요한 트랜잭션 코드의 실행을 식별합니다. <br><br>[SAP - 중요한 트랜잭션 코드](#transactions) 관심 목록에서 트랜잭션 코드를 유지 관리합니다.       |  중요한 트랜잭션 코드를 실행합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그      |    발견, 실행     |
|**SAP - 높음 - 함수 모듈이 테스트됨**     |  함수 모듈의 테스트를 식별합니다.       |   `SE37` / `SE80`을 사용하여 함수 모듈을 테스트합니다.  <br><br>**데이터 원본**: SAPcon - 감사 로그    |   수집, 방어 우회, 측면 이동      |
|**SAP - 높음 - HANA DB - 관리자 권한 할당**     |   관리자 권한 또는 역할 할당을 식별합니다.      |  관리자 역할 또는 권한이 있는 사용자를 할당합니다.  <br><br>**데이터 원본**: Linux 에이전트 - Syslog     | 권한 상승        |
|**SAP - 높음 - HANA DB - 감사 내역 정책 변경**     |  HANA DB 감사 내역 정책의 변경 사항을 식별합니다.       |     보안 정의에서 기존 감사 정책을 생성하거나 업데이트합니다. <br> <br>**데이터 원본**: Linux 에이전트 - Syslog    |  측면 이동, 방어 우회, 지속       |
|**SAP - 높음 - HANA DB - 감사 내역 비활성화**     |    HANA DB 감사 로그의 비활성화를 식별합니다.     |    HANA DB 보안 정의에서 감사 로그를 비활성화합니다. <br><br>**데이터 원본**: Linux 에이전트 - Syslog     |  지속성, 측면 이동, 방어 우회       |
|  **SAP - 높음 - HANA DB - 사용자 관리자 작업**   | 사용자 관리 작업을 식별합니다.        |  데이터베이스 사용자를 생성, 업데이트 또는 삭제합니다. <br><br>**데이터 원본**: Linux 에이전트 - Syslog*       |권한 상승         |
|**SAP - 높음 - 예기치 않은 네트워크에서 로그인**     |   예기치 않은 네트워크에서 로그인을 식별합니다. <br><br>[SAP - 네트워크](#networks) 관심 목록에서 네트워크를 유지 관리합니다.    |    네트워크 중 하나에 할당되지 않은 IP 주소에서 백 엔드 시스템에 로그인합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그    |   초기 액세스      |
|**SAP - 높음 - 중요한 함수 모듈의 RFC 실행**     | 관련 검색에 사용되는 중요한 함수 모델.    <br><br>[SAP - 중요한 함수 모듈](#module) 관심 목록에서 함수 모듈을 유지 관리합니다.   |      RFC를 사용하여 함수 모듈을 실행합니다.  <br><br>**데이터 원본**: SAPcon - 감사 로그 |  실행, 측면 이동, 발견       |
|**SAP - 높음 - 로그인한 중요한 권한이 있는 사용자**     |    중요한 권한이 있는 사용자의 대화 로그인을 식별합니다. <br><br>[SAP - 권한이 있는 사용자](#users) 관심 목록에서 권한이 있는 사용자를 유지 관리합니다.    |  `SAP*` 또는 다른 권한이 있는 사용자를 사용하여 백 엔드 시스템에 로그인합니다.  <br><br>**데이터 원본**: SAPcon - 감사 로그     |   초기 액세스, 자격 증명 액세스      |
|  **SAP - 높음 - 중요한 권한이 있는 사용자가 다른 사용자 변경**   |   다른 사용자의 중요한 권한이 있는 사용자의 변경 사항을 식별합니다.       | SU01을 사용하여 사용자 세부 정보/인증을 변경합니다.  <br><br>**데이터 원본**: SAPcon - 감사 로그     |   권한 에스컬레이션, 자격 증명 액세스       |
|**SAP - 높음 - 시스템 구성 변경**     | 시스템 구성에 대한 변경 사항을 식별합니다.        |   `SE06` 트랜잭션 코드를 사용하여 시스템 변경 옵션 또는 소프트웨어 구성 요소 수정을 조정합니다.<br><br>**데이터 원본**: SAPcon - 감사 로그 |반출, 방어 우회, 지속   |
|     |         |         |         |

### <a name="medium-level-built-in-sap-solution-analytics-rules"></a>중간 수준의 기본 제공 SAP 솔루션 분석 규칙

|규칙 이름  |설명  |원본 작업  |전술  |
|---------|---------|---------|---------|
|**SAP - 중간 - 중요한 프로필 할당**     |  사용자에게 중요한 프로필의 새 할당을 식별합니다. <br><br>[SAP - 중요한 프로필](#profiles) 관심 목록에서 중요한 프로필을 유지 관리합니다.      |    `SU01`을 사용하여 사용자에게 프로필을 할당합니다. <br><br>**데이터 원본**: SAPcon - 변경 문서 로그    |  권한 상승       |
|**SAP - 중간 - 중요한 역할 할당**     |    사용자에게 중요한 역할에 대한 새 할당을 식별합니다.     <br><br>[SAP - 중요한 역할](#roles) 관심 목록에서 중요한 역할을 유지 관리합니다.|  `SU01` / `PFCG`을 사용하여 사용자에게 역할 할당 <br><br>**데이터 원본**: SAPcon - 변경 문서 로그, 감사 로그     |   권한 상승      |
|**SAP - 중간 - 무차별 암호 대입 공격**     |     백 엔드 시스템에 대한 로그인 시도 실패에 따라 SAP 시스템에 대한 무차별 암호 대입 공격을 식별합니다.    |   예약된 시간 간격 내에 동일한 IP 주소에서 여러 시스템/클라이언트로 로그인을 시도합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그      | 자격 증명 액세스        |
|**SAP - 중간 - 중요한 권한 할당 - 새 권한 값**     | 새 사용자에 대한 중요 권한 부여 개체 값의 지정을 식별합니다.  <br><br>[SAP - 중요 권한 부여 개체](#objects) 관심 목록에서 중요 권한 부여 개체를 유지 관리합니다.      |  `PFCG`를 사용하여 새 권한 개체를 지정하거나 역할의 기존 개체를 업데이트합니다. <br><br>**데이터 원본**: SAPcon - 변경 문서 로그      |     권한 상승    |
|**SAP - 중간 - 중요한 권한 할당 - 새 사용자 할당**     |  새 사용자에 대한 중요 권한 부여 개체 값의 지정을 식별합니다. <br><br>[SAP - 중요 권한 부여 개체](#objects) 관심 목록에서 중요 권한 부여 개체를 유지 관리합니다.    |    `SU01`/`PFCG`를 사용하여 중요한 권한 값을 보유하는 역할에 새 사용자를 지정합니다. <br><br>**데이터 원본**: SAPcon - 변경 문서 로그    |  권한 상승       |
|**SAP - 중간 - 디버깅 활동**     |  모든 디버깅 관련 활동을 식별합니다.       |시스템에서 디버그("/h") 활성화, 활성 프로세스 디버그, 소스 코드에 중단점 추가 등의 작업을 수행합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그 |   검색      |
|**SAP - 중간 - IP별 다중 로그온**     |  예약된 시간 간격 내에 동일한 IP 주소에서 여러 사용자의 로그인을 식별합니다.       |    동일한 IP 주소를 통해 여러 사용자를 사용하여 로그인합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그 | 초기 액세스        |
|**SAP - 중간 - 사용자에 의한 다중 로그온**     | 예약된 시간 간격 내에 여러 터미널에서 동일한 사용자의 로그인을 식별합니다.  <br><br>SAP 버전 7.5 이상의 경우 감사 SAL 메서드를 통해서만 사용할 수 있습니다.      |   다른 IP 주소를 사용하여 동일한 사용자로 로그인합니다.   <br><br>**데이터 원본**: SAPcon - 감사 로그   |  PreAttack, 자격 증명 액세스, 초기 액세스, 수집       |
|**SAP - 중간 - 보안 감사 로그 구성 변경**     |  보안 감사 로그 구성의 변경 사항을 식별합니다.       |   `SM19`/`RSAU_CONFIG`를 사용하여 필터, 상태, 기록 모드 등과 같은 보안 감사 로그 구성을 변경합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그      |    지속성, 반출, 방어 우회     |
|**SAP - 중간 - 트랜잭션이 잠금 해제됨**     |트랜잭션 잠금 해제를 식별합니다.         |     `SM01`/`SM01_DEV`/`SM01_CUS`를 사용하여 트랜잭션 코드를 잠금 해제합니다. <br><br>**데이터 원본**: SAPcon - 감사 로그   |  지속성,실행       |
|     |         |         |         |

### <a name="low-level-built-in-sap-solution-analytics-rules"></a>낮은 수준의 기본 제공 SAP 솔루션 분석 규칙

|규칙 이름  |설명  |원본 작업  |전술  |
|---------|---------|---------|---------|
|**SAP - 낮음 - 사용자가 여러 번 암호 변경**     |   사용자가 여러 번 암호 변경한 것을 식별합니다.      |   사용자 암호 변경 <br><br>**데이터 원본**: SAPcon - 감사 로그      |    자격 증명 액세스     |
|**SAP - 낮음 - 대화 로그온으로 중요한 테이블에 직접 액세스**     |   대화 로그인을 통해 일반 테이블 액세스를 식별합니다.      |  `SE11`/`SE16`/`SE16N`을 사용하여 테이블 콘텐츠를 엽니다. <br><br>**데이터 원본**: SAPcon - 감사 로그      |    검색     |
|     |         |         |         |

## <a name="available-watchlists"></a>사용 가능한 관심 목록

다음 표에는 Azure Sentinel SAP 솔루션에 사용할 수 있는 [관심 목록](sap-deploy-solution.md#deploy-sap-security-content)과 각 관심 목록의 필드가 나열되어 있습니다.

이러한 관심 목록은 Azure Sentinel SAP 연속적인 위협 모니터링 솔루션에 대한 구성을 제공하며 https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists 의 Azure Sentinel GitHub 리포지토리에서 액세스할 수 있습니다.


|관심 목록 이름  |설명 및 필드  |
|---------|---------|
|<a name="objects"></a>**SAP - 중요 권한 부여 개체**     |  할당을 관리해야 하는 중요 권한 부여 개체입니다.     <br><br>- **AuthorizationObject**: `S_DEVELOP`, `S_TCODE` 또는 `Table TOBJ`와 같은 SAP 인증 개체 <br>- **AuthorizationField**: `OBJTYP` 또는 `TCD`와 같은 SAP 인증 필드    <br>- **AuthorizationValue**: `DEBUG`와 같은 SAP 인증 필드 값       <br>- **ActivityField**: SAP 활동 필드. 대부분의 경우 이 값은 `ACTVT`입니다. **활동** 이 없거나 **Activity** 필드만 있는 권한 부여 개체의 경우 `NOT_IN_USE`로 채워집니다.        <br>- **Activity**: 권한 부여 개체에 따른 SAP 활동, 예: `01`: 만들기; `02`: 변경; `03`: 디스플레이 등      <br>- **Description**: 이해할 수 있는 중요 권한 부여 개체 설명 |
|**SAP - 제외된 네트워크** | 웹 디스패처, 터미널 서버 등을 무시하는 것과 같이 제외된 네트워크의 내부 유지 관리를 위해 사용합니다. <br><br>-**Network**: 네트워크 IP 주소 또는 범위(예: `111.68.128.0/17`) <br>-**Description**: 이해할 수 있는 네트워크 설명|
|**SAP 제외된 사용자** |시스템에 로그인되어 있고 무시해야 하는 시스템 사용자입니다. 예를 들어 동일한 사용자의 다중 로그인에 대한 경고입니다. <br><br>- **User**: SAP 사용자 <br>-**Description**: 이해할 수 있는 사용자 설명 |
|<a name="networks"></a>**SAP-네트워크**     |  무단 로그인 식별을 위한 내부 및 유지 관리 네트워크입니다.      <br><br>- **Network**: 네트워크 IP 주소 또는 범위(예: `111.68.128.0/17`)     <br>- **Description**: 이해할 수 있는 네트워크 설명|
|<a name="users"></a>**SAP - 권한 있는 사용자**     |   추가 제한이 있는 권한이 있는 사용자.  <br><br>- **User**: `DDIC` 또는 `SAP`와 같은 ABAP 사용자 <br>- **Description**: 이해할 수 있는 사용자 설명 |
|<a name= "programs"></a>**SAP - 중요한 ABAP 프로그램**     |      실행을 관리해야 하는 중요한 ABAP 프로그램(보고서)입니다.   <br><br>- **ABAPProgram**: ABAP 프로그램 또는 보고서(예: `RSPFLDOC`)     <br>- **Description**: 이해할 수 있는 프로그램 설명|
|<a name="module"></a>**SAP - 중요한 함수 모듈**     |   무단 로그인 식별을 위한 내부 및 유지 관리 네트워크입니다.      <br><br>- **FunctionModule**: ABAP 함수 모듈(예: `RSAU_CLEAR_AUDIT_LOG`)       <br>- **Description**: 이해할 수 있는 모듈 설명     |
|<a name="profiles"></a>**SAP - 중요한 프로필**     |  할당을 관리해야 하는 중요한 프로필입니다.     <br><br>- **Profile**: `SAP_ALL` 또는 `SAP_NEW`와 같은 SAP 인증 프로필      <br>- **Description**: 이해할 수 있는 프로필 설명|
|<a name="tables"></a>**SAP - 중요한 표**     |  액세스를 관리해야 하는 중요한 테이블입니다.  <br><br>- **Table**: ABAP 사전 테이블(예: `USR02` 또는 `PA008`) <br>- **Description**: 이해할 수 있는 테이블 설명 |
|<a name="roles"></a>**SAP - 중요한 역할**     |  할당을 관리해야 하는 중요한 역할입니다.    <br><br>- **Role**: SAP 승인 역할(예: `SAP_BC_BASIS_ADMIN`)  <br>- **Description**: 이해할 수 있는 역할 설명 |
|<a name="transactions"></a>**SAP - 중요한 트랜잭션**     |     실행을 관리해야 하는 중요한 트랜잭션입니다.  <br><br>- **TransactionCode**: SAP 트랜잭션 코드(예: `RZ11`) <br>- **Description**: 이해할 수 있는 코드 설명 |
|<a name="systems"></a>**SAP - 시스템**     |    역할 및 용도에 따른 SAP 시스템의 환경을 설명합니다.<br><br>- **SystemID**: SYSID(SAP 시스템 ID) <br>- **SystemRole**: SAP 시스템 역할, 다음 값 중 하나: `Sandbox`, `Development`, `Quality Assurance`, `Training`, `Production` <br>- **SystemUsage**: SAP 시스템 사용량, 다음 값 중 하나: `ERP`, `BW`, `Solman`, `Gateway`, `Enterprise Portal`        |
| | |


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [자습서: SAP용 Azure Sentinel 솔루션 배포](sap-deploy-solution.md)
- [Azure Sentinel SAP 솔루션 로그 참조](sap-solution-log-reference.md)
- [온-프레미스에 Azure Sentinel SAP 데이터 커넥터 배포](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP 솔루션 세부 SAP 요구 사항](sap-solution-detailed-requirements.md)