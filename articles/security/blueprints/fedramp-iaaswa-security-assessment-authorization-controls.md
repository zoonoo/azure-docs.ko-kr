---
title: Azure Security and Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화 - 보안 평가 및 권한 부여
description: FedRAMP 웹 응용 프로그램 자동화 - 보안 평가 및 권한 부여
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 47c5914d-0d76-498a-9298-b3d9040791f8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: d1c9846589adaef0baeeaf16077c8e74bef1cd9f
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206523"
---
# <a name="security-assessment-and-authorization-ca"></a>보안 평가 및 권한 부여(CA)

> [!NOTE]
> 이러한 제어는 NIST와 미국 상무부에 의해 NIST 특별 출판물 800-53 개정 4의 일부로 정의됩니다. 각 제어에 대한 테스트 절차 및 지침에 대한 정보는 NIST 800-53 개정 4를 참조하세요.

## <a name="nist-800-53-control-ca-1"></a>NIST 800-53 Control CA-1

#### <a name="security-assessment-and-authorization-policy-and-procedures"></a>보안 평가 및 권한 부여 정책과 절차

**CA-1** 조직에서는 목적, 범위, 역할, 책임, 관리 약속, 조직 엔터티 간 조정 및 준수를 다루는 보안 평가 및 권한 부여 정책과 보안 평가 및 권한 부여 정책과 관련된 보안 평가 및 권한 부여 제어의 구현을 용이하게 하는 절차를 개발, 문서화하여 [할당: 조직에서 정의한 담당자 또는 역할]에게 배포하고, [할당: 조직에서 정의한 빈도]마다 현재 보안 평가 및 권한 부여 정책을 검토하고 업데이트하며 [할당: 조직에서 정의한 빈도]마다 보안 평가 및 권한 부여 절차를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 보안 평가 및 권한 부여 정책과 절차로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-2a"></a>NIST 800-53 Control CA-2.a

#### <a name="security-assessments"></a>보안 평가

**CA-2.a** 조직에서는 평가할 보안 제어 및 향상된 제어 기능을 포함하여 평가 범위를 설명하는 보안 평가 계획, 보안 제어 효율성을 확인하는 데 사용할 평가 절차, 평가 환경, 평가 팀 그리고 평가 역할 및 책임에 대해 설명하는 보안 평가 계획을 개발해야 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 대한 보안 평가 계획을 개발할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-2b"></a>NIST 800-53 Control CA-2.b

#### <a name="security-assessments"></a>보안 평가

**CA-2.b** 조직에서는 [할당: 조직에서 정의한 빈도]마다 정보 시스템 및 그 운영 환경의 보안 제어를 평가하여 컨트롤이 올바르게 구현되고, 의도대로 작동하고, 설정된 보안 요구 사항과 관련하여 원하는 결과를 도출하는 범위를 확인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 대한 CA-02.a에 정의된 보안 제어를 평가할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-2c"></a>NIST 800-53 Control CA-2.c

#### <a name="security-assessments"></a>보안 평가

**CA-2.c** 조직에서는 평가 결과를 설명하는 보안 평가 보고서를 작성합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 보안 평가 보고서를 작성할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-2d"></a>NIST 800-53 Control CA-2.d

#### <a name="security-assessments"></a>보안 평가

**CA-2.d** 조직에서는 [할당: 조직에서 정의한 개인 또는 역할]에 보안 제어 평가 결과를 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 필요한 개인/역할에게 보안 평가 결과를 제공할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ca-2-1"></a>NIST 800-53 Control CA-2 (1)

#### <a name="security-assessments--independent-assessors"></a>보안 평가 | 독립 평가자

**CA-2 (1)** 조직에서는 [할당: 조직에서 정의한 독립성 수준]을 충족하는 평가자 또는 평가 팀을 사용하여 보안 제어 평가를 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 독립 평가자 또는 평가 팀을 사용하여 보안 제어 평가를 수행할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ca-2-2"></a>NIST 800-53 Control CA-2 (2)

#### <a name="security-assessments--specialized-assessments"></a>보안 평가 | 특별 평가

**CA-2 (2)** 조직에서는 보안 제어 평가의 일부로 [할당: 조직에서 정의한 빈도]마다 [선택: 발표된; 발표되지 않은] [선택(하나 이상): 세부 모니터링; 취약성 스캔; 악의적인 사용자 테스트; 내부자 위협 평가; 성능/부하 테스트; [할당: 조직에서 정의한 기타 양식의 보안 평가]]를 포함합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 보안 제어 평가의 일부로 추가 테스트 선택을 포함할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ca-2-3"></a>NIST 800-53 Control CA-2 (3)

#### <a name="security-assessments--external-organizations"></a>보안 평가 | 외부 조직

**CA-2 (3)** 조직에서는 평가가 [할당: 조직에서 정의한 요구 사항]을 만족하는 경우 [할당: 조직에서 정의한 외부 조직]이 수행한 [할당: 조직에서 정의한 정보 시스템] 평과 결과를 수락합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 보안 평가 및 권한 부여 절차에서 FedRAMP 산하의 외부 조직(즉, 3PAO(타사 평가 조직) 또는 다른 기관)이 수행한 클라우드 서비스 제품(즉, Azure) 평가의 결과 수락에 대해 다룰 수 있습니다. Azure는 FedRAMP 보안 제어 및 기타 요구 사항을 준수하는지 확인하기 위해 FedRAMP에서 승인한 3PAO(타사 평가 조직)에 의해 평가됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-3a"></a>NIST 800-53 Control CA-3.a

#### <a name="system-interconnections"></a>시스템 상호 연결

**CA-3.a** 조직에서는 상호 연결 보안 계약을 사용하여 정보 시스템에서 다른 정보 시스템으로 연결할 권한을 부여 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 보안 평가 및 권한 부여 절차에서 시스템 상호 연결에 대한 권한 부여를 다룰 수 있습니다. 참고: FedRAMP는 CSP와 연방 기관 사이에 ISA가 필요하지 않습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-3b"></a>NIST 800-53 Control CA-3.b

#### <a name="system-interconnections"></a>시스템 상호 연결

**CA-3.b** 조직에서는 각 상호 연결에 대한 인터페이스 특징, 보안 요구 사항 및 전달되는 정보의 성질을 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 보안 평가 및 권한 부여 절차에서 시스템 상호 연결을 설정하기 위한 요구 사항을 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-3c"></a>NIST 800-53 Control CA-3.c

#### <a name="system-interconnections"></a>시스템 상호 연결

**CA-3.c** 조직은 [할당: 조직에서 정의한 빈도]마다 상호 연결 보안 계약을 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 보안 평가 및 권한 부여 절차에서 ISA 검토 및 업데이트 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ca-3-3"></a>NIST 800-53 Control CA-3 (3)

#### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>시스템 상호 연결 | 국가 소유가 아닌 미분류 보안 시스템 연결

**CA-3 (3)** 조직에서는 [Assignment; 조직에서 정의한 경계 보호 장치]를 사용하지 않고 [할당: 조직에서 정의한 국가 소유가 아닌 미분류 보안 시스템]을 외부 네트워크에 바로 연결하는 것을 금지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 보안 평가 및 권한 부여 절차에서 시스템 상호 연결에 대한 경계 보호 요구 사항을 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ca-3-5"></a>NIST 800-53 Control CA-3 (5)

#### <a name="system-interconnections--restrictions-on-external-system-connections"></a>시스템 상호 연결 | 외부 시스템 연결 제한

**CA-3 (5)** 조직에서는 [선택: allow-all, deny-by-exception; deny-all, permit-by-exception] 정책을 사용하여 [할당: 조직에서 정의한 정보 시스템]이 외부 정보 시스템에 연결하는 것을 허용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Application Gateway 및 네트워크 보안 그룹을 배포하여 외부 연결을 이 Blueprint에서 배포한 리소스로 제한합니다. 네트워크 보안 그룹에 적용되는 규칙 집합은 deny-by-default 체계를 사용하여 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-5a"></a>NIST 800-53 Control CA-5.a

#### <a name="plan-of-action-and-milestones"></a>작업 계획 및 마일스톤

**CA-5.a** 조직에서는 정보 시스템에 대한 작업 계획 및 마일스톤을 개발하여 보안 제어 평가에서 발견된 약점 또는 결함을 수정하고 시스템의 알려진 취약성을 줄이거나 제거하기 위해 계획한 수정 작업을 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함)에 대한 작업 계획 및 마일스톤을 개발할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-5b"></a>NIST 800-53 Control CA-5.b

#### <a name="plan-of-action-and-milestones"></a>작업 계획 및 마일스톤

**CA-5.b** 조직에서는 [할당: 조직에서 정의한 빈도]마다 보안 제어 평가, 보안 영향 분석 및 연속 모니터링 활동에서 얻은 정보를 바탕으로 기존 작업 계획 및 마일스톤을 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 CA-05.a에 정의된 POA&M 항목을 업데이트할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-6a"></a>NIST 800-53 Control CA-6.a

#### <a name="security-authorization"></a>보안 권한 부여

**CA-6.a** 조직에서는 정보 시스템에 대한 권한 있는 책임자로 임원급 간부 또는 관리자를 할당합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 권한 있는 책임자가 할당되는 엔터프라이즈 수준 프로세스를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-6b"></a>NIST 800-53 Control CA-6.b

#### <a name="security-authorization"></a>보안 권한 부여

**CA-6.b** 조직에서는 작업을 시작하기 전에 권한 있는 책임자가 정보 시스템에 처리 권한을 부여합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 엔터프라이즈 수준 시스템 권한 부여 프로세스를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-6c"></a>NIST 800-53 Control CA-6.c

#### <a name="security-authorization"></a>보안 권한 부여

**CA-6.c** 조직에서는 [할당: 조직에서 정의한 빈도]마다 보안 권한 부여를 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 엔터프라이즈 수준 시스템 권한 부여 프로세스를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-7a"></a>NIST 800-53 Control CA-7.a

#### <a name="continuous-monitoring"></a>연속 모니터링

**CA-7.a** 조직에서는 모니터링할 [할당: 조직에서 정의한 메트릭] 설정이 포함된 연속 모니터링 전략을 개발하고 연속 모니터링 프로그램을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 연속 모니터링 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-7b"></a>NIST 800-53 Control CA-7.b

#### <a name="continuous-monitoring"></a>연속 모니터링

**CA-7.b** 조직에서는 [할당: 조직에서 정의한 빈도] 모니터링 및 이러한 모니터링을 지원하는 [할당: 조직에서 정의한 빈도] 평가 설정이 포함된 연속 모니터링 전략을 개발하고 연속 모니터링 프로그램을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 연속 모니터링 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-7c"></a>NIST 800-53 Control CA-7.c

#### <a name="continuous-monitoring"></a>연속 모니터링

**CA-7.c** 조직에서는 조직의 연속 모니터링 전략에 따라 지속적으로 보안 제어를 평가하는 연속 모니터링 전략을 개발하고 연속 모니터링 프로그램을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 연속 모니터링 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-7d"></a>NIST 800-53 Control CA-7.d

#### <a name="continuous-monitoring"></a>연속 모니터링

**CA-7.d** 조직에서는 조직의 연속 모니터링 전략에 따라 조직에서 정의한 메트릭의 보안 상태를 지속적으로 모니터링하는 연속 모니터링 전략을 개발하고 연속 모니터링 프로그램을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 연속 모니터링 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-7e"></a>NIST 800-53 Control CA-7.e

#### <a name="continuous-monitoring"></a>연속 모니터링

**CA-7.e** 조직에서는 평가 및 모니터링을 통해 생성된 보안 관련 정보의 상관 관계를 지정하고 분석하는 연속 모니터링 전략을 개발하고 연속 모니터링 프로그램을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 연속 모니터링 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-7f"></a>NIST 800-53 Control CA-7.f

#### <a name="continuous-monitoring"></a>연속 모니터링

**CA-7.f** 조직에서는 보안 관련 정보의 분석 결과에 대한 대응 조치가 포함된 연속 모니터링 전략을 개발하고 연속 모니터링 프로그램을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 연속 모니터링 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-7g"></a>NIST 800-53 Control CA-7.g

#### <a name="continuous-monitoring"></a>연속 모니터링

**CA-7.g** 조직에서는 [할당: 조직에서 정의한 빈도]마다 [할당: 조직에서 정의한 담당자 또는 역할]에 조직과 정보 시스템의 보안 상태를 보고하는 연속 모니터링 전략을 개발하고 연속 모니터링 프로그램을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 연속 모니터링 프로그램으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ca-7-1"></a>NIST 800-53 Control CA-7 (1)

#### <a name="continuous-monitoring--independent-assessment"></a>연속 모니터링 | 독립 평가

**CA-7 (1)** 조직에서는 [할당: 조직에서 정의한 독립성 수준]을 충족하는 평가자 또는 평가 팀을 사용하여 정보 시스템의 보안 제어를 지속적으로 모니터링합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 연속 모니터링 프로그램에서 보안 제어를 독립적으로 평가하기 위한 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ca-7-3"></a>NIST 800-53 Control CA-7 (3)

#### <a name="continuous-monitoring--trend-analyses"></a>연속 모니터링 | 추세 분석

**CA-7 (3)** 조직에서는 추세 분석을 사용하여 경험적 데이터를 기반으로 보안 제어 구현, 연속 모니터링 작업의 빈도 및/또는 연속 모니터링 프로세스에 사용되는 작업 형식을 수정해야 하는지 확인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 연속 모니터링 프로그램에서 추세 분석을 위한 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-8"></a>NIST 800-53 Control CA-8

#### <a name="penetration-testing"></a>침투 테스트

**CA-8** 조직에서는 [할당: 조직에서 정의한 빈도]마다 [할당: 조직에서 정의한 정보 시스템 또는 시스템 구성 요소]에서 침투 테스트를 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 엔터프라이즈 수준의 침투 테스트 평가를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ca-8-1"></a>NIST 800-53 Control CA-8 (1)

#### <a name="penetration-testing--independent-penetration-agent-or-team"></a>침투 테스트 | 독립 침투 에이전트 또는 팀

**CA-8 (1)** 조직에서는 독립 침투 에이전트 또는 침투 팀을 사용하여 정보 시스템 또는 시스템 구성 요소에서 침투 테스트를 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 독립 에이전트 또는 팀에서 수행하는 엔터프라이즈 수준의 침투 테스트 평가를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-9a"></a>NIST 800-53 Control CA-9.a

#### <a name="internal-system-connections"></a>내부 시스템 연결

**CA-9.a** 조직에서는 [할당: 조직에서 정의한 정보 시스템 구성 요소 또는 구성 요소 클래스]를 정보 시스템에 내부적으로 연결하는 권한을 부여합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에서 내부 연결(즉, VM에 시스템 연결)에 대한 권한을 부여할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ca-9b"></a>NIST 800-53 Control CA-9.b

#### <a name="internal-system-connections"></a>내부 시스템 연결

**CA-9.b** 조직에서는 각 내부 연결에 대한 인터페이스 특징, 보안 요구 사항 및 전달되는 정보의 성질을 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 CA-09.a에 정의된 클래스/리소스 간의 각 내부 연결에 대한 세부 정보를 문서화할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |
