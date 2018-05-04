---
title: "Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화 - 계획"
description: "FedRAMP 웹 응용 프로그램 자동화 - 계획"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 55032e87-763a-452d-bb22-9c28936d5bb4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: d5e5666db71ac9f5a136c9acb448fb13bf39a853
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2018
---
# <a name="planning-pl"></a>계획(PL)

> [!NOTE]
> 이러한 제어는 NIST와 미국 상무부에 의해 NIST 특별 출판물 800-53 개정 4의 일부로 정의됩니다. 각 제어에 대한 테스트 절차 및 지침에 대한 정보는 NIST 800-53 개정 4를 참조하세요.

## <a name="nist-800-53-control-pl-1"></a>NIST 800-53 제어 PL-1

#### <a name="security-planning-policy-and-procedures"></a>보안 계획 정책 및 절차

**PL-1** 조직은 목적, 범위, 역할, 책임, 관리 약속, 조직 엔터티 간 조정 및 준수를 다루는 보안 계획 정책 및 보안 계획 정책과 관련 보안 계획 제어의 구현을 용이하게 하는 절차를 개발, 문서화하여 [할당: 조직이 정의한 담당자 또는 역할]에게 배포하고, [할당: 조직이 정의한 빈도]마다 현재 보안 계획 정책을 검토하고 업데이트하며 [할당: 조직이 정의한 빈도]마다 보안 계획 절차를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 보안 계획 정책 및 절차에서 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-2a"></a>NIST 800-53 제어 PL-2.a

#### <a name="system-security-plan"></a>시스템 보안 계획

**PL 2.a** 조직은 조직의 엔터프라이즈 아키텍처와 일치하는 정보 시스템에 대한 보안 계획을 개발하고, 시스템의 인증 경계를 명시적으로 정의하고, 임무 및 비즈니스 프로세스 측면에서 정보 시스템의 운영 컨텍스트를 설명하고, 지원 사유를 비롯한 정보 시스템의 보안 분류를 제공하고, 정보 시스템에 대한 운영 환경 및 다른 정보 시스템과의 관계 또는 연관을 설명하고, 시스템의 보안 요구 사항 개요를 제공하고, 관련 오버레이를 식별하고(해당되는 경우), 조정된 결정 사유를 비롯하여 해당 요구 사항을 총족하기 위한 현재 또는 계획된 보안 제어를 설명하고, 계획 구현 전에 허가 공무원 또는 지정된 담당자가 검토하고 승인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 대상 승인 기관(예: FedRAMP)에서 정의한 조건을 만족하는 SSP(시스템 보안 계획)를 개발할 책임이 있습니다. 고객은 NIST 특별 출판물 800-18 R1, Federal Information Systems의 보안 계획 개발 가이드를 참조할 수 있습니다. 고객 SSP는 Microsoft Azure에서 상속된 제어를 처리하고 Microsoft Azure SSP에서 구현 세부 사항을 참조하는 것이 좋습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-2b"></a>NIST 800-53 제어 PL-2.b

#### <a name="system-security-plan"></a>시스템 보안 계획

**PL-2.b** 조직은 보안 계획 사본을 배포하고 [할당: 조직이 정의한 직원 또는 역할]에게 후속 계획 변경 내용을 전달합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 시스템 보안 계획을 배포할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-2c"></a>NIST 800-53 제어 PL-2.c

#### <a name="system-security-plan"></a>시스템 보안 계획

**PL-2.c** 조직은 [할당: 조직이 정의한 빈도]마다 정보 시스템의 보안 계획을 검토합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 시스템 보안 계획을 검토할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-2d"></a>NIST 800-53 제어 PL-2.d

#### <a name="system-security-plan"></a>시스템 보안 계획

**PL-2.d** 조직은 정보 시스템/작업 환경의 변경 또는 계획 구현 또는 보안 제어 평가 동안 식별된 문제를 해결하기 위해 계획을 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 시스템 보안 계획을 업데이트할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-2e"></a>NIST 800-53 제어 PL-2.e

#### <a name="system-security-plan"></a>시스템 보안 계획

**PL-2.e** 조직은 보안 계획을 무단 공개 및 수정으로부터 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 시스템 보안 계획을 보호할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-pl-2-3"></a>NIST 800-53 제어 PL-2(3)

#### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>시스템 보안 계획 | 계획/다른 조직 주체와 함께 조정

**PL-2(3)** 조직은 다른 조직 주체에 미치는 영향을 줄이기 위해 이러한 활동을 이행하기 전에 [할당: 조직이 정의한 개인 또는 그룹]과 함께 정보 시스템에 영향을 미치는 보안 관련 활동을 계획하고 조정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 보안 관련 활동을 계획하고 조정하여 다른 조직 주체에 미치는 영향을 줄일 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-4a"></a>NIST 800-53 제어 PL-4.a

#### <a name="rules-of-behavior"></a>행동 규칙

**PL-4.a** 조직은 정보 및 정보 시스템 사용과 관련된 해당 의무 및 예상 행동을 설명하는 규칙을 설정하고 정보 시스템에 액세스해야 하는 개인이 이러한 규칙을 쉽게 사용할 수 있도록 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 행동 규칙으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-4b"></a>NIST 800-53 제어 PL-4.b

#### <a name="rules-of-behavior"></a>행동 규칙

**PL-4.b** 조직은 이러한 개인의 서명된 승인을 받으며, 정보 및 정보 시스템에 대한 액세스를 허가하기 전에 행동 규칙을 읽고, 이해하고, 준수할 것에 동의함을 명시합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 행동 규칙으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-4c"></a>NIST 800-53 제어 PL-4.c

#### <a name="rules-of-behavior"></a>행동 규칙

**PL-4.c** 조직은 [할당: 조직이 정의한 빈도]마다 행동 규칙을 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 행동 규칙으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-4d"></a>NIST 800-53 제어 PL-4.d

#### <a name="rules-of-behavior"></a>행동 규칙

**PL-4.d** 조직은 행동 규칙이 수정/업데이트될 경우 이전 버전의 행동 규칙에 서명한 개인에게 해당 행동 규칙을 읽고 다시 서명할 것을 요청합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 행동 규칙으로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-pl-4-1"></a>NIST 800-53 제어 PL-4 (1)

#### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>행동 규칙 | 소셜 미디어 및 네트워킹 제한 사항

**PL-4(1)** 조직은 소셜 미디어/네트워킹 사이트의 사용 및 공용 웹 사이트에 조직 정보 게시에 대한 명시적 제한 사항을 행동 규칙에 포함합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 행동 규칙에는 소셜 미디어/네트워킹 사이트에 대한 제한 사항이 포함될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-8a"></a>NIST 800-53 제어 PL-8.a

#### <a name="information-security-architecture"></a>정보 보안 아키텍처

**PL-8.a** 조직은 조직 정보의 기밀성, 무결성 및 가용성 보호와 관련해서 수용해야 할 전반적인 원칙, 요구 사항 및 접근 방식을 설명하고, 정보 보안 아키텍처가 엔터프라이즈 아키텍처에 통합되고 이러한 아키텍처를 지원하는 방식을 설명하고, 외부 서비스에 대한 정보 보안 가정 및 종속성을 설명하는 정보 시스템에 대한 정보 보안 아키텍처를 개발합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대해 정보 보안 아키텍처를 개발할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-8b"></a>NIST 800-53 제어 PL-8.b

#### <a name="information-security-architecture"></a>정보 보안 아키텍처

**PL-8.b** 조직은 [할당: 조직이 정의한 빈도]마다 정보 보안 아키텍처를 검토 및 업데이트하여 엔터프라이즈 아키텍처에 업데이트를 반영합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 정보 보안 아키텍처를 검토 및 업데이트할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-pl-8c"></a>NIST 800-53 제어 PL-8.c

#### <a name="information-security-architecture"></a>정보 보안 아키텍처

**PL-8.c** 조직은 계획된 정보 보안 아키텍처 변경 내용이 보안 계획, 보안 운영 개념(CONOPS) 및 조직 조달/인수에 반영되도록 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 정보 보안 아키텍처에 대한 계획된 변경을 설명할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |
