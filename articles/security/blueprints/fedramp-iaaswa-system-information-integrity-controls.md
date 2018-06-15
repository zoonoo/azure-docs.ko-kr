---
title: Azure 보안 및 규정 준수 청사진 - FedRAMP 웹 응용 프로그램 자동화 - 시스템 및 정보 무결성
description: FedRAMP 웹 응용 프로그램 자동화 - 시스템 및 정보 무결성
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 0eca3c82aea287f6582bd56574512dce5e8e86c7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206558"
---
# <a name="system-and-information-integrity-si"></a>SI(시스템 및 정보 무결성)

> [!NOTE]
> 이러한 제어는 NIST와 미국 상무부에 의해 NIST 특별 출판물 800-53 개정 4의 일부로 정의됩니다. 각 제어에 대한 테스트 절차 및 지침에 대한 정보는 NIST 800-53 개정 4를 참조하세요.

## <a name="nist-800-53-control-si-1"></a>NIST 800-53 제어 SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>시스템 및 정보 무결성 정책 및 절차

**SI-1** 조직은 목적, 범위, 역할, 책임, 관리 약속, 조직 엔터티 간 조정 및 준수를 다루는 시스템 및 정보 무결성 정책 및 시스템 및 정보 무결성 정책과 관련 시스템 및 정보 무결성 제어의 구현을 용이하게 하는 절차를 개발, 문서화하여 [할당: 조직에서 정의한 담당자 또는 역할]에게 배포하고, [할당: 조직에서 정의한 빈도]마다 현재 시스템 및 정보 무결성 정책을 검토하고 업데이트하며, [할당: 조직에서 정의한 빈도]마다 시스템 및 정보 무결성 절차를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 시스템 및 정보 무결성 정책 및 절차가 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-2a"></a>NIST 800-53 제어 SI-2.a

#### <a name="flaw-remediation"></a>결함 수정

**SI-2.a** 조직은 정보 시스템 결함을 식별하고 보고하고 수정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Automation & Control 솔루션을 배포하여 이 아키텍처에서 배포된 Windows 가상 머신에 대한 업데이트 상태를 추적합니다. 대시보드에서 배포된 모든 Windows 서버에 대한 결함 수정 상태가 업데이트 관리 타일에 표시됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-2b"></a>NIST 800-53 제어 SI-2.b

#### <a name="flaw-remediation"></a>결함 수정

**SI-2.b** 조직은 설치하기 전에 효율성 및 잠재적인 부작용에 대한 결함 수정과 관련된 소프트웨어 및 펌웨어 업데이트를 테스트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스를 설치하기 전에 효율성 및 잠재적인 부작용에 대한 결함 수정과 관련된 업데이트를 테스트할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-2c"></a>NIST 800-53 제어 SI-2.c

#### <a name="flaw-remediation"></a>결함 수정

**SI 2.c** 조직은 업데이트 릴리스의 [할당: 조직에서 정의한 기간] 내에 보안 관련 소프트웨어 및 펌웨어 업데이트를 설치합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 Windows 가상 머신은 기본적으로 Windows 업데이트 서비스에서 자동 업데이트를 받도록 구성됩니다. 또한 이 솔루션에서는 필요할 때 Windows 서버에 패치를 배포하도록 업데이트 배포를 만들 수 있는 Automation & Control 솔루션도 배포합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-2d"></a>NIST 800-53 제어 SI-2.d

#### <a name="flaw-remediation"></a>결함 수정

**SI-2.d** 조직은 결함 수정을 조직 구성 관리 프로세스로 통합합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 구성 관리에 결함 수정을 포함할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-2-1"></a>NIST 800-53 제어 SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>결함 수정 | 중앙 관리

**SI-2 (1)** 조직은 결함 수정 프로세스를 중앙에서 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Automation & Control 솔루션을 배포하여 이 아키텍처에서 배포된 Windows 가상 머신에 대한 업데이트 상태를 추적합니다. 대시보드에서 배포된 모든 Windows 서버에 대한 결함 수정 상태가 업데이트 관리 타일에 표시됩니다. 필요한 경우 Windows 서버에 패치를 배포하도록 업데이트 배포를 만들 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-2-2"></a>NIST 800-53 제어 SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>결함 수정 | 자동화된 결함 수정 상태

**SI-2 (2)** 조직은 [할당: 조직에서 정의한 빈도]마다 자동화된 메커니즘을 사용하여 결함 수정과 관련하여 정보 시스템 구성 요소의 상태를 확인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Automation & Control 솔루션을 배포하여 이 아키텍처에서 배포된 Windows 가상 머신에 대한 업데이트 상태를 추적합니다. 관리되는 Windows 컴퓨터 각각의 경우 검색은 하루에 두 번 수행됩니다. 15분마다 Windows API가 호출되어 마지막 업데이트 시간을 쿼리하여 상태가 변경되었는지, 상태가 변경되었다면 준수 검사가 시작되었는지 확인합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-2-3a"></a>NIST 800-53 제어 SI-2 (3).a

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>결함 수정 | 결함 수정 시간 / 정정 작업 벤치마크

**SI-2 (3).a** 조직은 결함을 식별하여 수정할 때까지의 시간을 측정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스 내의 결함을 수정할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-2-3b"></a>NIST 800-53 제어 SI-2 (3).b

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>결함 수정 | 결함 수정 시간 / 정정 작업 벤치마크

**SI-2 (3).b** 조직은 정정 작업을 수행하기 위해 [할당: 조직에서 정의한 벤치마크]를 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 결함 수정 프로세스에 대한 엔터프라이즈 수준 벤치마크를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-3a"></a>NIST 800-53 제어 SI-3.a

#### <a name="malicious-code-protection"></a>악성 코드 방지

**SI-3.a** 조직은 정보 시스템 진입 및 종료 지점에서 악성 코드 방지 메커니즘을 사용하여 악성 코드를 감지하고 제거합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 배포된 모든 Windows 가상 머신에 대해 Microsoft 맬웨어 방지 가상 머신 확장을 사용하여 구현되는 호스트 기반 맬웨어 방지 보호를 배포합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-3b"></a>NIST 800-53 제어 SI-3.b

#### <a name="malicious-code-protection"></a>악성 코드 방지

**SI 3.b** 조직은 조직 구성 관리 정책 및 절차에 따라 새 릴리스가 제공될 때마다 악성 코드 방지 메커니즘을 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 배포된 모든 Windows 가상 머신에 대해 Microsoft 맬웨어 방지 가상 머신 확장을 사용하여 구현되는 호스트 기반 맬웨어 방지 보호를 배포합니다. 이 확장은 릴리스가 제공될 때마다 맬웨어 방지 엔진 및 보호 서명 둘 다 자동으로 업데이트되도록 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-3c"></a>NIST 800-53 제어 SI-3.c

#### <a name="malicious-code-protection"></a>악성 코드 방지

**SI 3.c** 조직은 악성 코드 방지 메커니즘을 구성하여 [할당: 조직에서 정의한 빈도]마다 정보 시스템의 정기적 검색, 조직 보안 정책에 따라 파일이 다운로드되거나 열리거나 실행될 때 [선택(하나 이상): 끝점, 네트워크 진입점/출구점]에서 외부 원본의 파일 실시간 검사 및 악성 코드 검색에 대한 응답으로 [선택(하나 이상): 악성 코드 차단, 악의적인 코드 격리, 관리자에게 경고 보내기, [할당: 조직에서 정의한 작업]]을 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 배포된 모든 Windows 가상 머신에 대해 Microsoft 맬웨어 방지 가상 머신 확장을 사용하여 구현되는 호스트 기반 맬웨어 방지 보호를 배포합니다. 이 확장은 실시간 검사 및 주기적 검사(매주)를 수행하고, 맬웨어 방지 엔진 및 보호 서명을 자동으로 업데이트하고, 자동 문제 해결 작업을 수행합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-3d"></a>NIST 800-53 제어 SI-3.d

#### <a name="malicious-code-protection"></a>악성 코드 방지

**SI 3.d** 조직은 악성 코드를 검색하고 제거하는 동안의 가양성 수신 및 정보 시스템의 가용성에 대한 잠재적 영향을 해결합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 악성 코드를 방지할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-3-1"></a>NIST 800-53 제어 SI-3 (1)

#### <a name="malicious-code-protection--central-management"></a>악성 코드 방지 | 중앙 관리

**SI-3 (1)** 조직은 악성 코드 방지 메커니즘을 중앙에서 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 배포된 모든 Windows 가상 머신에 대해 Microsoft 맬웨어 방지 가상 머신 확장을 사용하여 구현되는 호스트 기반 맬웨어 방지 보호를 배포합니다. Azure는 중앙 집중식 기능을 제공하여 맬웨어 방지 솔루션의 현재 상태를 검토합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-3-2"></a>NIST 800-53 제어 SI-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>악성 코드 방지 | 자동 업데이트

**SI 3 (2)** 정보 시스템은 자동으로 악성 코드 방지 메커니즘을 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 배포된 모든 Windows 가상 머신에 대해 Microsoft 맬웨어 방지 가상 머신 확장을 사용하여 구현되는 호스트 기반 맬웨어 방지 보호를 배포합니다. 이 확장은 릴리스가 제공될 때마다 맬웨어 방지 엔진 및 보호 서명 둘 다 자동으로 업데이트되도록 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-3-7"></a>NIST 800-53 제어 SI-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>악성 코드 방지 | 비서명 기반 검색

**SI-3 (7)** 정보 시스템은 비서명 기반 악성 코드 검색 메커니즘을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 배포된 모든 Windows 가상 머신에 대해 Microsoft 맬웨어 방지 가상 머신 확장을 사용하여 구현되는 호스트 기반 맬웨어 방지 보호를 배포합니다. 이 확장은 추론적 검색을 수행하도록 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-4a"></a>NIST 800-53 제어 SI-4.a

#### <a name="information-system-monitoring"></a>정보 시스템 모니터링

**SI-4.a** 조직은 정보 시스템을 모니터링하여 [할당: 조직에서 정의한 모니터링 목표]에 따라 공격 및 잠재적 공격 지표와 권한이 없는 로컬, 네트워크, 원격 연결을 검색합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Log Analytics와 보안 및 감사 솔루션을 배포합니다. 이 솔루션은 보안 상태, 공격 및 잠재적 공격 지표의 전체적인 보기를 제공합니다. 보안 및 감사 대시보드는 배포된 관리 솔루션에서 사용 가능한 데이터를 사용하여 배포된 리소스의 보안 상태에 대한 높은 수준의 통찰력을 제공합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-4b"></a>NIST 800-53 제어 SI-4.b

#### <a name="information-system-monitoring"></a>정보 시스템 모니터링

**SI-4.b** 조직은 [할당: 조직에서 정의한 기술 및 방법]을 통해 정보 시스템의 무단 사용을 식별합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 보안 및 감사 솔루션을 배포합니다. 식별 및 액세스 도메인은 대시보드에 실패한 로그온 시도 횟수 및 현재 로그인된 계정 수를 포함하는 정보 시스템 ID 상태에 대한 개요를 제공합니다. 이 대시보드에 표시되는 정보를 통해 잠재적으로 의심스러운 활동을 식별할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-4c"></a>NIST 800-53 제어 SI-4.c

#### <a name="information-system-monitoring"></a>정보 시스템 모니터링

**SI-4.c** 조직은 정보 시스템 내에 모니터링 장치를 전략적으로 배포하여 조직에서 지정한 필수 정보를 수집하고 시스템 내의 임시 위치에 배포하여 조직에서 원하는 특정 유형의 트랜잭션을 추적합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Log Analytics와 보안 및 감사 솔루션을 배포합니다. 보안 및 감사 대시보드는 배포된 관리 솔루션에서 사용 가능한 데이터를 사용하여 VM 운영 체제 모니터링 데이터에 대한 정보를 비롯한 배포된 리소스의 보안 상태에 대한 높은 수준의 통찰력을 제공합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-4d"></a>NIST 800-53 제어 SI-4.d

#### <a name="information-system-monitoring"></a>정보 시스템 모니터링

**SI-4.d** 조직은 침입 모니터링 도구에서 가져온 정보를 권한이 없는 액세스, 수정 및 삭제로부터 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 논리적 액세스 제어는 무단 액세스, 수정, 삭제로부터 이 청사진 내의 모니터링 정보를 보호하는 데 사용됩니다. Azure Active Directory는 역할 기반 그룹 멤버 자격을 사용하여 승인된 논리적 액세스를 적용합니다. 모니터링 정보를 보고 모니터링 도구를 사용하는 기능은 이러한 권한이 필요한 사용자로 제한될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-4e"></a>NIST 800-53 제어 SI-4.e

#### <a name="information-system-monitoring"></a>정보 시스템 모니터링

**SI-4.e** 조직은 법 적용 정보, 인텔리전스 정보 또는 정보의 기타 신뢰할 수 있는 원본을 기반으로 조직의 운영과 자산, 개인, 다른 조직 또는 국가에 대한 위험이 증가된 것으로 표시될 때마다 정보 시스템 모니터링 작업의 수준을 강화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스를 모니터링할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-4f"></a>NIST 800-53 제어 SI-4.f

#### <a name="information-system-monitoring"></a>정보 시스템 모니터링

**SI-4.f** 조직은 적용 가능한 연방 법률, 행정 명령, 지시, 정책 또는 규정에 따른 정보 시스템 모니터링 작업과 관련하여 법적 견해를 얻습니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스를 모니터링할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-4g"></a>NIST 800-53 제어 SI-4.g

#### <a name="information-system-monitoring"></a>정보 시스템 모니터링

**SI-4.g** 조직은 [할당: 조직에서 정의한 정보 시스템 모니터링 정보]를 [할당: 조직에서 정의한 담당자 또는 역할]에 [선택(하나 이상): 필요에 따라, [할당: 조직에서 정의한 빈도]마다] 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스를 모니터링할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-1"></a>NIST 800-53 제어 SI-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>정보 시스템 모니터링 | 시스템 차원의 침입 검색 시스템

**SI-4 (1)** 조직은 정보 시스템 차원의 침입 검색 시스템에 개별 침입 검색 도구를 연결하고 구성합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스를 모니터링할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-2"></a>NIST 800-53 제어 SI-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>정보 시스템 모니터링 | 실시간 분석용 자동화된 도구

**SI-4 (2)** 조직은 자동화된 도구를 사용하여 거의 실시간으로 이벤트를 분석할 수 있습니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Log Analytics 및 다양한 솔루션(보안 및 감사 솔루션 포함)을 배포합니다. Log Analytics는 배포된 리소스에서 이벤트를 거의 실시간으로 분석할 수 있습니다. 관리 솔루션은 솔루션 도메인에서 보안 상태에 대한 종합적인 보기를 제공합니다. Log Analytics는 배포된 관리 솔루션에서 사용 가능한 데이터를 사용하여 배포된 리소스의 보안 상태에 대한 정보를 제공합니다. 정의된 조건에 따라 경고를 생성하도록 Log Analytics를 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-4"></a>NIST 800-53 제어 SI-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>정보 시스템 모니터링 | 인바운드 및 아웃바운드 통신 트래픽

**SI-4 (4)** 정보 시스템은 [할당: 조직에서 정의한 빈도]마다 일반적이지 않거나 권한이 없는 작업 또는 조건에 대해 인바운드 및 아웃바운드 통신 트래픽을 모니터링합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스를 모니터링할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-5"></a>NIST 800-53 제어 SI-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>정보 시스템 모니터링 | 시스템에서 생성된 경고

**SI-4 (5)** 정보 시스템은 [할당: 조직에서 정의한 손상 지표]와 같은 손상 또는 잠재적 손상에 대한 징후가 발생할 때 [할당: 조직에서 정의한 담당자 또는 역할]에게 경고를 표시합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 보안 및 감사 솔루션을 포함하여 다양한 관리 솔루션을 배포합니다. Log Analytics는 배포된 리소스에서 이벤트를 거의 실시간으로 분석할 수 있습니다. 관리 솔루션은 솔루션 도메인에서 보안 상태에 대한 종합적인 보기를 제공합니다. 정의된 조건에 따라 경고를 생성하도록 Log Analytics를 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-11"></a>NIST 800-53 제어 SI-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>정보 시스템 모니터링 | 통신 트래픽 이상 분석

**SI-4 (11)** 조직은 정보 시스템의 외부 경계 및 선택한 [할당: 조직에서 정의한 시스템 내부 지점(예: 하위 네트워크, 하위 시스템)]에서 아웃바운드 통신 트래픽을 분석하여 이상을 검색합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대해 통신 트래픽 이상을 분석할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-14"></a>NIST 800-53 제어 SI-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>정보 시스템 모니터링 | 무선 침입 검색

**SI-4 (14)** 조직은 무선 침입 검색 시스템을 사용하여 Rogue 무선 장치를 식별하고 정보 시스템에 대한 공격 시도 및 잠재적 손상/위반을 검색합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | Azure 데이터 센터에서는 무선 장치를 포함하여 고객이 제어하는 하드웨어를 사용할 수 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 AC-18에 설명된 대로 분기별 Rogue 무선 신호를 정기적으로 모니터링합니다. <br /> Microsoft Azure는 PaaS 및 IaaS 고객을 대신하여 이 컨트롤을 구현합니다. |


 ### <a name="nist-800-53-control-si-4-16"></a>NIST 800-53 제어 SI-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>정보 시스템 모니터링 | 모니터링 정보 상호 연결

**SI-4 (16)** 조직은 정보 시스템 전체에서 사용되는 모니터링 도구의 정보를 상호 연결합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Log Analytics 및 다양한 솔루션(보안 및 감사 솔루션 포함)을 배포합니다. Log Analytics는 배포된 관리 솔루션에서 사용 가능한 데이터를 사용하여 배포된 리소스의 보안 상태에 대한 정보를 제공합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-18"></a>NIST 800-53 제어 SI-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>정보 시스템 모니터링 | 트래픽 분석 / 비밀 반출

**SI-4 (18)** 조직은 정보 시스템의 외부 경계(즉, 시스템 경계) 및 [할당: 조직에서 정의한 시스템 내부 지점(예: 하위 시스템, 하위 네트워크)]에서 아웃바운드 통신 트래픽을 분석하여 정보의 비밀 반출을 검색할 수 있습니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대해 통신 트래픽을 분석할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-19"></a>NIST 800-53 제어 SI-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>정보 시스템 모니터링 | 위험도가 높은 개인

**SI-4 (19)** 조직은 [할당: 조직에서 정의한 원본]에서 위험 수준이 증가된 것으로 식별된 개인에 대한 [할당: 조직에서 정의한 추가 모니터링]을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 위험도가 높은 개인을 모니터링할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-20"></a>NIST 800-53 제어 SI-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>정보 시스템 모니터링 | 권한 있는 사용자

**SI-4 (20)** 조직은 권한 있는 사용자에 대한 [할당: 조직에서 정의한 추가 모니터링]을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 권한 있는 사용자를 모니터링할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-22"></a>NIST 800-53 제어 SI-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>정보 시스템 모니터링 | 권한이 없는 네트워크 서비스

**SI-4 (22)** 정보 시스템은 [할당: 조직에서 정의한 권한 부여 또는 승인 프로세스]에 의해 권한이 없거나 승인되지 않은 네트워크 서비스를 검색하고 [선택(하나 이상): 감사, [할당: 조직에서 정의한 담당자 또는 역할]에게 경고]합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 권한이 없는 네트워크 서비스를 검색할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-23"></a>NIST 800-53 제어 SI-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>정보 시스템 모니터링 | 호스트 기반 장치

**SI-4 (23)** 조직은 [할당: 조직에서 정의한 정보 시스템 구성 요소]에서 [할당: 조직에서 정의한 호스트 기반 모니터링 메커니즘]을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 호스트 기반 모니터링 기능의 데이터를 비롯한 배포된 리소스의 모니터링 데이터를 수집합니다. Microsoft Monitoring Agent는 모든 Windows 가상 머신에 설치되어 Log Analytics 및 다른 관리 솔루션에서 사용하는 모니터링 데이터를 수집합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-4-24"></a>NIST 800-53 제어 SI-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>정보 시스템 모니터링 | 손상 지표

**SI-4 (24)** 정보 시스템은 손상 지표를 검색, 수집, 배포 및 사용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대한 손상 지표를 검색, 수집, 배포 및 사용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-5a"></a>NIST 800-53 제어 SI-5.a

#### <a name="security-alerts-advisories-and-directives"></a>보안 경고, 권고 및 지시

**SI-5.a** 조직은 [할당: 조직에서 정의한 외부 조직]에서 지속적으로 정보 시스템 보안 경고, 권고 및 지시를 받습니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함)에 대한 보안 경고, 권고 및 지시를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-5b"></a>NIST 800-53 제어 SI-5.b

#### <a name="security-alerts-advisories-and-directives"></a>보안 경고, 권고 및 지시

**SI-5.b** 조직은 필요한 경우 내부 보안 경고, 권고 및 지시를 생성합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대한 보안 경고, 권고 및 지시를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-5c"></a>NIST 800-53 제어 SI-5.c

#### <a name="security-alerts-advisories-and-directives"></a>보안 경고, 권고 및 지시

**SI-5.c** 조직은 보안 경고, 권고 및 지시를 [선택(하나 이상): [할당: 조직에서 정의한 담당자 또는 역할], [할당: 조직에서 정의한 조직 내의 요소], [할당: 조직에서 정의한 외부 조직]]에 전달합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대한 보안 경고, 권고 및 지시를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-5d"></a>NIST 800-53 제어 SI-5.d

#### <a name="security-alerts-advisories-and-directives"></a>보안 경고, 권고 및 지시

**SI-5.d** 조직은 설정된 타임프레임에 따라 보안 지시를 구현하거나 문제가 발생한 조직에 비준수 정도를 알립니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대한 보안 경고, 권고 및 지시를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-5-1"></a>NIST 800-53 제어 SI-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>보안 경고, 권고 및 지시 | 자동화된 경고 및 권고

**SI-5 (1)** 조직은 자동화된 메커니즘을 사용하여 조직 전반에 걸쳐 사용할 수 있는 보안 경고 및 권고 정보를 만듭니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대한 보안 경고, 권고 및 지시를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-6a"></a>NIST 800-53 제어 SI-6.a

#### <a name="security-function-verification"></a>보안 기능 확인

**SI-6.a** 정보 시스템은 [할당: 조직에서 정의한 보안 기능]이 올바르게 작동하는지 확인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함)에 대한 보안 기능을 확인할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-6b"></a>NIST 800-53 제어 SI-6.b

#### <a name="security-function-verification"></a>보안 기능 확인

**SI-6.b** 정보 시스템은 [선택(하나 이상): [할당: 조직에서 정의한 시스템 전환 상태 시], 적절한 권한이 있는 사용자의 명령 실행 시, [할당: 조직에서 정의한 빈도]마다] 확인 작업을 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대해 보안 기능을 확인할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-6c"></a>NIST 800-53 제어 SI-6.c

#### <a name="security-function-verification"></a>보안 기능 확인

**SI-6.c** 정보 시스템은 실패한 보안 확인 테스트를 [할당: 조직에서 정의한 담당자 또는 역할]에게 알립니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대해 보안 기능을 확인할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-6d"></a>NIST 800-53 제어 SI-6.d

#### <a name="security-function-verification"></a>보안 기능 확인

**SI-6.d** 정보 시스템은 이상 검색 시 [선택(하나 이상): 정보 시스템 종료, 정보 시스템 다시 시작, [할당: 조직에서 정의한 대체 작업]] 작업을 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대해 보안 기능을 확인할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-7"></a>NIST 800-53 제어 SI-7

#### <a name="software-firmware-and-information-integrity"></a>소프트웨어, 펌웨어 및 정보 무결성

**SI-7** 조직은 무결성 확인 도구를 사용하여 [할당: 조직에서 정의한 소프트웨어, 펌웨어 및 정보]에 대한 무단 변경을 검색합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 가상 머신은 Windows 운영 체제를 실행합니다. Windows는 WRP(Windows 리소스 보호) 기능을 통해 권한 있는 Windows 시스템 업데이트 또는 Windows의 일부로 설치되는 핵심 시스템 파일의 복구, 실시간 파일 무결성 유효성 검사 및 보호 기능을 제공합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-7-1"></a>NIST 800-53 제어 SI-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>소프트웨어, 펌웨어 및 정보 무결성 | 무결성 검사

**SI-7 (1)** 정보 시스템은 [선택(하나 이상): 시작 시, [할당: 조직에서 정의한 전환 상태 또는 보안 관련 이벤트]에서, [할당: 조직에서 정의한 빈도]마다] [할당: 조직에서 정의한 소프트웨어, 펌웨어 및 정보]의 무결성 검사를 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 가상 머신은 Windows 운영 체제를 실행합니다. Windows는 WRP(Windows 리소스 보호) 기능을 통해 권한 있는 Windows 시스템 업데이트 또는 Windows의 일부로 설치되는 핵심 시스템 파일의 복구, 실시간 파일 무결성 유효성 검사 및 보호 기능을 제공합니다. WRP를 통해 실시간 무결성 검사를 수행할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-7-2"></a>NIST 800-53 제어 SI-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>소프트웨어, 펌웨어 및 정보 무결성 | 자동화된 무결성 위반 알림

**SI-7 (2)** 조직은 무결성 확인 중 불일치가 검색되면 [할당: 조직에서 정의한 담당자 또는 역할]에게 알림을 제공하는 자동화된 도구를 사용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 가상 머신은 Windows 운영 체제를 실행합니다. Windows는 WRP(Windows 리소스 보호) 기능을 통해 권한 있는 Windows 시스템 업데이트 또는 Windows의 일부로 설치되는 핵심 시스템 파일의 복구, 실시간 파일 무결성 유효성 검사 및 보호 기능을 제공합니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-7-5"></a>NIST 800-53 제어 SI-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>소프트웨어, 펌웨어 및 정보 무결성 | 무결성 위반에 대한 자동화된 응답

**SI-7 (5)** 정보 시스템은 무결성 위반이 검색되면 자동으로 [선택(하나 이상): 정보 시스템 종료, 정보 시스템 다시 시작, [할당: 조직에서 정의한 보안 세이프가드] 구현] 작업을 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스 내의 무결성 위반에 대해 자동으로 응답할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-7-7"></a>NIST 800-53 제어 SI-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>소프트웨어, 펌웨어 및 정보 무결성 | 검색 및 대응의 통합

**SI-7 (7)** 조직은 무단 [할당: 정보 시스템에 대해 조직에서 정의한 보안 관련 변경]의 검색을 조직의 인시던트 응답 기능에 통합합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대한 소프트웨어 및 정보 무결성을 보호할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-7-14"></a>NIST 800-53 제어 SI-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>소프트웨어, 펌웨어 및 정보 무결성 | 이진 또는 컴퓨터 실행 코드

**SI-7 (14)** 조직은 제한되거나 보증되지 않지만 소스 코드의 프로비전 없는 소스에서 이진 또는 컴퓨터 실행 코드의 사용을 금지하며, 매우 중요한 업무 또는 운영 요구 사항이며 권한 부여 담당자의 승인이 있는 경우에만 소스 코드 요구 사항에 예외를 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 시스템 및 정보 무결성 절차는 일부 소스에서 이진 또는 컴퓨터 실행 코드의 소스 코드를 가져오는 요구 사항을 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-8a"></a>NIST 800-53 제어 SI-8.a

#### <a name="spam-protection"></a>스팸 방지

**SI-8.a** 조직은 정보 시스템 진입점 및 출구점에서 스팸 방지 메커니즘을 사용하여 원하지 않는 메시지를 검색하고 작업을 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진의 일부로 배포된 메일 서버가 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-8b"></a>NIST 800-53 제어 SI-8.b

#### <a name="spam-protection"></a>스팸 방지

**SI-8.b** 조직은 조직 구성 관리 정책 및 절차에 따라 새 릴리스가 제공될 때 스팸 방지 메커니즘을 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진의 일부로 배포된 메일 서버가 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-8-1"></a>NIST 800-53 제어 SI-8 (1)

#### <a name="spam-protection--central-management"></a>스팸 방지 | 중앙 관리

**SI-8 (1)** 조직은 중앙에서 스팸 방지 메커니즘을 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진의 일부로 배포된 메일 서버가 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-si-8-2"></a>NIST 800-53 제어 SI-8 (2)

#### <a name="spam-protection--automatic-updates"></a>스팸 방지 | 자동 업데이트

**SI-8 (2)** 정보 시스템은 스팸 방지 메커니즘을 자동으로 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진의 일부로 배포된 메일 서버가 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-10"></a>NIST 800-53 제어 SI-10

#### <a name="information-input-validation"></a>정보 입력 유효성 검사

**SI-10** 정보 시스템은 [할당: 조직에서 정의한 정보 입력]의 유효성을 검사합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함)에 대한 정보 입력의 유효성을 검사할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-11a"></a>NIST 800-53 제어 SI-11.a

#### <a name="error-handling"></a>오류 처리

**SI-11.a** 정보 시스템은 악의적 사용자에 의해 이용될 수 있는 정보를 표시하지 않고 수정 작업에 필요한 정보를 제공하는 오류 메시지를 생성합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 리소스는 상업적 운영 체제 및 소프트웨어 응용 프로그램을 사용합니다. 이 소프트웨어는 업계 모범 사례를 사용하여 오류 메시지에 중요한 정보가 표시되지 않도록 합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-11b"></a>NIST 800-53 제어 SI-11.b

#### <a name="error-handling"></a>오류 처리

**SI-11.b** 정보 시스템은 [할당: 조직에서 정의한 담당자 또는 역할]에게만 오류 메시지를 표시합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 리소스는 상업적 운영 체제 및 소프트웨어 응용 프로그램을 사용합니다. 이 소프트웨어는 업계 모범 사례를 사용하여 메시지 수신에 사용되는 컨텍스트에 적절한 오류 메시지를 제공합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-12"></a>NIST 800-53 제어 SI-12

#### <a name="information-handling-and-retention"></a>정보 처리 및 보존

**SI-12** 조직은 적용 가능한 연방 법률, 행정 명령, 지시, 정책, 규정, 표준 및 운영 요구 사항에 따라 정보 시스템 내의 정보 및 시스템에서의 정보 출력을 처리하고 보존합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스(응용 프로그램, 운영 체제, 데이터베이스 및 소프트웨어 포함) 내의 정보 및 해당 리소스에서의 정보 출력을 처리하고 보존할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-si-16"></a>NIST 800-53 제어 SI-16

#### <a name="memory-protection"></a>메모리 보호

**SI-16** 정보 시스템은 [할당: 조직에서 정의한 보안 세이프가드]를 구현하여 해당 메모리를 무단 코드 실행으로부터 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 가상 머신은 Windows 운영 체제를 실행합니다. Windows에는 NX(No Execute), ASLR(Address Space Layout Randomization) 및 DEP(데이터 실행 방지) 등 제한된 메모리 위치에서 코드 실행을 방지하는 보호 기능이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |
