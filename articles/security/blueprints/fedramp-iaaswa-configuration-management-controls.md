---
title: Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화 - 구성 관리
description: FedRAMP 웹 애플리케이션 자동화 - 구성 관리
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 356eccac6af2780c02d1cd935d41891b5f89f1a2
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="configuration-management-cm"></a>CM(구성 관리)

> [!NOTE]
> 이러한 제어는 NIST와 미국 상무부에 의해 NIST 특별 출판물 800-53 개정 4의 일부로 정의됩니다. 각 제어에 대한 테스트 절차 및 지침에 대한 정보는 NIST 800-53 개정 4를 참조하세요.

## <a name="nist-800-53-control-cm-1"></a>NIST 800-53 Control CM-1

#### <a name="configuration-management-policy-and-procedures"></a>구성 관리 정책 및 절차

**CM-1** 조직에서는 목적, 범위, 역할, 책임, 관리 약속, 조직 엔터티 간 조정 및 규정 준수를 다루는 구성 관리 정책 및 구성 관리 정책과 관련 구성 관리 제어의 구현을 용이하게 하는 절차를 개발, 문서화하여 [할당: 조직에서 정의한 담당자 또는 역할]에게 배포하고, [할당: 조직에서 정의한 빈도]마다 현재 구성 관리 정책을 검토하고 업데이트하며 [할당: 조직에서 정의한 빈도]마다 구성 관리 절차를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 구성 관리 정책 및 절차에서 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-2"></a>NIST 800-53 Control CM-2

#### <a name="baseline-configuration"></a>기준 구성

**CM-2** 조직에서는 구성 제어 하에서 정보 시스템의 현재 기준 구성을 개발, 문서화 및 유지 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Resource Manager 템플릿과 이 청사진을 구성하는 함께 제공되는 리소스는 배포된 아키텍처의 "코드로 구성" 기준을 나타냅니다. 솔루션은 GitHub를 통해 제공되며, 구성 제어에 사용할 수 있습니다. 솔루션에는 배포된 각 가상 머신에 대한 DSC(필요한 상태 구성) 기준이 포함되어 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-2-1a"></a>NIST 800-53 Control CM-2 (1).a

#### <a name="baseline-configuration--reviews-and-updates"></a>기준 구성 | 검토 및 업데이트

**CM-2 (1).a** 조직에서는 [할당: 조직에서 정의한 빈도]마다 정보 시스템의 기준 구성을 검토 및 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함)의 기준 구성을 검토 및 업데이트할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-2-1b"></a>NIST 800-53 Control CM-2 (1).b

#### <a name="baseline-configuration--reviews-and-updates"></a>기준 구성 | 검토 및 업데이트

**CM-2 (1).b** 조직에서는 [할당: 조직에서 정의한 환경] 때문에 필요한 경우 정보 시스템의 기준 구성을 검토 및 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 필요한 경우 고객 배포 리소스의 기준 구성을 검토 및 업데이트할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-2-1c"></a>NIST 800-53 Control CM-2 (1).c

#### <a name="baseline-configuration--reviews-and-updates"></a>기준 구성 | 검토 및 업데이트

**CM-2 (1).c** 조직에서는 정보 시스템 구성 요소 설치 및 업그레이드의 필수적인 부분으로써 정보 시스템의 기준 구성을 검토 및 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 필요한 경우 고객 배포 리소스의 기준 구성을 검토 및 업데이트할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800-53 Control CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>기준 구성 | 정확도를 위한 자동화 지원 / 통화

**CM-2 (2)** 조직에서는 자동화된 메커니즘을 사용하여 완전하고 정확하고 즉시 사용할 수 있는 최신 정보 시스템 기준 구성을 유지 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Resource Manager 템플릿과 이 청사진을 구성하는 함께 제공되는 리소스는 배포된 아키텍처의 "코드로 구성" 기준을 나타냅니다. 솔루션은 GitHub를 통해 제공되며, 구성 제어에 사용할 수 있습니다. Azure Portal에서 자동화 스크립트는 배포된 모든 리소스서 사용할 수 있으며 언제나 이러한 리소스의 최신 표현을 제공합니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800-53 Control CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>기준 구성 | 이전 구성 보존

**CM-2 (3)** 조직에서는 롤백을 지원할 수 있도록 [할당: 조직에서 정의한 정보 시스템 기준 구성의 이전 버전]을 보존합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 대한 이전 버전의 기준 구성을 유지할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-2-7a"></a>NIST 800-53 Control CM-2 (7).a

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>기준 구성 | 고위험 영역의 시스템, 구성 요소 또는 장치 구성

**CM-2 (7).a** 조직에서는 매우 위험한 것으로 간주되는 지역으로 출장을 가는 개인에게 [할당: 조직에서 정의한 구성]이 적용된 [할당: 조직에서 정의한 정보 시스템, 시스템 구성 요소 또는 장치]를 지급합니다.

**책임:** `Not Applicable`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에는 고객이 제어하는 물리적 장치가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure 고객 콘텐츠는 절대로 물리적으로 미국 대륙에 위치한 Microsoft Azure 외부에 저장되지 않습니다. Microsoft Azure 담당자는 Microsoft Azure 인벤토리 내에 포함된 장치를 휴대한 채로 출장을 다니지 않습니다. 따라서 이 제어는 Microsoft Azure에 적용되지 않습니다. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>NIST 800-53 Control CM-2 (7).b

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>기준 구성 | 고위험 영역의 시스템, 구성 요소 또는 장치 구성

**CM-2 (7).b** 조직에서는 개인이 장치를 반환할 때 [할당: 조직에서 정의한 보안 조치]를 적용합니다.

**책임:** `Not Applicable`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에는 고객이 제어하는 물리적 장치가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure 고객 콘텐츠는 절대로 Microsoft Azure 외부에 저장되지 않으며 Microsoft Azure 담당자는 Microsoft Azure 인벤토리 내에 포함된 장치를 휴대한 채로 출장을 다니지 않습니다. 따라서 이 제어가 적용되지 않습니다. |


 ## <a name="nist-800-53-control-cm-3a"></a>NIST 800-53 Control CM-3.a

#### <a name="configuration-change-control"></a>구성 변경 제어

**CM-3.a** 조직에서는 구성을 통해 제어되는 정보 시스템 변경 유형을 결정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 구성을 통해 제어되는 고객 배포 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함) 변경 유형을 결정할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-3b"></a>NIST 800-53 Control CM-3.b

#### <a name="configuration-change-control"></a>구성 변경 제어

**CM-3.b** 조직에서는 구성을 통해 제어되는 시스템 변경 제안을 검토한 후 보안 영향 분석을 명시적으로 고려하여 변경을 승인 또는 거부합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 구성을 통해 제어되는 고객 배포 리소스 변경 제안을 검토할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-3c"></a>NIST 800-53 Control CM-3.c

#### <a name="configuration-change-control"></a>구성 변경 제어

**CM-3.c** 조직에서는 정보 시스템과 관련된 구성 변경 결정 사항을 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스와 관련된 구성을 통해 제어되는 변경을 문서화할 책임이 있습니다(CM-03.b 참조). |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-3d"></a>NIST 800-53 Control CM-3.d

#### <a name="configuration-change-control"></a>구성 변경 제어

**CM-3.d** 조직에서는 승인된 구성을 통해 제어되는 변경을 정보 시스템에 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 CM-03.b에 승인된 구성을 통해 제어되는 변경을 구현할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-3e"></a>NIST 800-53 Control CM-3.e

#### <a name="configuration-change-control"></a>구성 변경 제어

**CM-3.e** 조직에서는 구성을 통해 제어되는 정보 시스템 변경 기록을 [할당: 조직에서 정의한 기간] 동안 보관합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 구성을 통해 제어되는 고객 배포 리소스 변경 레코드를 보관할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-3f"></a>NIST 800-53 Control CM-3.f

#### <a name="configuration-change-control"></a>구성 변경 제어

**CM-3.f** 조직에서는 구성을 통해 제어되는 정보 시스템 변경과 관련된 작업을 감사 및 검토합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 구성 변경을 감사 및 검토할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-3g"></a>NIST 800-53 Control CM-3.g

#### <a name="configuration-change-control"></a>구성 변경 제어

**CM-3.g** 조직에서는 [선택(하나 이상): [할당: 조직에서 정의한 빈도]마다 [할당: 조직에서 정의한 구성 변경 조건]]을 소집하는 [할당: 조직에서 정의한 구성 변경 제어 요소(예: 위원회, 이사회)]를 통해 구성 변경 제어 작업을 조정 및 감독합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 구성 변경 제어 작업을 조정 및 감독할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-3-1a"></a>NIST 800-53 Control CM-3 (1).a

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>구성 변경 제어 | 자동화 문서 / 알림 / 변경 금지

**CM-3 (1).a** 조직에서는 자동화된 메커니즘을 사용하여 제안된 정보 시스템 변경을 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 자동화 메커니즘을 사용하여 제안된 변경을 문서화할 책임이 있습니다(CM-03.b 참조). |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-3-1b"></a>NIST 800-53 Control CM-3 (1).b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>구성 변경 제어 | 자동화 문서 / 알림 / 변경 금지

**CM-3 (1).b** 조직에서는 자동화된 메커니즘을 사용하여 [할당: 조직에서 정의한 승인 기관]에 제안된 정보 시스템 변경을 알리고 변경 승인을 요청합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 자동화된 메커니즘을 사용하여 제안된 고객 배포 리소스 변경을 라우팅하고 승인을 요청할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-3-1c"></a>NIST 800-53 Control CM-3 (1).c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>구성 변경 제어 | 자동화 문서 / 알림 / 변경 금지

**CM-3 (1).c** 조직에서는 자동화된 메커니즘을 사용하여 [할당: 조직에서 정의한 기간]까지 승인 또는 거부되지 않은 제안된 정보 시스템 변경을 강조합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 자동화된 메커니즘을 사용하여 검토되지 않은 변경 제한을 강조할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-3-1d"></a>NIST 800-53 Control CM-3 (1).d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>구성 변경 제어 | 자동화 문서 / 알림 / 변경 금지

**CM-3 (1).d** 조직에서는 자동화된 메커니즘을 사용하여 지정된 승인을 받을 때까지 정보 시스템 변경을 금지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 자동화된 메커니즘을 사용하여 승인되지 않은 고객 배포 리소스 변경 구현을 금지할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-3-1e"></a>NIST 800-53 Control CM-3 (1).e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>구성 변경 제어 | 자동화 문서 / 알림 / 변경 금지

**CM-3 (1).e** 조직에서는 자동화된 메커니즘을 사용하여 모든 정보 시스템 변경을 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 자동화된 메커니즘을 사용하여 모든 구현된 고객 배포 리소스 변경을 문서화할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-3-1f"></a>NIST 800-53 Control CM-3 (1).f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>구성 변경 제어 | 자동화 문서 / 알림 / 변경 금지

**CM-3 (1).f** 조직에서는 자동화된 메커니즘을 사용하여 승인된 정보 시스템 변경이 완료되는 시기를 [할당: 조직에서 정의한 담당자]에게 알립니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 자동화된 메커니즘을 사용하여 승인된 고객 배포 리소스 변경이 완료되는 시기를 알리는 알림을 제공할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800-53 Control CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>구성 변경 제어 | 변경 테스트/유효성 검사/문서화

**CM-3 (2)** 조직에서는 운영 체제에 변경을 구현하기 전에 정보 시스템 변경 내용을 테스트하고, 유효성을 검사하고, 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 변경 내용을 구현하기 전에 고객 배포 리소스 변경 내용을 테스트하고, 유효성을 검사하고, 문서화할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800-53 Control CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>구성 변경 제어 | 보안 담당자

**CM-3 (4)** 조직에서는 정보 보안 담당자를 [할당: 조직에서 정의한 구성 변경 제어 요소]의 구성원으로 할당합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 정보 보안 담당자를 CM-03.g에 정의된 변경 제어 요소 구성원으로 할당할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800-53 Control CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>구성 변경 제어 | 암호화 관리

**CM-3 (6)** 조직에서는 [할당: 조직에서 정의한 보안 조치]를 제공하기 위해 사용되는 암호화 메커니즘이 구성 관리를 따르도록 보장합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 암호화 메커니즘이 구성 관리를 따르도록 보장할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-4"></a>NIST 800-53 Control CM-4

#### <a name="security-impact-analysis"></a>보안 영향 분석

**CM 4** 조직에서는 변경 내용을 구현하기 전에 정보 시스템 변경 내용을 분석하여 잠재적 보안 영향을 확인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 제안된 고객 배포 리소스 변경 내용을 분석할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800-53 Control CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>보안 영향 분석 | 별도의 테스트 환경

**CM-4 (1)** 조직에서는 운영 환경에 변경 내용을 구현하기 전에 별도의 테스트 환경에서 정보 시스템 변경 내용을 분석하여 결함, 약점, 비호환성 또는 의도적 악의로 인한 보안 영향을 살펴봅니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 운영 환경에 변경 내용을 구현하기 전에 테스트 환경에서 제안된 고객 배포 리소스 변경 내용을 분석할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-5"></a>NIST 800-53 Control CM-5

#### <a name="access-restrictions-for-change"></a>변경 액세스 제한

**CM-5** 조직에서는 정보 시스템 변경과 관련된 물리적 및 논리적 액세스 제한을 정의, 문서화, 승인 및 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 배포된 리소스를 보고 제어할 수 있는 사용자를 철저하게 제어하는 역할에 사용자를 할당함으로써 역할 기반 액세스 제어를 사용하여 Azure Active Directory 계정 권한을 구현합니다. Active Directory 계정 권한은 보안 그룹에 사용자를 할당함으로써 역할 기반 액세스 제어를 사용하여 구현됩니다. 이러한 보안 그룹은 운영 체제 구성과 관련하여 사용자가 수행할 수 있는 작업을 제어합니다. 이러한 역할 기반 체계는 업무 요구 사항을 충족시키기 위해 고객이 확장할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800-53 Control CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>변경 액세스 제한 | 자동 액세스 적용/감사

**CM-5 (1)** 정보 시스템은 액세스 제한을 적용하고 적용 작업의 감사를 지원합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 배포된 리소스를 보고 제어할 수 있는 사용자를 철저하게 제어하는 역할에 사용자를 할당함으로써 역할 기반 액세스 제어를 사용하여 Azure Active Directory 계정 권한을 구현합니다. Active Directory 계정 권한은 보안 그룹에 사용자를 할당함으로써 역할 기반 액세스 제어를 사용하여 구현됩니다. 이러한 보안 그룹은 운영 체제 구성과 관련하여 사용자가 수행할 수 있는 작업을 제어합니다. 모든 액세스 및 액세스 시도를 감사합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800-53 Control CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>변경 액세스 제한 | 시스템 변경 내용 검토

**CM-5 (2)** 조직에서는 [할당: 조직에서 정의한 빈도] 및 [할당: 조직에서 정의한 상황]에 따라 정보 시스템 변경 내용을 검토하여 무단 변경 발생 여부를 확인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스의 변경 내용을 검토하여 무단 변경 발생 여부를 확인할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800-53 Control CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>변경 액세스 제한 | 서명된 구성 요소

**CM-5 (3)** 정보 시스템은 [할당: 조직에서 정의한 소프트웨어 및 펌웨어 구성 요소]가 조직의 인정 및 승인을 받은 인증서를 사용하여 디지털 서명된 것으로 확인되기 전에는 해당 구성 요소의 설치를 금지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 가상 머신은 특정 응용 프로그램을 설치 및/또는 실행할 수 있는 사용자를 지정하는 Windows AppLocker를 구현합니다. 또한 모든 Windows 운영 체제 업데이트가 디지털로 서명됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-5-5a"></a>NIST 800-53 Control CM-5 (5).a

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>변경 액세스 제한 | 프로덕션/작업 권한 제한

**CM-5 (5).a** 조직에서는 프로덕션 또는 운영 환경 내에서 정보 시스템 구성 요소 및 시스템 관련 정보를 변경하는 권한을 제한합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 프로덕션 또는 운영 환경 내에서 변경 권한을 제한할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-5-5b"></a>NIST 800-53 Control CM-5 (5).b

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>변경 액세스 제한 | 프로덕션/작업 권한 제한

**CM-5 (5).b** 조직에서는 [할당: 조직에서 정의한 빈도]마다 권한을 검토 및 재평가합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 CM-05(05).a에 정의된 권한을 검토 및 재평가할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-6a"></a>NIST 800-53 Control CM-6.a

#### <a name="configuration-settings"></a>구성 설정

**CM-6.a** 조직에서는 운영 요구 사항과 일치하는 가장 제한적인 모드를 반영하는 [할당: 조직에서 정의한 보안 구성 확인 목록]을 사용하여 정보 시스템 내에서 사용되는 정보 기술 제품에 대한 구성 설정을 지정 및 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에는 배포된 각 가상 머신에 대한 DSC(필요한 상태 구성) 기준이 포함되어 있습니다. 이러한 선언적 PowerShell 스크립트는 적용될 리소스를 정의 및 구성합니다. 이 솔루션을 통해 배포된 리소스에 대해 포함된 기준 DSC는 고객이 업무 요구 사항에 맞게 확장할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-6b"></a>NIST 800-53 Control CM-6.b

#### <a name="configuration-settings"></a>구성 설정

**CM-6.b** 조직에서는 구성 설정을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에는 배포된 각 가상 머신에 대한 DSC(필요한 상태 구성) 기준이 포함되어 있습니다. 기준은 사용자 지정 스크립트 가상 머신 확장을 사용하여 배포하는 동안 가상 머신에 자동으로 적용됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-6c"></a>NIST 800-53 Control CM-6.c

#### <a name="configuration-settings"></a>구성 설정

**CM-6.c** 조직에서는 [할당: 조직에서 정의한 운영 요구 사항]을 기반으로 [할당: 조직에서 정의한 정보 시스템 구성 요소]에 대해 지정된 구성 설정의 편차를 식별, 문서화 및 승인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 대해 지정된 구성 설정의 모든 편차를 식별, 문서화 및 승인할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-6d"></a>NIST 800-53 Control CM-6.d

#### <a name="configuration-settings"></a>구성 설정

**CM-6.d** 조직에서는 조직의 정책 및 절차에 따라 구성 설정 변경을 모니터링 제어합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 자동화 DSC를 배포합니다. 자동화 DSC는 컴퓨터 구성을 특정 조직에서 정의한 구성에 맞춥니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800-53 Control CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>구성 설정 | 자동 중앙 관리/응용 프로그램/확인

**CM-6 (1)** 조직에서는 자동 머신을 사용하여 [할당: 조직에서 정의한 정보 시스템 구성 요소]의 구성 설정을 중앙에서 관리, 적용 및 확인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Azure Automation DSC를 배포합니다. 자동화 DSC는 컴퓨터 구성을 특정 조직에서 정의한 구성에 맞추고 변경 내용을 지속적으로 모니터링합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800-53 Control CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>구성 설정 | 권한 없는 변경에 대응

**CM-6 (2)** 조직에서는 [할당: 조직에서 정의한 보안 조치]를 사용하여 권한 없는 [할당: 조직에서 정의한 구성 설정] 변경에 대응합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Azure Automation DSC를 배포합니다. Automation DSC는 구성 오류를 감지하면 경고를 생성하거나 오류를 해결하도록 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-7a"></a>NIST 800-53 Control CM-7.a

#### <a name="least-functionality"></a>최소한의 기능

**CM-7.a** 조직에서는 필수 기능만 제공하도록 정보 시스템을 구성합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진을 통해 배포된 리소스는 원래 용도에 필요한 최소한의 기능을 제공하도록 구성됩니다. 배포된 각 가상 머신에 대한 DSC(필요한 상태 구성) 기준이 포함되어 있습니다. 이러한 선언적 PowerShell 스크립트는 적용될 리소스를 정의 및 구성합니다. 이 솔루션을 통해 배포된 리소스에 대해 포함된 기준 DSC는 고객이 업무 요구 사항을 충족하는 기능을 더욱 제한하도록 확장할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-7b"></a>NIST 800-53 Control CM-7.b

#### <a name="least-functionality"></a>최소한의 기능

**CM-7.b** 조직에서는 [할당: 조직에서 정의한 금지 또는 제한된 함수, 포트, 프로토콜 및/또는 서비스]의 사용을 금지하거나 제한합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 포트 및 프로토콜을 꼭 필요한 경우에만 사용하도록 제한하는 Azure Application Gateway 및 네트워크 보안 그룹을 배포합니다. 고객은 가상 머신의 Application Gateway, 네트워크 보안 그룹 및 DSC 기준을 추가로 구성하여 의도한 기능만 제공하도록 함수, 포트, 프로토콜 및 서비스 사용을 제한할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-7-1a"></a>NIST 800-53 Control CM-7 (1).a

#### <a name="least-functionality--periodic-review"></a>최소한의 기능 | 주기적으로 검토

**CM-7 (1).a** 조직에서는 [할당: 조직에서 정의한 빈도]마다 정보 시스템을 검토하여 불필요하거나 안전하지 않은 함수, 포트, 프로토콜 및 서비스를 식별합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함)를 검토하여 불필요하거나 안전하지 않은 함수, 포트, 프로토콜 및 서비스를 식별할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-7-1b"></a>NIST 800-53 Control CM-7 (1).b

#### <a name="least-functionality--periodic-review"></a>최소한의 기능 | 주기적으로 검토

**CM-7 (1).b** 조직에서는 [할당: 조직에서 정의한 정보 시스템 내부의 함수, 포트, 프로토콜 및 서비스 중 불필요하거나 안전하지 않은 것으로 간주되는 것]을 사용하지 않도록 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 불필요하거나 안전하지 않은 것으로 간주되는 함수, 포트, 프로토콜 및 서비스를 사용하지 않도록 설정할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800-53 Control CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>최소한의 기능 | 프로그램 실행 금지

**CM-7 (2)** 정보 시스템은 [선택(하나 이상): [할당: 소프트웨어 프로그램 사용 및 제한과 관련하여 조직에서 정의한 정책]; 소프트웨어 프로그램 사용 조건을 규정하는 규칙]에 따라 프로그램 실행을 금지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 정의 소프트웨어 프로그램 사용 정책에 따라 프로그램 실행을 방지할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-7-5a"></a>NIST 800-53 Control CM-7 (5).a

#### <a name="least-functionality--authorized-software--whitelisting"></a>최소한의 기능 | 권한이 부여된 소프트웨어/허용 목록

**CM-7 (5).a** 조직에서는 [할당: 정보 시스템에서 실행할 수 있는 권한이 부여된 조직 정의 소프트웨어 프로그램]을 식별합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 권한 있는 소프트웨어 프로그램을 식별할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-7-5b"></a>NIST 800-53 Control CM-7 (5).b

#### <a name="least-functionality--authorized-software--whitelisting"></a>최소한의 기능 | 권한이 부여된 소프트웨어/허용 목록

**CM-7 (5).b** 조직에서는 deny-all, permit-by-exception 정책을 사용하여 정보 시스템에서 권한 있는 소프트웨어 프로그램 예외를 허용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 deny-all, permit-by-exception 정책을 사용하여 고객 배포 리소스에서 권한 있는 소프트웨어 프로그램 예외를 허용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-7-5c"></a>NIST 800-53 Control CM-7 (5).c

#### <a name="least-functionality--authorized-software--whitelisting"></a>최소한의 기능 | 권한이 부여된 소프트웨어/허용 목록

**CM-7 (5).c** 조직에서는 [할당: 조직에서 정의한 빈도]마다 권한 있는 소프트웨어 프로그램 목록을 검토 및 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 권한 있는 소프트웨어 프로그램 목록을 검토 및 업데이트할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-8a"></a>NIST 800-53 Control CM-8.a

#### <a name="information-system-component-inventory"></a>정보 시스템 구성 요소 인벤토리

**CM-8.a** 조직에서는 현재 정보 시스템을 정확하게 반영하고, 정보 시스템의 권한 부여 경계 내부에 있는 모든 구성 요소를 포함하고, 세분성 수준이 추적 및 보고에 필요한 것으로 간주되는 수준이고, [할당: 조직에서 정의한 효과적인 정보 시스템 구성 요소 책임을 달성하는 데 필요한 정보]를 포함하는 정보 시스템 구성 요소 인벤토리를 개발 및 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 모든 리소스를 Azure Resource Manager 리소스 그룹에 배포합니다. Azure Resource Manager는 항상 배포된 리소스의 최신 목록을 제공하며, 사용자 지정을 통해 리소스에 태그를 지정하고 그룹화하여 인벤토리 관리에 사용할 수 있습니다. 이 솔루션을 통해 배포된 리소스에는 시스템 경계와 연결할 수 있는 특정 리소스 태그가 지정됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-8b"></a>NIST 800-53 Control CM-8.b

#### <a name="information-system-component-inventory"></a>정보 시스템 구성 요소 인벤토리

**CM-8.b** 조직에서는 [할당: 조직에서 정의한 빈도]마다 정보 시스템 구성 요소 인벤토리를 검토 및 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 모든 리소스를 Azure Resource Manager 리소스 그룹에 배포합니다. Azure Resource Manager는 항상 배포된 리소스의 최신 목록을 제공하며 이 목록은 Azure Portal에서 검토할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800-53 Control CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>정보 시스템 구성 요소 인벤토리 | 설치 중 업데이트/제거

**CM-8 (1)** 조직에서는 구성 요소 설치, 제거 및 정보 시스템 업데이트의 필수적인 부분으로써 정보 시스템 구성 요소 인벤토리를 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 모든 리소스를 Azure Resource Manager 리소스 그룹에 배포합니다. Azure Portal의 리소스 블레이드는 배포된 모든 리소스를 표시하며, 리소스가 배포 및 제거될 때마다 항상 최신 인벤토리를 제공합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800-53 Control CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>정보 시스템 구성 요소 인벤토리 | 자동 유지 관리

**CM-8 (2)** 조직에서는 자동화된 메커니즘을 사용하여 완전하고 정확하고 즉시 사용할 수 있는 최신 정보 시스템 구성 요소 인벤토리를 유지 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 모든 리소스를 Azure Resource Manager 리소스 그룹에 배포합니다. Azure Portal의 리소스 블레이드는 배포된 모든 리소스를 표시하며, 리소스가 배포 및 제거될 때마다 항상 최신 인벤토리를 제공합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-8-3a"></a>NIST 800-53 Control CM-8 (3).a

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>정보 시스템 구성 요소 인벤토리 | 권한 없는 구성 요소 자동 검색

**CM-8 (3).a** 조직에서는 [할당: 조직에서 정의한 빈도]마다 자동화된 메커니즘을 사용하여 정보 시스템 내에서 권한 없는 하드웨어, 소프트웨어 및 펌웨어 구성 요소를 검색합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 자동화된 메커니즘을 사용하여 고객 배포 리소스 내에서 권한 없는 소프트웨어를 검색할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-8-3b"></a>NIST 800-53 Control CM-8 (3).b

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>정보 시스템 구성 요소 인벤토리 | 권한 없는 구성 요소 자동 검색

**CM-8 (3).b** 조직에서는 권한 없는 구성 요소가 검색되면 다음과 같은 조치를 수행합니다. [선택(하나 이상): 검색된 구성 요소가 네트워크에 액세스하지 못하게 설정, 구성 요소 격리, [할당: 조직에서 정의한 담당자 또는 역할]에 통지].

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 권한 없는 소프트웨어가 검색되면 적절한 작업을 수행할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800-53 Control CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>정보 시스템 구성 요소 인벤토리 | 책임 정보

**CM-8 (4)** 조직에서는 [선택(하나 이상): 이름, 지위, 역할]을 기준으로 구성 요소 관리 책임이 있는 개인을 식별하기 위한 수단으로 정보 시스템 구성 요소 인벤토리 정보를 포함합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 모든 리소스를 Azure Resource Manager 리소스 그룹에 배포합니다. Azure 리소스 태그는 책임 및/또는 관리 목적으로 리소스를 분류하는 데 사용할 수 있는 키/값 쌍입니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800-53 Control CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>정보 시스템 구성 요소 인벤토리 | 구성 요소의 이중 회계 없음

**CM-8 (5)** 조직에서는 정보 시스템의 권한 부여 경계 내부에 있는 모든 구성 요소가 다른 정보 시스템 구성 요소 인벤토리에 복제되지 않았는지 확인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 모든 리소스를 Azure Resource Manager 리소스 그룹에 배포합니다. Azure Resource Manager는 항상 배포된 리소스의 최신 목록을 제공합니다. 이 솔루션을 통해 배포된 리소스에는 시스템 경계와 연결할 수 있는 특정 리소스 태그가 지정됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-9a"></a>NIST 800-53 Control CM-9.a

#### <a name="configuration-management-plan"></a>구성 관리 계획

**CM-9.a** 조직에서는 역할, 책임 및 구성 관리 프로세스와 절차를 다루는 정보 시스템에 대한 구성 관리 계획을 개발, 문서화 및 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 대한 구성 관리 계획을 개발, 문서화 및 구현할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-9b"></a>NIST 800-53 Control CM-9.b

#### <a name="configuration-management-plan"></a>구성 관리 계획

**CM-9.b** 조직에서는 시스템 개발 수명 주기 전체에 걸쳐 구성 항목을 식별하고 구성 항목의 구성을 관리하기 위한 프로세스를 수립하는 정보 시스템에 대한 구성 관리 계획을 개발, 문서화 및 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 대한 구성 관리 계획을 개발, 문서화 및 구현할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-9c"></a>NIST 800-53 Control CM-9.c

#### <a name="configuration-management-plan"></a>구성 관리 계획

**CM-9.c** 조직에서는 정보 시스템에 대한 구성 항목을 정의하고 구성 항목을 구성 관리 아래에 배치하는 정보 시스템에 대한 구성 관리 계획을 개발, 문서화 및 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 대한 구성 관리 계획을 개발, 문서화 및 구현할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-9d"></a>NIST 800-53 Control CM-9.d

#### <a name="configuration-management-plan"></a>구성 관리 계획

**CM-9.d** 조직에서는 권한 없는 공개 및 수정으로부터 구성 관리 계획을 보호하는 정보 시스템에 대한 구성 관리 계획을 수립, 문서화 및 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 대한 구성 관리 계획을 개발, 문서화 및 구현할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-10a"></a>NIST 800-53 Control CM-10.a

#### <a name="software-usage-restrictions"></a>소프트웨어 사용 제한

**CM-10.a** 조직에서는 계약서 및 저작권법에 따라 소프트웨어 및 관련 문서를 사용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진을 통해 배포된 리소스를 위해 Windows 및 SQL Server 라이선스가 포함됩니다. 이것은 Azure의 기본 기능입니다. 기존 소프트웨어 라이선스 계약을 보유한 조직에서는 대체 라이선스 모델을 배포하는 방안을 고려해 볼 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-10b"></a>NIST 800-53 Control CM-10.b

#### <a name="software-usage-restrictions"></a>소프트웨어 사용 제한

**CM-10.b** 조직에서는 복사 및 배포를 제어하기 위해 수량 라이선스의 보호를 받는 소프트웨어 및 관련 문서의 사용을 추적합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진을 통해 배포된 리소스를 위해 Windows 및 SQL Server 라이선스가 포함됩니다. 사용자는 라이선스 사용을 별도로 추적할 의무는 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-10c"></a>NIST 800-53 Control CM-10.c

#### <a name="software-usage-restrictions"></a>소프트웨어 사용 제한

**CM-10.c** 조직에서는 P2P 파일 공유 기술이 저작권법의 보호를 받는 콘텐츠를 무단으로 배포, 표시, 상영 또는 복제하는 데 사용되는 일이 없도록 P2P 파일 공유 기술을 제어 및 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진이 배포한 P2P 파일 공유 기능은 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800-53 Control CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>소프트웨어 사용 제한 | 오픈 소스 소프트웨어

**CM-10 (1)** 조직에서는 오픈 소스 소프트웨어의 사용과 관련하여 [할당: 조직에서 정의한 제한]을 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 구성 관리 정책에서 오픈 소스 소프트웨어 사용 제한을 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-11a"></a>NIST 800-53 Control CM-11.a

#### <a name="user-installed-software"></a>사용자가 설치한 소프트웨어

**CM-11.a** 조직에서는 사용자의 소프트웨어 설치를 관리하는 [할당: 조직에서 정의한 정책]을 수립합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 사용자가 소프트웨어를 설치하는 것과 관련된 정책을 수립할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-11b"></a>NIST 800-53 Control CM-11.b

#### <a name="user-installed-software"></a>사용자가 설치한 소프트웨어

**CM-11.b** 조직에서는 [할당: 조직에서 정의한 방법]을 통해 소프트웨어 설치 정책을 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 소프트웨어 설치 정책을 적용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-cm-11c"></a>NIST 800-53 Control CM-11.c

#### <a name="user-installed-software"></a>사용자가 설치한 소프트웨어

**CM-11.c** 조직에서는 [할당: 조직에서 정의한 빈도]마다 정책 준수를 모니터링합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스가 CM-11.a에 규정된 정책을 준수하는지 모니터링할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800-53 Control CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>사용자가 설치한 소프트웨어 | 권한 없는 설치 경고

**CM-11 (1)** 정보 시스템은 권한 없는 소프트웨어 설치가 검색되면 [할당: 조직에서 정의한 담당자 또는 역할]에게 경고합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 권한 없는 소프트웨어 설치가 검색되면 경고할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |

