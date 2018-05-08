---
title: Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화 - 인시던트 응답
description: FedRAMP 웹 응용 프로그램 자동화 - 인시던트 응답
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 48acbdbb-1579-4db9-8e4a-3c12fa558842
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: bcdb2b151c4d14af59e8133bd871ef6c3c0b9f73
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="incident-response-ir"></a>인시던트 응답(IR)

> [!NOTE]
> 이러한 제어는 NIST와 미국 상무부에 의해 NIST 특별 출판물 800-53 개정 4의 일부로 정의됩니다. 각 제어에 대한 테스트 절차 및 지침에 대한 정보는 NIST 800-53 개정 4를 참조하세요.

## <a name="nist-800-53-control-ir-1"></a>NIST 800-53 제어 IR-1

#### <a name="incident-response-policy-and-procedures"></a>인시던트 응답 정책 및 절차

**IR-1** 조직은 목적, 범위, 역할, 책임, 관리 약속, 조직 엔터티 간 조정 및 준수를 다루는 인시던트 응답 정책 및 인시던트 응답 정책과 관련 인시던트 응답의 구현을 용이하게 하는 절차를 개발, 문서화하여 [할당: 조직이 정의한 담당자 또는 역할]에게 배포하고, [할당: 조직이 정의한 빈도]마다 현재 인시던트 응답 정책을 검토하고 업데이트하며 [할당: 조직이 정의한 빈도]마다 인시던트 응답 절차를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 정책 및 절차에서 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-2a"></a>NIST 800-53 제어 IR-2.a

#### <a name="incident-response-training"></a>인시던트 응답 교육

**IR-2.a** 조직은 인시던트 응답 역할이나 책임이 있다고 가정하고 [할당: 조직이 정의한 기간] 내에 할당된 역할과 책임에 부합하는 정보 시스템 사용자에게 인시던트 응답 교육을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 교육으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-2b"></a>NIST 800-53 제어 IR-2.b

#### <a name="incident-response-training"></a>인시던트 응답 교육

**IR-2.b** 조직은 정보 시스템 변경에 필요한 경우 할당된 역할과 책임에 부합하는 정보 시스템 사용자에게 인시던트 응답 교육을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 교육으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-2c"></a>NIST 800-53 제어 IR-2.c

#### <a name="incident-response-training"></a>인시던트 응답 교육

**IR-2.c** 조직은 [할당: 조직이 정의한 빈도]마다 할당된 역할과 책임에 부합하는 정보 시스템 사용자에게 인시던트 응답 교육을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 교육으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-2-1"></a>NIST 800-53 제어 IR-2(1)

#### <a name="incident-response-training--simulated-events"></a>인시던트 응답 교육 | 시뮬레이트된 이벤트

**IR-2 (1)** 조직은 위기 상황에서 담당자가 효과적으로 대응할 수 있도록 시뮬레이트된 이벤트를 인시던트 응답 교육에 통합합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 교육은 시뮬레이트된 이벤트를 포함할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-2-2"></a>NIST 800-53 제어 IR-2(2)

#### <a name="incident-response-training--automated-training-environments"></a>인시던트 응답 교육 | 자동화된 학습 환경

**IR-2 (2)** 조직은 자동화 메커니즘을 사용하여 보다 철저하고 현실적인 인시던트 응답 학습 환경을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 교육은 자동화된 교육 환경을 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-3"></a>NIST 800-53 제어 IR-3

#### <a name="incident-response-testing"></a>인시던트 응답 테스트

**IR-3** 조직은 [할당: 조직이 정의한 빈도]마다 [할당: 조직이 정의한 테스트]를 통해 정보 시스템의 인시던트 응답 계획을 테스트하여 인시던트 응답 효율성을 파악하고 결과를 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 기능 테스트로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-3-2"></a>NIST 800-53 제어 IR-3(2)

#### <a name="incident-response-testing--coordination-with-related-plans"></a>인시던트 응답 테스트 | 관련 계획에 맞게 조정

**IR-3(2)** 조직은 관련 계획을 담당하는 조직 요소를 사용하여 인시던트 응답 테스트를 조정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인스턴스 응답 기능 테스트는 관련 계획의 테스트에 맞게 조정될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-4a"></a>NIST 800-53 제어 IR-4.a

#### <a name="incident-handling"></a>인시던트 처리

**IR-4.a** 조직은 준비, 검색, 분석, 방지, 삭제 및 복구를 포함하는 보안 인시던트에 대한 인시던트 처리 기능을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 처리 기능으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-4b"></a>NIST 800-53 제어 IR-4.b

#### <a name="incident-handling"></a>인시던트 처리

**IR-4.b** 조직은 인시던트 처리 작업을 대체 계획 작업에 맞게 조정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 처리 기능으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-4c"></a>NIST 800-53 제어 IR-4.c

#### <a name="incident-handling"></a>인시던트 처리

**IR-4.c** 조직은 계속되는 인시던트 처리 작업에서 얻은 교훈을 인시던트 응답 절차, 교육 및 테스트에 통합하고 그에 맞게 결과 변경을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 처리 기능으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-4-1"></a>NIST 800-53 제어 IR-4 (1)

#### <a name="incident-handling--automated-incident-handling-processes"></a>인시던트 처리 | 자동화된 인시던트 처리 프로세스

**IR-4(1)** 조직은 인시던트 처리 프로세슬르 지원하는 자동화된 메커니즘을 사용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 처리 기능은 자동화를 통해 인시던트 처리 프로세스를 지원할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-4-2"></a>NIST 800-53 제어 IR-4(2)

#### <a name="incident-handling--dynamic-reconfiguration"></a>인시던트 처리 | 동적 재구성

**IR-4 (2)** 조직은 인시던트 응답 기능의 일부로 [할당: 조직이 정의한 정보 시스템 구성 요소]의 동적 구성을 포함합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 모든 고객 배포 리소스의 동적 재구성을 인시던트 응답 기능(예: 방화벽 및 게이트웨이에 대한 필터링 규칙, 액세스 제어 목록)에 포함할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-4-3"></a>NIST 800-53 제어 IR-4(3)

#### <a name="incident-handling--continuity-of-operations"></a>인시던트 처리 | 작업 연속성

**IR-4(3)** 조직은 [할당: 조직이 정의한 인시던트 클래스] 및 [할당: 인시던트 클래스에 대한 응답으로 수행할 조직이 정의한 작업]을 식별하여 조직의 임무 및 비즈니스 기능이 지속되도록 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 처리 기능은 인시던트 동안 조직의 임무 및 비즈니스 기능이 지속되도록 할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-4-4"></a>NIST 800-53 제어 IR-4(4)

#### <a name="incident-handling--information-correlation"></a>인시던트 처리 | 정보 상관 관계

**IR-4(4)** 조직은 인시던트 정보 및 개별 인시던트 응답 간의 상관 관계를 분석하여 인시던트 인식 및 응답을 조직 차원에서 파악할 수 있습니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 처리 기능에는 인시던트 정보 상관 관계가 포함될 수 있습니다.

 | | **공급자 (Microsoft Azure)** | 해당 사항 없음 |


 ### <a name="nist-800-53-control-ir-4-6"></a>NIST 800-53 제어 IR-4(6)

#### <a name="incident-handling--insider-threats---specific-capabilities"></a>인시던트 처리 | 내부자 위협 - 특정 기능

**IR-4(6)** 조직은 내부자 위협에 대한 인시던트 처리 기능을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 처리 기능으로 내부자 위협을 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-4-8"></a>NIST 800-53 제어 IR-4(8)

#### <a name="incident-handling--correlation-with-external-organizations"></a>인시던트 처리 | 외부 조직과의 상관 관계

**IR-4(8)** 조직은 [할당: 조직이 정의한 외부 조직]과 조율하면서 [할당: 조직이 정의한 인시던트 정보]의 상관 관계를 파악하고 공유함으로써 조직 차원에서 인시던트 인식 및 보다 효과적인 인시던트 응답을 구현할 수 있습니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 처리 기능에는 외부 조직과의 상관 관계를 파악할 수 있는 기능이 포함될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-5"></a>NIST 800-53 제어 IR-5

#### <a name="incident-monitoring"></a>인시던트 모니터링

**IR-5** 조직은 정보 시스템 보안 인시던트를 추적하고 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 모니터링 기능으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-5-1"></a>NIST 800-53 제어 IR-5(1)

#### <a name="incident-monitoring--automated-tracking--data-collection--analysis"></a>인시던트 모니터링 | 자동 추적 / 데이터 수집 / 분석

**IR-5(1)** 조직은 자동화된 메커니즘을 사용하여 보안 인시던트의 추적과 인시던트 정보의 수집 및 분석에 도움을 줍니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 모니터링 기능은 자동 추적, 수집 및 분석 메커니즘을 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-6a"></a>NIST 800-53 제어 IR-6.a

#### <a name="incident-reporting"></a>인시던트 보고

**IR-6.a** 조직은 직원이 [할당: 조직이 정의한 기간] 내에 조직 인시던트 응답 기능으로 의심스러운 보안 인시던트를 보고하도록 요구합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 절차는 직원의 보고 요구를 처리할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-6b"></a>NIST 800-53 제어 IR-6.b

#### <a name="incident-reporting"></a>인시던트 보고

**IR-6.b** 조직은 보안 인시던트 정보를 [할당: 조직이 정의한 기관]에 보고합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 절차는 직원의 보고 요구를 처리할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-6-1"></a>NIST 800-53 제어 IR-6(1)

#### <a name="incident-reporting--automated-reporting"></a>인시던트 보고 | 자동 보고

**IR-6(1)** 조직은 보안 자동화 메커니즘을 사용하여 보안 인시던트 보고에 도움을 줍니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 보고 기능에 자동화 보고 메커니즘이 포함될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-7"></a>NIST 800-53 제어 IR-7

#### <a name="incident-response-assistance"></a>인시던트 응답 지원

**IR-7** 조직은 조직의 인시던트 응답 기능에 통합된 인시던트 응답 지원 리소스를 제공하여 정보 시스템 사용자가 보안 인시던트를 처리 및 보고하기 위한 권고 및 지원을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 기능에는 인시던트 응답 지원 리소스가 포함될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-7-1"></a>NIST 800-53 제어 IR-7(1)

#### <a name="incident-response-assistance--automation-support-for-availability-of-information--support"></a>인시던트 응답 지원 | 정보의 가용성에 대한 자동 지원 / 지원

**IR-7(1)** 조직은 자동 메커니즘을 사용하여 인시던트 응답 관련 정보 및 지원의 가용성을 높입니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 기능은 자동 메커니즘을 사용하여 사용 가능한 인시던트 응답 지원 및 리소스를 만들 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-7-2a"></a>NIST 800-53 제어 IR-7(2).a

#### <a name="incident-response-assistance--coordination-with-external-providers"></a>인시던트 응답 지원 | 외부 공급자와 조율

**IR-7(2).a** 조직은 인시던트 응답 기능과 정보 시스템 보호 기능의 외부 공급자 간에 직접적인 협력 관계를 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 기능은 외부 공급자와의 조율되도록 할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-7-2b"></a>NIST 800-53 제어 IR-7(2).b

#### <a name="incident-response-assistance--coordination-with-external-providers"></a>인시던트 응답 지원 | 외부 공급자와 조율

**IR-7(2).b** 조직은 외부 공급자에게 조직의 인시던트 응답 팀 구성원을 식별합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 기능은 외부 공급자와의 조율되도록 할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-8a"></a>NIST 800-53 제어 IR-8.a

#### <a name="incident-response-plan"></a>인시던트 응답 계획

**IR-8.a** 조직은 인시던트 응답 기능을 구현하기 위한 로드맵을 조직에 제공하고, 인시던트 응답 기능의 구조 및 조직을 설명하고, 인시던트 응답 기능이 전체 조직에 어떻게 맞는지에 대한 개괄적인 접근 방식을 제공하고, 임무, 규모, 구조 및 기능과 관련된 조직의 고유한 요구 사항을 충족하고, 보고 가능한 인시던트를 정의하고, 조직 내의 인시던트 응답 기능을 측정하기 위한 메트릭을 제공하고, 인시던트 응답 기능을 효과적으로 유지하고 향상시키는 데 필요한 리소스 및 관리 지원을 정의하고, [할당: 조직이 정의한 직원 또는 역할]이 검토 및 승인하는 인시던트 응답 계획을 개발합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 계획으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-8b"></a>NIST 800-53 제어 IR-8.b

#### <a name="incident-response-plan"></a>인시던트 응답 계획

**IR-8.b** 조직은 인시던트 응답 계획 사본을 [할당: 조직이 정의한 인시던트 응답 담당자(이름 및/또는 역할로 식별) 및 조직 요소]에게 전달합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-8c"></a>NIST 800-53 제어 IR-8.c

#### <a name="incident-response-plan"></a>인시던트 응답 계획

**IR-8.c** 조직은 [할당: 조직이 정의한 빈도]마다 인시던트 응답 계획을 검토합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-8d"></a>NIST 800-53 제어 IR-8.d

#### <a name="incident-response-plan"></a>인시던트 응답 계획

**IR-8.d** 조직은 시스템/조직 변경 또는 계획 구현, 실행 또는 테스트 중에 발생한 문제를 해결하기 위해 인시던트 응답 계획을 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-8e"></a>NIST 800-53 제어 IR-8.e

#### <a name="incident-response-plan"></a>인시던트 응답 계획

**IR-8.e** 조직은 인시던트 응답 계획 변경 사항을 [할당: 조직이 정의한 인시던트 응답 담당자(이름 및/또는 역할로 식별) 및 조직 요소]에게 전달합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-8f"></a>NIST 800-53 제어 IR-8.f

#### <a name="incident-response-plan"></a>인시던트 응답 계획

**IR-8.f** 조직은 무단 공개 및 수정으로부터 인시던트 응답 계획을 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-9a"></a>NIST 800-53 제어 IR-9.a

#### <a name="information-spillage-response"></a>정보 유출 대응

**IR-9.a** 조직은 정보 시스템 오염에 해당되는 구체적 정보를 식별하여 정보 유출에 대응합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 정보 유출 대응 기능으로 이 제어를 충분히 해결할 수 있습니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-9b"></a>NIST 800-53 제어 IR-9.b

#### <a name="information-spillage-response"></a>정보 유출 대응

**IR-9.b** 조직은 [할당: 조직이 정의한 담당자 또는 역할]에게 유출과 관련되지 않은 통신 방법을 사용하여 정보 유출을 경고함으로써 정보 유출에 대응합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 정보 유출 대응 기능으로 이 제어를 충분히 해결할 수 있습니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-9c"></a>NIST 800-53 제어 IR-9.c

#### <a name="information-spillage-response"></a>정보 유출 대응

**IR-9.c** 조직은 오염된 정보 시스템이나 시스템 구성 요소를 분리하여 정보 유출에 대응합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 정보 유출 대응 기능으로 이 제어를 충분히 해결할 수 있습니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-9d"></a>NIST 800-53 제어 IR-9.d

#### <a name="information-spillage-response"></a>정보 유출 대응

**IR-9.d** 조직은 오염된 정보 시스템이나 시스템 구성 요소에서 정보를 제거하여 정보 유출에 대응합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 정보 유출 대응 기능으로 이 제어를 충분히 해결할 수 있습니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-9e"></a>NIST 800-53 제어 IR-9.e

#### <a name="information-spillage-response"></a>정보 유출 대응

**IR-9.e** 조직은 이후에 오염될 수 있는 다른 정보 시스템 또는 시스템 구성 요소를 식별하여 정보 유출에 대응합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 정보 유출 대응 기능으로 이 제어를 충분히 해결할 수 있습니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ir-9f"></a>NIST 800-53 제어 IR-9.f

#### <a name="information-spillage-response"></a>정보 유출 대응

**IR-9.f** 조직은 다른 [할당: 조직이 정의한 작업]을 수행하여 정보 유출에 대응합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 정보 유출 대응 기능으로 이 제어를 충분히 해결할 수 있습니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-9-1"></a>NIST 800-53 제어 IR-9(1)

#### <a name="information-spillage-response--responsible-personnel"></a>정보 유출 대응 | 책임 담당자

**IR-9(1)** 조직은 [할당: 조직이 정의한 담당자 또는 역할]에게 정보 유출에 대한 대응 책임 할당합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 절차는 정보 유출 대응 책임이 있는 담당자 또는 역할을 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-9-2"></a>NIST 800-53 제어 IR-9(2)

#### <a name="information-spillage-response--training"></a>정보 유출 대응 | 교육

**IR-9(2)** 조직은 [할당: 조직이 정의한 빈도]마다 정보 유출 대응 교육을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 인시던트 응답 교육에는 정보 유출 대응이 포함될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-9-3"></a>NIST 800-53 제어 IR-9(3)

#### <a name="information-spillage-response--post-spill-operations"></a>정보 유출 대응 | 유출 후 작업

**IR-9(3)** 조직은 [할당: 조직이 정의한 절차]를 구현하여 정보 유출의 영향을 받은 조직 직원이 오염된 시스템이 수정되는 동안 할당된 태스크를 수행할 수 있도록 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 정보 유출에 대한 유출 후 대응 기능으로 이 제어를 충분히 해결할 수 있습니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ir-9-4"></a>NIST 800-53 제어 IR-9(4)

#### <a name="information-spillage-response--exposure-to-unauthorized-personnel"></a>정보 유출 대응 | 권한 없는 사람에게 노출

**IR-9(4)** 조직은 할당된 액세스 권한을 벗어나서 정보에 노출된 직원에 대해 [할당: 조직이 정의한 보안 안전 조치]를 사용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 사고 대응 프로시저는 할당된 액세스 권한을 벗어나 정보에 실수로 노출된 직원에 대해 안전 조치를 실행할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |
