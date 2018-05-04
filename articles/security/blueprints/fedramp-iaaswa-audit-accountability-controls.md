---
title: Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화 - 감사 및 책임
description: FedRAMP 웹 응용 프로그램 자동화 - 감사 및 책임
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: acb0677d48084108a9a5219f92dead10948228c8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="audit-and-accountability-au"></a>AU(감사 및 책임)

> [!NOTE]
> 이러한 제어는 NIST와 미국 상무부에 의해 NIST 특별 출판물 800-53 개정 4의 일부로 정의됩니다. 각 제어에 대한 테스트 절차 및 지침에 대한 정보는 NIST 800-53 개정 4를 참조하세요.

## <a name="nist-800-53-control-au-1"></a>NIST 800-53 제어 AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>감사 및 책임 정책 및 절차

**AU-1** 조직은 목적, 범위, 역할, 책임, 관리 약속, 조직 엔터티 간 조정 및 준수를 다루는 감사 및 책임 정책 및 감사 및 책임 정책과 관련 감사 및 책임 제어의 구현을 용이하게 하는 절차를 개발, 문서화하여 [할당: 조직에서 정의한 담당자 또는 역할]에게 배포하고, [할당: 조직에서 정의한 빈도]마다 현재 감사 및 책임 정책을 검토하고 업데이트하며 [할당: 조직에서 정의한 빈도]마다 감사 및 책임 절차를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 감사 및 책임 정책 및 절차가 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-2a"></a>NIST 800-53 제어 AU-2.a

#### <a name="audit-events"></a>감사 이벤트

**AU-2.a** 조직은 정보 시스템이 [할당: 조직에서 정의한 감사 가능 이벤트] 이벤트를 감사할 수 있는지 결정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에 대한 감사 기능은 Azure Monitor와 Log Analytics 서비스에서 제공합니다. Azure Monitor는 배포된 리소스와 관련된 활동에 대한 자세한 감사 로그를 제공합니다. 이러한 로그 및 OS 수준의 로그는 Log Analytics에 의해 수집되어 OMS 리포지토리에 저장됩니다. Log Analytics는 이 솔루션으로 배포된 여러 리소스의 감사 데이터를 상호 연결시켜 고객이 배포한 웹 응용 프로그램으로 확장할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-2b"></a>NIST 800-53 제어 AU-2.b

#### <a name="audit-events"></a>감사 이벤트

**AU-2.b** 조직은 감사 관련 정보를 필요로 하는 다른 조직 엔터티를 사용하여 보안 감사 기능을 조정하여 상호 지원을 강화하고 감사 가능 이벤트 선택을 돕습니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 감사 가능 이벤트를 결정하는 설정된 엔터프라이즈 수준 프로세스를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-2c"></a>NIST 800-53 제어 AU-2.c

#### <a name="audit-events"></a>감사 이벤트

**AU-2.c** 조직은 감사 가능 이벤트가 보안 인시던트에 대한 사후 조사를 지원하기에 충분한 것으로 간주되는 이유에 대한 근거를 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 감사하는 이벤트에는 이벤트 발생 시기, 이벤트 원본, 이벤트 결과 및 보안 인시던트 조사를 지원하는 기타 세부 정보를 확인하기에 충분한 정보가 포함되어 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-2d"></a>NIST 800-53 제어 AU-2.d

#### <a name="audit-events"></a>감사 이벤트

**AU-2.d** 조직은 [할당: 조직에서 정의한, 식별된 각 이벤트에 대한 감사 빈도에 따라(또는 필요 시) 감사된 이벤트(AU-2 a에 정의된 감사 가능 이벤트의 하위 집합)] 이벤트가 정보 시스템 내에서 감사되어야 한다고 결정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 감사하는 이벤트에는 배포된 리소스, OS 수준 로그, Active Directory 로그 및 SQL Server 로그에 대해 Azure 활동 로그에서 감사하는 이벤트가 포함됩니다. 고객은 업무 요구 사항에 맞게 감사할 추가 이벤트를 선택할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-2-3"></a>NIST 800-53 제어 AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>감사 이벤트 | 검토 및 업데이트

**AU-2 (3)** 조직은 [할당: 조직에서 정의한 빈도]마다 감사된 이벤트를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 정의된 감사 이벤트 집합에 대해 설정된 엔터프라이즈 수준 주기적 검토 및 업데이트 프로세스를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-3"></a>NIST 800-53 제어 AU-3

#### <a name="content-of-audit-records"></a>감사 레코드의 콘텐츠

**AU-3** 정보 시스템은 발생한 이벤트의 유형, 이벤트가 발생한 시기, 이벤트가 발생한 위치, 이벤트 원본, 이벤트 결과 및 이벤트와 관련된 개인 또는 주체의 ID를 설정하는 정보가 포함된 감사 레코드를 생성합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Azure, Windows Server 및 SQL Server의 기본 제공 감사 기능을 사용합니다. 이러한 감사 솔루션은 이 제어의 요구 사항을 충족하기 위해 충분한 세부 정보가 포함된 감사 레코드를 캡처합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-3-1"></a>NIST 800-53 제어 AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>감사 레코드의 콘텐츠 | 추가 감사 정보

**AU-3 (1)** 정보 시스템은 [할당: 조직에서 정의한 추가 세부 정보]와 같은 추가 정보를 포함하는 감사 레코드를 생성합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Activity Log 이벤트는 20여 가지의 감사 정보 필드를 포함하는 상세한 스키마를 사용합니다. 이 청사진은 활동 로그 외에도 Windows 로그, Linux 로그, Azure 진단 로그 및 고객 로그를 비롯한 다양한 데이터 원본 집합을 지원하는 Log Analytics 솔루션을 배포합니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-3-2"></a>NIST 800-53 제어 AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>감사 레코드의 콘텐츠 | 계획된 감사 레코드 콘텐츠의 중앙 집중식 관리

**AU-3 (2)** 정보 시스템은 [할당: 조직에서 정의한 정보 시스템 구성 요소]에 의해 생성된 감사 레코드에 캡처되는 콘텐츠의 중앙 집중식 관리 및 구성을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 모든 가상 머신은 배포된 Active Directory 도메인에 가입됩니다. 도메인에 가입된 모든 가상 머신은 OS 수준의 감사 시스템 구성을 중앙에서 관리하도록 구성할 수 있는 그룹 정책을 구현합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-4"></a>NIST 800-53 제어 AU-4

#### <a name="audit-storage-capacity"></a>감사 저장소 용량

**AU-4** 조직은 [할당: 조직에서 정의한 감사 레코드 저장소 요구 사항]에 따라 감사 레코드 저장소 용량을 할당합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 1년 동안 감사 레코드를 보존하기에 충분한 저장소 용량을 할당합니다. 모든 감사 레코드는 Log Analytics에 의해 수집되어 1년 동안 보존됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-5a"></a>NIST 800-53 제어 AU-5.a

#### <a name="response-to-audit-processing-failures"></a>감사 처리 실패에 대한 응답

**AU-5.a** 정보 처리 시스템은 감사 처리가 실패한 경우 [할당: 조직에서 정의한 담당자 또는 역할]에 경고합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Monitor 및 Log Analytics의 서비스 상태는 Azure Portal의 Azure 상태 웹 사이트 및 서비스 상태 블레이드에서 사용할 수 있습니다. Log Analytics를 통해 경고를 구성하여 다른 유형의 감사 처리 실패를 알릴 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-5b"></a>NIST 800-53 제어 AU-5.b

#### <a name="response-to-audit-processing-failures"></a>감사 처리 실패에 대한 응답

**AU-5.b** 정보 시스템은 [할당: 조직에서 정의한 수행할 작업(예: 정보 시스템 종료, 가장 오래된 감사 레코드 덮어쓰기, 감사 레코드 생성 중지)]과 같은 추가 작업을 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에 의해 배포된 리소스에서 생성한 모든 감사 레코드는 Log Analytics에 의해 수집되어 1년 동안 보존됩니다. 이 감사 레코드 저장소에 대한 저장소 할당은 충분한 용량을 사용할 수 있도록 동적으로 할당됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-5-1"></a>NIST 800-53 제어 AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>감사 처리 실패에 대한 응답 | 감사 저장소 용량

**AU-5 (1)** 정보 시스템은 할당된 감사 레코드 저장소 용량이 리포지토리 최대 감사 레코드 저장소 용량의 [할당: 조직에서 정의한 비율]에 도달할 때 [할당: 조직에서 정의한 기간] 내에서 [할당: 조직에서 정의한 담당자, 역할 및/또는 위치]에 경고를 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에 의해 배포된 리소스에서 생성한 모든 감사 레코드는 Log Analytics에 의해 수집되어 1년 동안 보존됩니다. 이 감사 레코드 저장소에 대한 저장소 할당은 충분한 용량을 사용할 수 있도록 동적으로 할당됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-5-2"></a>NIST 800-53 제어 AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>감사 처리 실패에 대한 응답 | 실시간 경고

**AU-5 (2)** 정보 시스템은 [할당: 조직에서 정의한 실시간 경고가 필요한 감사 실패 이벤트] 감사 실패 이벤트가 발생할 경우 [할당: 조직에서 정의한 실시간 기간]에 [할당: 조직에서 정의한 담당자, 역할 및/또는 위치]에 경고를 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure의 서비스 상태는 Azure Portal의 서비스 상태 블레이드에서 사용할 수 있습니다. Log Analytics를 통해 경고를 구성하여 다른 유형의 감사 처리 실패를 알릴 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-6a"></a>NIST 800-53 제어 AU-6.a

#### <a name="audit-review-analysis-and-reporting"></a>감사 검토, 분석 및 보고

**AU-6.a** 조직은 [할당: 조직에서 정의한 빈도]마다 [할당: 조직에서 정의한 부적절하거나 비정상적인 활동]의 징후에 대해 정보 시스템 감사 레코드를 검토하고 분석합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함)에 대한 감사 레코드를 검토하고 분석할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-6b"></a>NIST 800-53 제어 AU-6.b

#### <a name="audit-review-analysis-and-reporting"></a>감사 검토, 분석 및 보고

**AU-6.b** 조직은 결과를 [할당: 조직에서 정의한 담당자 또는 역할]에 보고합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대해 부적절하거나 비정상적인 활동(AU-06.a에 정의됨)에 대한 결과를 보고할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-6-1"></a>NIST 800-53 제어 AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>감사 검토, 분석 및 보고 | 프로세스 통합

**AU-6 (1)** 조직은 자동화된 메커니즘을 사용하여 감사 검토, 분석 및 보고 프로세스를 통합하여 의심스러운 활동에 대해 조사 및 대응하도록 조직 프로세스를 지원합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 엔터프라이즈 수준 중앙 집중식 감사 검토, 분석 및 보고 기능을 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-6-3"></a>NIST 800-53 제어 AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>감사 검토, 분석 및 보고 | 감사 리포지토리 상호 연결

**AU-6 (3)** 조직은 서로 다른 리포지토리에서 감사 레코드를 분석하고 상호 연결시켜 조직 전체의 상황 인식을 지원합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Log Analytics 솔루션을 구현하여 배포된 리소스 전반의 감사 데이터를 중앙 집중화함으로써 조직 전체의 상황 인식을 지원합니다. 고객은 Log Analytics를 다른 시스템과 추가로 통합할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-6-4"></a>NIST 800-53 제어 AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>감사 검토, 분석 및 보고 | 중앙 검토 및 분석

**AU-6 (4)** 정보 시스템은 시스템 내의 여러 구성 요소에 대한 감사 레코드를 중앙에서 검토하고 분석할 수 있는 기능을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Log Analytics 솔루션을 구현하여 배포된 리소스 전반의 감사 데이터를 중앙 집중화함으로써 중앙 집중식 검토, 분석 및 보고를 지원합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-6-5"></a>NIST 800-53 제어 AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>감사 검토, 분석 및 보고 | 통합/검색 및 모니터링 기능

**AU-6 (5)** 조직은 감사 레코드 분석을 [선택(하나 이상): 취약성 검색 정보, 성능 데이터, 정보 시스템 모니터링 정보, [할당: 조직에서 정의한 데이터/다른 원본에서 수집한 정보]] 분석과 통합하여 부적절하거나 비정상적인 활동을 식별하는 기능을 더욱 향상시킵니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 보안 및 감사 솔루션을 배포합니다. 이 솔루션은 보안 상태에 대한 종합적인 보기를 제공합니다. 보안 및 감사 대시보드는 배포된 Log Analytics 솔루션에서 사용 가능한 데이터를 사용하여 배포된 리소스의 보안 상태에 대한 높은 수준의 통찰력을 제공하고 기준 및 패치 평가를 통해 로그 데이터 및 취약성 데이터를 통합합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-6-6"></a>NIST 800-53 제어 AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>감사 검토, 분석 및 보고 | 물리적 모니터링과의 상관 관계

**AU-6 (6)** 조직은 감사 레코드의 정보를 물리적 액세스 모니터링으로 얻은 정보와 상호 연결시켜 의심스럽거나 부적절하거나 비정상적이거나 악의적인 활동을 식별하는 기능을 더욱 향상시킵니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | 고객은 Azure 데이터 센터의 시스템 리소스에 물리적으로 액세스할 권한이 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure의 SIM 팀은 관련된 물리적 모니터링 데이터를 사용하고 이를 감사 레코드와 상호 연결하여 물리적 인시던트가 검색될 때 관련된 논리적인 위반 또는 의심스러운 동작이 있는지 여부를 확인합니다. |


 ### <a name="nist-800-53-control-au-6-7"></a>NIST 800-53 제어 AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>감사 검토, 분석 및 보고 | 허용되는 작업

**AU-6 (7)** 조직은 감사 정보의 검토, 분석 및 보고와 관련된 각 [선택(하나 이상): 정보 시스템 프로세스, 역할, 사용자]에 대해 허용된 작업을 지정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진이 배포한 Windows 가상 머신은 사용자가 감사 정보와 관련하여 수행할 수 있는 작업을 제한하는 OS 수준의 권한을 구현합니다. Azure에서 사용자 또는 사용자 그룹을 역할(예: 소유자, 참가자, 읽기 권한자 또는 사용자 지정 역할)에 할당하여 Log Analytics를 비롯한 모든 리소스 또는 배포 솔루션과 관련하여 사용 가능한 작업을 제한할 수 있습니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-6-10"></a>NIST 800-53 제어 AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>감사 검토, 분석 및 보고 | 감사 수준 조정

**AU-6 (10)** 조직은 법 적용 정보, 인텔리전스 정보 또는 정보의 기타 신뢰할 수 있는 원본을 기반으로 위험이 변경되는 경우 정보 시스템 내에서 감사 검토, 분석 및 보고의 수준을 조정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 법 적용, 인텔리전스 또는 기타 신뢰할 수 있는 원본에서 제공한 정보를 기반으로 위험이 변경되는 경우 고객 배포 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함)에 대한 감사 검토, 분석 및 보고 수준을 조정할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-7a"></a>NIST 800-53 제어 AU-7.a

#### <a name="audit-reduction-and-report-generation"></a>감사 감소 및 보고서 생성

**AU-7.a** 정보 시스템은 주문형 감사 검토, 분석, 보고 요구 사항 및 보안 인시던트에 대한 사후 조사를 지원하는 감사 감소 및 보고서 생성 기능을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Log Analytics 솔루션을 구현합니다. Log Analytics는 관리형 리소스의 데이터를 중앙 리포지토리로 수집하여 Azure에 대한 모니터링 서비스를 제공합니다. 수집된 데이터는 경고, 분석 및 내보내기에 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-7b"></a>NIST 800-53 제어 AU-7.b

#### <a name="audit-reduction-and-report-generation"></a>감사 감소 및 보고서 생성

**AU-7.b** 정보 시스템은 감사 레코드의 원래 콘텐츠나 시간 순서를 변경하지 않는 감사 감소 및 보고서 생성 기능을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Log Analytics 솔루션을 구현합니다. Log Analytics는 관리형 리소스의 데이터를 중앙 리포지토리로 수집하여 Azure에 대한 모니터링 서비스를 제공합니다. Log Analytics에서 수집할 때 감사 레코드의 콘텐츠 및 시간 순서는 변경되지 않습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-7-1"></a>NIST 800-53 제어 AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>감사 감소 및 보고서 생성 | 자동 처리

**AU-7 (1)** 정보 시스템은 [할당: 조직에서 정의한 감사 레코드 내의 감사 필드]를 기반으로 관심 있는 이벤트에 대한 감사 레코드를 처리하는 기능을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Log Analytics 솔루션을 구현합니다. Log Analytics는 관리형 리소스의 데이터를 중앙 리포지토리로 수집하여 Azure에 대한 모니터링 서비스를 제공합니다. 수집된 데이터는 경고, 분석 및 내보내기에 사용할 수 있습니다. Log Analytics에는 리포지토리에 저장된 데이터를 추출하는 강력한 쿼리 언어가 포함되어 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-8a"></a>NIST 800-53 제어 AU-8.a

#### <a name="time-stamps"></a>타임스탬프

**AU-8.a** 정보 시스템은 내부 시스템 클록을 사용하여 감사 레코드에 대한 타임스탬프를 생성합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진이 배포한 리소스는 내부 시스템 클록을 사용하여 감사 레코드를 위한 타임스탬프를 생성합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-8b"></a>NIST 800-53 제어 AU-8.b

#### <a name="time-stamps"></a>타임스탬프

**AU-8.b** 정보 시스템은 UTC(협정 세계시) 또는 GMT(그리니치 표준시)로 매핑될 수 있고 [할당: 조직에서 정의한 시간 측정 세분성]을 충족시키는 감사 레코드의 타임스탬프를 기록합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진이 배포한 리소스는 내부 시스템 클록을 사용하여 감사 레코드를 위한 타임스탬프를 생성합니다. 타임스탬프는 UTC로 기록됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-8-1a"></a>NIST 800-53 제어 AU-8 (1).a

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>타임스탬프 | 신뢰할 수 있는 시간 원본과의 동기화

**AU-8 (1).a** 정보 시스템은 내부 정보 시스템 클록을 [할당: 조직에서 정의한 빈도]마다 [할당: 조직에서 정의한 신뢰할 수 있는 시간 원본]과 비교합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진이 배포한 리소스는 내부 시스템 클록을 사용하여 감사 레코드를 위한 타임스탬프를 생성합니다. 내부 시스템 클록은 신뢰할 수 있는 시간 원본과 동기화되도록 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-8-1b"></a>NIST 800-53 제어 AU-8 (1).b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>타임스탬프 | 신뢰할 수 있는 시간 원본과의 동기화

**AU-8 (1).b** 정보 시스템은 시차가 [할당: 조직에서 정의한 기간]보다 클 경우 내부 시스템 클록을 신뢰할 수 있는 시간 원본과 동기화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진이 배포한 리소스는 내부 시스템 클록을 사용하여 감사 레코드를 위한 타임스탬프를 생성합니다. 내부 시스템 클록은 신뢰할 수 있는 시간 원본과 동기화되도록 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-9"></a>NIST 800-53 제어 AU-9

#### <a name="protection-of-audit-information"></a>감사 정보 보호

**AU-9** 정보 시스템은 감사 정보 및 감사 도구를 무단 액세스, 수정, 삭제로부터 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 논리적 액세스 제어는 무단 액세스, 수정, 삭제로부터 이 청사진 내의 감사 정보 및 도구를 보호하는 데 사용됩니다. Azure Active Directory는 역할 기반 그룹 멤버 자격을 사용하여 승인된 논리적 액세스를 적용합니다. 감사 정보를 보고 감사 도구를 사용하는 기능은 이러한 권한이 필요한 사용자로 제한될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-9-2"></a>NIST 800-53 제어 AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>감사 정보 보호 | 개별 물리적 시스템/구성 요소에 대한 감사 백업

**AU-9 (2)** 정보 시스템은 [할당: 조직에서 정의한 빈도]마다 감사 레코드를 감사 중인 시스템 또는 구성 요소와 물리적으로 다른 시스템 또는 시스템 구성 요소에 백업합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Log Analytics 서비스를 구현합니다. 배포된 VM 및 Azure 진단 저장소 계정은 Log Analytics에 연결된 원본이며 해당 원본과 별도로 보존됩니다. 데이터는 거의 실시간으로 Log Analytics에 의해 수집됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-9-3"></a>NIST 800-53 제어 AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>감사 정보 보호 | 암호화 보호

**AU-9 (3)** 정보 시스템은 감사 정보 및 감사 도구의 무결성을 보호하기 위해 암호화 메커니즘을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Log Analytics 서비스를 구현합니다. Log Analytics는 Azure 인증을 통해 인증서 및 데이터 무결성의 유효성을 검사하여 들어오는 데이터가 신뢰할 수 있는 원본에서 온 것임을 보장합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-9-4"></a>NIST 800-53 제어 AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>감사 정보 보호 | 권한 있는 사용자의 하위 집합별 액세스

**AU-9 (4)** 조직은 [할당: 조직에서 정의한 권한 있는 사용자의 하위 집합]에만 감사 기능 관리에 대한 액세스 권한을 부여합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 논리적 액세스 제어는 무단 액세스, 수정, 삭제로부터 이 청사진 내의 감사 정보 및 도구를 보호하는 데 사용됩니다. Azure Active Directory는 역할 기반 그룹 멤버 자격을 사용하여 승인된 논리적 액세스를 적용합니다. 감사 정보를 보고 감사 도구를 사용하는 기능은 이러한 권한이 필요한 사용자로 제한될 수 있습니다.
 |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-10"></a>NIST 800-53 제어 AU-10

#### <a name="non-repudiation"></a>부인 방지

**AU-10** 정보 시스템은 [할당: 조직에서 정의한 부인 방지의 대상이 되는 작업] 수행을 잘못 부인한 개인(또는 개인을 대신하여 동작하는 프로세스)을 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에 대한 감사 기능은 Azure Monitor와 Log Analytics 서비스에서 제공합니다. Azure Monitor는 배포된 리소스와 관련된 활동에 대한 자세한 감사 로그를 제공합니다. 이러한 로그 및 OS 수준의 로그는 Log Analytics에 의해 수집되어 OMS 리포지토리에 저장됩니다. 이 로그에는 정보 시스템 이벤트에 대한 자세한 레코드가 포함되어 있으며 부인 방지로부터 보호하는 데 도움이 될 수 있습니다. 또한 역할 기반 액세스 제어를 사용하여 로그 데이터에 대한 액세스가 제한되므로 로그 데이터의 무단 수정 또는 삭제를 방지할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-11"></a>NIST 800-53 제어 AU-11

#### <a name="audit-record-retention"></a>감사 레코드 보존

**AU-11** 조직은 보안 인시던트에 대한 사후 조사를 지원하고 규제 및 조직 정보 보존 요구 사항을 충족시키기 위해 [할당: 레코드 보존 정책에 따라 조직에서 정의한 기간] 동안 감사 레코드를 보존합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Log Analytics 서비스를 구현합니다. Log Analytics는 관리형 리소스의 데이터를 중앙 리포지토리로 수집하여 Azure에 대한 모니터링 서비스를 제공합니다. 수집된 데이터는 Log Analytics 구성별로 1년 동안 보존됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-12a"></a>NIST 800-53 제어 AU-12.a

#### <a name="audit-generation"></a>감사 생성

**AU-12.a** 정보 시스템은 AU-2 a의 정의된 감사 가능 이벤트에 대한 감사 레코드 생성 기능을 [할당: 조직에서 정의한 정보 시스템 구성 요소]에 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 감사하는 이벤트에는 배포된 리소스, OS 수준 로그, Active Directory 로그 및 SQL Server 로그에 대해 Azure 활동 로그에서 감사하는 이벤트가 포함됩니다. 고객은 업무 요구 사항에 맞게 감사할 추가 이벤트를 선택할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-12b"></a>NIST 800-53 제어 AU-12.b

#### <a name="audit-generation"></a>감사 생성

**AU-12.b** 정보 시스템은 [할당: 조직에서 정의한 담당자 또는 역할]이 정보 시스템의 특정 구성 요소에서 감사할 감사 가능 이벤트를 선택하도록 허용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 감사 기능에 대한 액세스는 Azure 내에서 및 가상 머신 OS 수준에서 역할 기반 액세스 제어를 사용하여 제한됩니다. 이 청사진에서 배포한 리소스에 의해 감사하도록 선택된 이벤트의 구성을 적절한 역할 기준 권한 부여를 사용한 사용자가 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-au-12c"></a>NIST 800-53 제어 AU-12.c

#### <a name="audit-generation"></a>감사 생성

**AU-12.c** 정보 시스템은 AU-2.d에 정의된 이벤트에 대한 감사 레코드를 AU-3에 정의된 콘텐츠를 사용하여 생성합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 감사하는 이벤트에는 배포된 리소스, OS 수준 로그, Active Directory 로그 및 SQL Server 로그에 대해 Azure 활동 로그에서 감사하는 이벤트가 포함됩니다. 고객은 업무 요구 사항에 맞게 감사할 추가 이벤트를 선택할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-12-1"></a>NIST 800-53 제어 AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>감사 생성 | 시스템 수준/시간 관련 감사 내역

**AU-12 (1)** 정보 시스템은 [할당: 조직에서 정의한 정보 시스템 구성 요소]의 감사 레코드를 [할당: 조직에서 정의한, 감사 내역에서 개별 레코드의 타임스탬프 간의 관계에 대한 허용 수준] 내에서 시간 상관 관계가 있는 시스템 전체(논리적 또는 물리적) 감사 내역으로 컴파일합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Log Analytics 서비스를 구현합니다. Log Analytics는 관리형 리소스의 데이터를 중앙 리포지토리로 수집하여 Azure에 대한 모니터링 서비스를 제공합니다. 감사 레코드 타임스탬프는 변경되지 않으므로 감사 내역은 시간과 상관 관계가 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-au-12-3"></a>NIST 800-53 제어 AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>감사 생성 | 권한 있는 개인에 따른 변경

**AU-12 (3)** 정보 시스템은 [할당: 조직에서 정의한 담당자 또는 역할]에게 [할당: 조직에서 정의한 시간 임계값] 내에서 [할당: 조직에서 정의한 선택 가능 이벤트 조건]을 기반으로 [할당: 조직에서 정의한 정보 시스템 구성 요소]에서 수행될 감사를 변경할 수 있는 기능을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 감사 기능에 대한 액세스는 Azure 내에서 및 가상 머신 OS 수준에서 역할 기반 액세스 제어를 사용하여 제한됩니다. 이 청사진에서 배포한 리소스에 의해 감사하도록 선택된 이벤트의 구성을 적절한 역할 기준 권한 부여를 사용한 사용자가 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |
