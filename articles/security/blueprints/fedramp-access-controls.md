---
title: Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화 - Access Control
description: FedRAMP 웹 응용 프로그램 자동화 - Access Control
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 3d30f889c4d7ed0dafcf8559e8987090c03f4d5e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31421500"
---
# <a name="access-control-ac"></a>AC(액세스 제어)

> [!NOTE]
> 이러한 제어는 NIST와 미국 상무부에 의해 NIST 특별 출판물 800-53 개정 4의 일부로 정의됩니다. 각 제어에 대한 테스트 절차 및 지침에 대한 정보는 NIST 800-53 개정 4를 참조하세요.

## <a name="nist-800-53-control-ac-1"></a>NIST 800-53 제어 AC-1

#### <a name="access-control-policy-and-procedures"></a>액세스 제어 정책 및 절차

**AC-1** 조직은 목적, 범위, 역할, 책임, 관리 약속, 조직 엔터티 간 조정 및 준수를 다루는 액세스 제어 정책 및 액세스 제어 정책과 관련 액세스 제어의 구현을 용이하게 하는 절차를 개발, 문서화하여 [할당: 조직에서 정의한 담당자 또는 역할]에게 배포하고, [할당: 조직에서 정의한 빈도]마다 현재 액세스 제어 정책을 검토하고 업데이트하며 [할당: 조직에서 정의한 빈도]마다 액세스 제어 절차를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책 및 절차가 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2a"></a>NIST 800-53 제어 AC-2.a

#### <a name="account-management"></a>계정 관리

**AC-2.a** 조직은 조직 업무/비즈니스 기능을 지원하기 위해 [할당: 조직에서 정의한 정보 시스템 계정 유형] 유형의 정보 시스템 계정을 식별하고 선택합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Azure Active Directory 사용자(솔루션 배포 및 Azure 리소스에 대한 액세스 관리에 사용), Windows OS 사용자(Active Directory에서 관리), SQL Server 서비스 계정과 같은 시스템 계정 유형을 사용하고 구현합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2b"></a>NIST 800-53 제어 AC-2.b

#### <a name="account-management"></a>계정 관리

**AC-2.b** 조직은 정보 시스템 계정에 계정 관리자를 할당합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 AC-02.a에서 식별한 계정에 관리자를 할당할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2c"></a>NIST 800-53 제어 AC-2.c

#### <a name="account-management"></a>계정 관리

**AC-2.c** 조직은 그룹 및 역할 멤버 자격을 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 제어하는 계정 유형(AC-02.a 참조)에 대해 역할 및 그룹 멤버 자격 조건을 설정할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2d"></a>NIST 800-53 제어 AC-2.d

#### <a name="account-management"></a>계정 관리

**AC-2.d** 조직은 정보 시스템의 권한 있는 사용자, 그룹 및 역할 멤버 자격, 각 계정에 대한 액세스 권한(즉, 권한) 및 기타 특성(필요한 경우)을 지정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 설정된 엔터프라이즈 수준 계정 권한 부여 프로세스를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2e"></a>NIST 800-53 제어 AC-2.e

#### <a name="account-management"></a>계정 관리

**AC-2.e** 조직은 정보 시스템 계정 만들기 요청에 대해 [할당: 조직에서 정의한 담당자 또는 역할]이 승인할 것을 요구합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 설정된 엔터프라이즈 수준 계정 권한 부여 프로세스를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2f"></a>NIST 800-53 제어 AC-2.f

#### <a name="account-management"></a>계정 관리

**AC-2.f** 조직은 [할당: 조직에서 정의한 절차 또는 조건]에 따라 정보 시스템 계정을 생성, 사용, 수정, 사용 안 함 및 제거합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 설정된 엔터프라이즈 수준 계정 관리 프로세스를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2g"></a>NIST 800-53 제어 AC-2.g

#### <a name="account-management"></a>계정 관리

**AC-2.g** 조직은 정보 시스템 계정 사용을 모니터링합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 보안 및 감사 솔루션의 ID 및 액세스 대시보드를 구현합니다. 이 대시보드를 통해 계정 관리자는 정보 시스템 계정 사용을 모니터링할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2h"></a>NIST 800-53 제어 AC-2.h

#### <a name="account-management"></a>계정 관리

**AC-2.h** 조직은 계정이 더 이상 필요하지 않은 경우, 사용자가 종료되거나 이전된 경우, 개인 정보 시스템 사용 또는 알 필요가 있는 변경 사항이 있는 경우 계정 관리자에게 알립니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 절차는 계정이 더 이상 필요하지 않은 경우 적절한 계정 관리자에게 알리는 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2i"></a>NIST 800-53 제어 AC-2.i

#### <a name="account-management"></a>계정 관리

**AC-2.i** 조직은 유효한 액세스 권한 부여, 의도된 시스템 사용, 조직 또는 관련 업무/비즈니스 기능에서 요구하는 기타 특성을 기반으로 정보 시스템에 대한 액세스 권한을 부여합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 절차는 액세스 권한 부여 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2j"></a>NIST 800-53 제어 AC-2.j

#### <a name="account-management"></a>계정 관리

**AC-2.j** 조직은 [할당: 조직에서 정의한 빈도]마다 계정 관리 요구 사항을 준수하는지 계정을 검토합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 필요한 빈도로 고객이 제어하는 계정을 검토하여 계정이 모든 조직 요구 사항을 준수하는지 확인할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-2k"></a>NIST 800-53 제어 AC-2.k

#### <a name="account-management"></a>계정 관리

**AC-2.k** 조직은 개인이 그룹에서 제거될 때 공유/그룹 계정 자격 증명(배포된 경우)을 다시 발행하는 프로세스를 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 절차는 그룹 계정 자격 증명을 관리하는 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-1"></a>NIST 800-53 제어 AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>계정 관리 | 자동화된 시스템 계정 관리

**AC-2 (1)** 조직은 자동화된 메커니즘을 사용하여 정보 시스템 계정 관리를 지원합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 보안 및 감사 솔루션의 ID 및 액세스 대시보드를 구현합니다. 이 대시보드를 통해 계정 관리자는 정보 시스템 계정 사용을 모니터링할 수 있습니다. 비정상적 활동이 의심되거나 미리 정의된 기타 이벤트가 발생할 때 경고를 보내도록 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-2"></a>NIST 800-53 제어 AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>계정 관리 | 임시/긴급 계정 제거

**AC-2 (2)** 정보 시스템이 [할당: 각 유형의 계정에 대해 조직에서 정의한 기간] 이후 임시 및 긴급 계정을 자동으로 [선택: 제거, 사용하지 않도록 설정]합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 임시 또는 긴급 계정을 배포하지 않습니다. 수동으로 사용하지 않도록 설정하지 않은 경우 배포된 도메인 컨트롤러는 35일 후에 모든 비활성 계정을 자동으로 사용하지 않도록 설정합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-3"></a>NIST 800-53 제어 AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>계정 관리 | 비활성 계정 사용 안 함

**AC-2 (3)** 정보 시스템이 [할당: 조직에서 정의한 기간] 이후 비활성 계정을 자동으로 사용하지 않도록 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint가 배포한 도메인 컨트롤러는 35일 동안 사용하지 않으면 모든 사용자 계정을 사용하지 않도록 구성되어 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-4"></a>NIST 800-53 제어 AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>계정 관리 | 자동화된 감사 동작

**AC-2 (4)** 정보 시스템은 계정 생성, 수정, 사용, 사용 안 함 및 제거 동작을 자동으로 감사하고 [할당: 조직에서 정의한 담당자 또는 역할]에게 알립니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Azure Active Directory 사용자, Windows OS 사용자, SQL Server 서비스 계정과 같은 시스템 계정 유형을 구현합니다. Azure Active Directory 계정 관리 작업은 Azure 활동 로그에 이벤트를 생성하고 OS 수준 계정 관리 작업은 시스템 로그에 이벤트를 생성합니다. 이러한 로그는 Log Analytics에 의해 수집되어 Log Analytics 작업 영역에 저장됩니다. 미리 정의된 이벤트가 발생할 때 경고를 보내도록 Log Analytics를 구성할 수 있습니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-5"></a>NIST 800-53 제어 AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>계정 관리 | 비활성 로그아웃

**AC-2 (5)** 조직은 [할당: 조직에서 정의한 예상 비활성 기간 또는 로그아웃 시점에 대한 설명] 시점에 사용자에게 로그아웃하도록 요구합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책은 일정 기간(또는 다른 요인) 동안 비활성 상태가 될 것이 예상될 때 사용자가 로그아웃하는 정책을 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-7a"></a>NIST 800-53 제어 AC-2 (7).a

#### <a name="account-management--role-based-schemes"></a>계정 관리 | 역할 기반 체계

**AC-2 (7).a** 조직은 허용된 정보 시스템 액세스 및 권한을 역할로 구성하는 역할 기반 액세스 체계에 따라 권한 있는 사용자 계정을 설정하고 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Azure Active Directory 사용자, Windows OS 사용자, SQL Server 서비스 계정과 같은 시스템 계정 유형을 구현합니다. Azure Active Directory 계정 권한은 역할에 사용자를 할당하여 역할 기반 액세스 제어를 통해 구현됩니다. Active Directory 계정 권한은 사용자를 보안 그룹에 할당하여 역할 기반 액세스 제어를 통해 구현됩니다. 이러한 역할 기반 체계는 업무 요구 사항을 충족시키기 위해 고객이 확장할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-7b"></a>NIST 800-53 제어 AC-2 (7).b

#### <a name="account-management--role-based-schemes"></a>계정 관리 | 역할 기반 체계

**AC-2 (7).b** 조직은 권한 있는 역할 할당을 모니터링합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 보안 및 감사 솔루션의 ID 및 액세스 대시보드를 구현합니다. 이 대시보드를 통해 계정 관리자는 정보 시스템 계정 사용을 모니터링할 수 있습니다. 이 솔루션을 쿼리하여 권한 있는 역할 할당을 보고할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-7c"></a>NIST 800-53 제어 AC-2 (7).c

#### <a name="account-management--role-based-schemes"></a>계정 관리 | 역할 기반 체계

**AC-2 (7).c** 조직은 권한 있는 역할 할당이 더 이상 적절하지 않을 때 [할당: 조직에서 정의한 작업]을 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 권한 있는 역할 할당이 더 이상 적절하지 않은 경우 고객이 제어하는 계정에 대해 작업을 수행해야 할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-9"></a>NIST 800-53 제어 AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>계정 관리 | 공유/그룹 계정의 사용에 대한 제한

**AC-2 (9)** 조직은 [할당: 공유/그룹 계정 설정을 위해 조직에서 정의한 조건]을 충족하는 공유/그룹 계정만 사용하도록 허용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포한 리소스에 공유/그룹 계정이 사용하도록 설정되어 있지 않습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-10"></a>NIST 800-53 제어 AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>계정 관리 | 공유/그룹 계정 자격 증명 종료

**AC-2 (10)** 정보 시스템은 멤버가 그룹을 떠날 때 공유/그룹 계정 자격 증명을 종료합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포한 리소스에 공유/그룹 계정이 사용하도록 설정되어 있지 않습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-11"></a>NIST 800-53 제어 AC-2 (11)

#### <a name="account-management--usage-conditions"></a>계정 관리 | 사용 조건

**AC-2 (11)** 정보 시스템은 [할당: 조직에서 정의한 정보 시스템 계정]에 대해 [할당: 조직에서 정의한 환경 및/또는 사용 조건]을 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 그룹 정책을 Active Directory에서 설정하고 기간 제한이나 다른 계정 사용 조건을 구현하도록 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-12a"></a>NIST 800-53 제어 AC-2 (12).a

#### <a name="account-management--account-monitoring--atypical-usage"></a>계정 관리 | 계정 모니터링/비정상적 사용

**AC-2 (12).a** 조직은 [할당: 조직에서 정의한 비정상적 사용]에 대해 정보 시스템 계정을 모니터링합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 보안 및 감사 솔루션의 ID 및 액세스 대시보드를 구현합니다. 이 대시보드를 통해 계정 관리자는 배포된 리소스에 대한 액세스 시도를 모니터링할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-12b"></a>NIST 800-53 제어 AC-2 (12).b

#### <a name="account-management--account-monitoring--atypical-usage"></a>계정 관리 | 계정 모니터링/비정상적 사용

**AC-2 (12).b** 조직은 정보 시스템 계정의 비정상적 사용을 [할당: 조직에서 정의한 담당자 또는 역할]에게 보고합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 보안 및 감사 솔루션의 ID 및 액세스 대시보드를 구현합니다. 이 대시보드를 통해 계정 관리자는 배포된 리소스에 대한 액세스 시도를 모니터링할 수 있습니다. 비정상적 활동이 의심되거나 미리 정의된 기타 이벤트가 발생할 때 경고를 보내도록 이 솔루션을 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800-53 제어 AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>계정 관리 | 위험 수준이 높은 개인용 계정 사용 안 함

**AC-2 (13)** 조직은 위험 검색 [할당: 조직에서 정의한 기간] 내에 심각한 위험을 초래하는 사용자의 계정을 사용하지 않도록 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책 및 절차는 조직에 심각한 위험을 초래하는 사용자의 계정을 사용하지 않도록 조건을 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-3"></a>NIST 800-53 제어 AC-3

#### <a name="access-enforcement"></a>액세스 적용

**AC-3** 정보 시스템은 적용 가능한 액세스 제어 정책에 따라 정보 및 시스템 리소스에 대한 논리적 액세스를 위해 승인된 권한 부여를 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Azure Active Directory(사용자를 역할에 할당), Active Directory(사용자를 보안 그룹에 할당) 및 Windows OS 수준 제어에 의해 적용되는 역할 기반 액세스 제어를 사용하여 논리적 액세스 권한 부여를 적용합니다. 사용자 또는 그룹에 할당된 Azure Active Directory 역할은 리소스, 그룹 또는 구독 수준에서 Azure 내의 리소스에 대한 논리적 액세스를 제어합니다. Active Directory 보안 그룹은 OS 수준의 리소스 및 기능에 대한 논리적 액세스를 제어합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-4"></a>NIST 800-53 제어 AC-4

#### <a name="information-flow-enforcement"></a>정보 흐름 적용

**AC-4** 정보 시스템은 [할당: 조직에서 정의한 정보 흐름 제어 정책]을 기반으로 시스템 내 및 상호 연결된 시스템 간의 정보 흐름을 제어하기 위해 승인된 권한 부여를 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 리소스가 배포되는 서브넷, Application Gateway 및 부하 분산 장치에 적용되는 네트워크 보안 그룹을 사용하여 정보 흐름 제한을 적용합니다. 네트워크 보안 그룹은 승인된 규칙에 따라 리소스 간 정보 흐름이 제어되도록 합니다. Application Gateway 및 부하 분산 장치는 승인된 역할에 따라 특정 리소스로 트래픽을 동적으로 라우팅합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-4-8"></a>NIST 800-53 제어 AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>정보 흐름 적용 | 보안 정책 필터

**AC-4 (8)** 정보 시스템은 [할당: 조직에서 정의한 정보 흐름]에 대한 흐름 제어 결정의 기초로 [할당: 조직에서 정의한 보안 정책 필터]를 사용하여 정보 흐름 제어를 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스 내에서 정보 흐름 제어를 적용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-4-21"></a>NIST 800-53 제어 AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>정보 흐름 적용 | 정보 흐름의 물리적/논리적 분리

**AC-4 (21)** 정보 시스템은 [할당: 정보 유형별로 조직에서 정의한 필수 분리]를 수행하기 위해 [할당: 조직에서 정의한 메커니즘 및/또는 기술]을 사용하여 정보 흐름을 논리적으로 또는 물리적으로 분리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스 내에서 정보 흐름을 분리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-5a"></a>NIST 800-53 제어 AC-5.a

#### <a name="separation-of-duties"></a>의무 분리

**AC 5.a** 조직은 [할당: 조직에서 정의한 개인의 의무]를 분리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 제어하는 계정에서 의무를 분리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-5b"></a>NIST 800-53 제어 AC-5.b

#### <a name="separation-of-duties"></a>의무 분리

**AC-5.b** 조직은 개인의 의무 분리를 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 제어하는 계정에서 의무 분리를 문서화할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-5c"></a>NIST 800-53 제어 AC-5.c

#### <a name="separation-of-duties"></a>의무 분리

**AC-5.c** 조직은 의무 분리를 지원하기 위해 정보 시스템 액세스 권한 부여를 정의합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 조직의 요구 사항에 따라 의무를 분리하도록 구성할 수 있는 역할 기반 액세스 제어를 구현합니다. Azure Active Directory 계정 권한은 역할에 사용자를 할당하여 역할 기반 액세스 제어를 통해 구현됩니다. Active Directory 계정 권한은 사용자를 보안 그룹에 할당하여 역할 기반 액세스 제어를 통해 구현됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-6"></a>NIST 800-53 제어 AC-6

#### <a name="least-privilege"></a>최소 권한

**AC-6** 조직은 최소한의 권한 원칙을 사용하여 사용자(또는 사용자 대신 동작하는 프로세스)에 대해 조직 업무와 비즈니스 기능에 따라 할당된 작업을 수행하는 데 필요한 액세스 권한만 허용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 역할 기반 액세스 제어를 구현하여 명시적으로 할당된 권한으로만 사용자를 제한합니다. Azure Active Directory 계정 권한은 역할에 사용자를 할당하여 역할 기반 액세스 제어를 통해 구현됩니다. Active Directory 계정 권한은 사용자를 보안 그룹에 할당하여 역할 기반 액세스 제어를 통해 구현됩니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800-53 제어 AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>최소 권한 | 보안 기능에 대한 액세스 권한 부여

**AC-6 (1)** 조직은 [할당: 조직에서 정의한 보안 기능(하드웨어, 소프트웨어 및 펌웨어에 배포됨) 및 보안 관련 정보]에 대한 액세스 권한을 명시적으로 부여합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 절차는 보안 기능에 대한 액세스를 포함하는 액세스 권한 부여 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800-53 제어 AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>최소 권한 | 비보안 기능에 대한 권한이 없는 액세스

**AC-6 (2)** 조직은 [할당: 조직에서 정의한 보안 기능 또는 보안 관련 정보]에 대한 액세스 권한이 있는 정보 시스템 계정 또는 역할의 사용자에게 비보안 기능에 액세스하는 경우 권한이 없는 계정 또는 역할을 사용하도록 요구합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책은 사용자에게 비보안 기능에 액세스하는 경우 권한이 없는 계정을 사용하도록 요구할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800-53 제어 AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>최소 권한 | 권한 있는 명령에 대한 네트워크 액세스

**AC-6 (3)** 조직은 [할당: 조직에서 정의한 강력한 운영 요구 사항]의 경우에만 [할당: 조직에서 정의한 권한 있는 명령]에 대한 네트워크 액세스 권한을 부여하고 정보 시스템 보안 계획에서 이러한 액세스에 대한 근거를 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책은 네트워크를 통해 액세스할 수 있는 권한 있는 명령을 정의할 수 있습니다. 참고: 고객은 Azure 인프라에 물리적으로 액세스할 수 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800-53 제어 AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>최소 권한 | 권한 있는 계정

**AC-6 (5)** 조직은 정보 시스템의 권한 있는 계정을 [할당: 조직에서 정의한 담당자 또는 역할]로 제한합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책은 권한 있는 계정 사용에 대한 제한을 정의할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-6-7a"></a>NIST 800-53 제어 AC-6 (7).a

#### <a name="least-privilege--review-of-user-privileges"></a>최소 권한 | 사용자 권한 검토

**AC-6 (7).a** 조직은 [할당: 조직에서 정의한 빈도]마다 [할당: 조직에서 정의한 역할 또는 사용자 클래스]에 할당된 권한을 검토하여 이러한 권한의 필요성에 대한 유효성을 검사합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 제어하는 계정의 사용자 권한을 검토할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-6-7b"></a>NIST 800-53 제어 AC-6 (7).b

#### <a name="least-privilege--review-of-user-privileges"></a>최소 권한 | 사용자 권한 검토

**AC-6 (7).b** 조직은 조직 업무/비즈니스 요구 사항을 올바르게 반영하기 위해 필요한 경우 권한을 재할당하거나 제거합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 적절할 때 고객이 제어하는 계정에 대한 권한을 재할당하거나 제거해야 할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800-53 제어 AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>최소 권한 | 코드 실행에 대한 권한 수준

**AC-6 (8)** 정보 시스템은 [할당: 조직에서 정의한 소프트웨어]가 소프트웨어를 실행하는 사용자보다 높은 권한 수준에서 실행되는 것을 방지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 역할 기반 액세스 제어를 구현하여 명시적으로 할당된 권한으로만 사용자를 제한합니다. 가상 머신 OS 수준 보호는 소프트웨어를 실행하는 사용자보다 높은 권한 수준에서 소프트웨어를 실행할 수 없도록 합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800-53 제어 AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>최소 권한 | 권한 있는 기능 사용 감사

**AC-6 (9)** 정보 시스템은 권한 있는 기능의 실행을 감사합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Log Analytics 서비스를 구현합니다. 배포된 VM 및 Azure 진단 저장소 계정은 권한 있는 기능의 실행이 감사되도록 Log Analytics에 연결된 원본입니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800-53 제어 AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>최소 권한 | 권한이 없는 사용자가 권한 있는 기능을 실행할 수 없도록 방지

**AC-6 (10)** 정보 시스템은 권한이 없는 사용자가 구현된 보안 세이프가드/대응을 사용 안 함, 우회 또는 변경하는 것을 포함하여 권한 있는 기능을 실행하지 못하도록 방지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 역할 기반 액세스 제어를 구현하여 명시적으로 할당된 권한으로만 사용자를 제한합니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-7a"></a>NIST 800-53 제어 AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>실패한 로그온 시도

**AC-7.a** 정보 시스템은 [할당: 조직에서 정의한 기간] 동안 사용자에 의한 [할당: 조직에서 정의한 수]의 연속적인 잘못된 로그온 시도 제한을 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Portal은 사용자에 의한 연속적인 잘못된 로그온 시도를 제한합니다. 그룹 정책은 이 Blueprint에서 배포한 모든 가상 머신에 대해 운영 체제 수준에서 적용됩니다. 이 정책은 사용자에 의한 연속적인 잘못된 로그온 시도를 15분 이내에 3회 이하로 제한합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-7b"></a>NIST 800-53 제어 AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>실패한 로그온 시도

**AC-7.b** 정보 시스템은 실패한 최대 시도 횟수를 초과하면 자동으로 [선택: [할당: 조직에서 정의한 기간] 동안 계정/노드를 잠급니다, 관리자가 해제할 때까지 계정/노드를 잠급니다, [할당: 조직에서 정의한 지연 알고리즘]에 따라 다음 로그온 프롬프트를 지연시킵니다].

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Portal은 사용자에 의한 연속적인 잘못된 로그온 시도 후 계정을 잠급니다. 그룹 정책은 이 Blueprint에서 배포한 모든 가상 머신에 대해 운영 체제 수준에서 적용됩니다. 이 정책은 사용자가 3회 연속해서 잘못된 로그온 시도를 한 후 3시간 동안 계정을 잠급니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-7-2"></a>NIST 800-53 제어 AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>실패한 로그온 시도 | 모바일 장치 제거/초기화

**AC-7 (2)** 정보 시스템은 [할당: 조직에서 정의한 수]만큼 연속적이고 실패한 장치 로그온 시도 후에 [할당: 조직에서 정의한 요구 사항/기술 제거/초기화]에 따라 [할당: 조직에서 정의한 모바일 장치]에서 정보를 제거/초기화합니다.

**책임:** `Not Applicable`

|||
|---|---|
| **고객** | 모바일 장치가 Azure에 배포된 시스템 범위 내에 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Azure 경계 내에서 모바일 장치를 허용하지 않습니다. 따라서 이 제어는 Microsoft Azure에 적용되지 않습니다. |


 ## <a name="nist-800-53-control-ac-8a"></a>NIST 800-53 제어 AC-8.a

#### <a name="system-use-notification"></a>시스템 사용 알림

**AC-8.a** 정보 시스템은 적용 가능한 연방법, 행정 명령, 지시, 정책, 규정, 표준, 참고 자료에 부합하여 개인 정보 보호 및 보안 고지를 제공하는 시스템에 대한 액세스 권한을 부여하기 전에 [할당: 조직에서 정의한 시스템 사용 알림 메시지 또는 배너]를 사용자에게 표시하고 다음 내용을 명시합니다. 사용자가 미국 정보 시스템에 액세스하고 있습니다. 정보 시스템 사용은 모니터링, 기록 및 감사 대상이 될 수 있습니다. 정보 시스템의 무단 사용은 금지되어 있으며 형사 및 민사 처벌 대상입니다. 정보 시스템을 사용하면 모니터링 및 기록에 동의한 것으로 간주됩니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 그룹 정책은 로그인하기 전에 사용자에게 표시되는 시스템 사용 알림을 구현합니다. 참고: 이 Blueprint는 예제 시스템 사용 알림을 구현합니다. 고객은 조직 및/또는 규제 기관 요구 사항을 충족시키도록 이 텍스트를 편집해야 합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-8b"></a>NIST 800-53 제어 AC-8.b

#### <a name="system-use-notification"></a>시스템 사용 알림

**AC-8.b** 정보 시스템은 사용자가 사용 조건을 확인하고 정보 시스템에 로그온하거나 추가로 액세스하기 위해 명시적인 작업을 수행할 때까지 화면에 알림 메시지 또는 배너를 유지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 그룹 정책은 로그온하기 전에 사용자에게 표시되는 시스템 사용 알림을 구현합니다. 사용자는 알림을 승인해야 로그인할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-8c"></a>NIST 800-53 제어 AC-8.c

#### <a name="system-use-notification"></a>시스템 사용 알림

**AC-8.c** 공개적으로 액세스할 수 있는 시스템의 정보 시스템은 추가 액세스 권한을 부여하기 전에 시스템 사용 정보 [할당: 조직에서 정의한 조건]을 표시하고, 해당 활동을 일반적으로 금지하는 시스템에 대해 개인 정보 보호 합의와 일치하는 모니터링, 기록 또는 감사에 대한 참조(있는 경우)를 표시하고, 시스템의 권한 있는 사용에 대한 설명을 포함합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 공개적으로 액세스할 수 있는 모든 고객 배포 리소스에 대한 시스템 사용 알림을 표시할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-10"></a>NIST 800-53 제어 AC-10

#### <a name="concurrent-session-control"></a>동시 세션 제어

**AC-10** 정보 시스템은 각 [할당: 조직에서 정의한 계정 및/또는 계정 유형]에 대한 동시 세션 수를 [할당: 조직에서 정의한 수]로 제한합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포한 가상 머신에 운영 체제 정책이 구현되었습니다. 정책은 동시 세션 제한 사항(두 개의 세션)을 구현합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-11a"></a>NIST 800-53 제어 AC-11.a

#### <a name="session-lock"></a>세션 잠금

**AC-11.a** 정보 시스템은 비활성 [할당: 조직에서 정의한 시간] 또는 사용자의 요청을 수신한 후에 세션 잠금을 시작하여 시스템에 대한 추가 액세스를 방지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 그룹 정책은 RDP 세션에 대해 비활성 잠금을 구현합니다. 사용자는 수동으로 잠금을 시작할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-11b"></a>NIST 800-53 제어 AC-11.b

#### <a name="session-lock"></a>세션 잠금

**AC-11.b** 정보 시스템은 사용자가 설정된 ID 및 인증 절차를 사용하여 액세스를 다시 설정할 때까지 세션 잠금을 유지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 그룹 정책은 RDP 세션에 대해 비활성 잠금을 구현합니다. 세션 잠금을 해제하려면 사용자가 다시 인증해야 합니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-11-1"></a>NIST 800-53 제어 AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>세션 잠금 | 패턴 숨기기 표시

**AC-11 (1)** 정보 시스템은 공개적으로 볼 수 있는 이미지로 디스플레이에 이전에 표시되었던 정보를 세션 잠금을 통해 숨깁니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 그룹 정책은 RDP 세션에 대해 비활성 잠금을 구현합니다. 세션 잠금은 이전에 표시되었던 정보를 숨깁니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-12"></a>NIST 800-53 제어 AC-12

#### <a name="session-termination"></a>세션 종료

**AC-12** 정보 시스템은 [할당: 세션 연결 끊기가 필요한 조직에서 정의한 조건 또는 트리거 이벤트] 이후 사용자 세션을 자동으로 종료합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포한 Windows 가상 머신에 대한 원격 데스크톱 세션 호스트 구성을 조직 세션 종료 요구 사항을 충족하도록 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-12-1a"></a>NIST 800-53 제어 AC-12 (1).a

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>세션 종료 | 사용자가 시작한 로그 아웃/메시지 표시

**AC-12 (1).a** 정보 시스템은 인증을 사용하여 [할당: 조직에서 정의한 정보 리소스]에 액세스 할 때마다 사용자가 시작한 통신 세션에 대한 로그아웃 기능을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Portal 및 이 Blueprint가 배포한 가상 머신 운영 체제를 사용하면 로그아웃을 시작할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-12-1b"></a>NIST 800-53 제어 AC-12 (1).b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>세션 종료 | 사용자가 시작한 로그 아웃/메시지 표시

**AC-12 (1).b** 정보 시스템은 사용자에게 인증된 통신 세션의 안정적인 종료를 나타내는 명시적 로그아웃 메시지를 표시합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Portal 및 이 Blueprint가 배포한 가상 머신 운영 체제를 사용하면 로그아웃을 시작할 수 있습니다. 로그아웃 프로세스는 세션이 종료되었음을 사용자에게 알려줍니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-14a"></a>NIST 800-53 제어 AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>ID 또는 인증 없이 허용되는 작업

**AC-14.a** 조직은 조직 업무/비즈니스 기능과 일치하는 ID 또는 인증 없이 정보 시스템에서 수행할 수 있는 [할당: 조직에서 정의한 사용자 작업]을 식별합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 ID 또는 인증 없이 고객이 배포한 리소스에서 수행할 수 있는 작업(예: 공개적으로 액세스할 수 있는 웹 페이지 보기)을 식별할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-14b"></a>NIST 800-53 제어 AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>ID 또는 인증 없이 허용되는 작업

**AC-14.b** 조직은 정보 시스템에 대한 보안 계획의 지원 근거 및 ID 또는 인증을 요구하지 않는 사용자 작업을 문서화하고 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 배포한 리소스에 대해 ID 또는 인증을 요구하지 않는 사용자 작업에 대한 문서를 제공할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-17a"></a>NIST 800-53 제어 AC-17.a

#### <a name="remote-access"></a>원격 액세스

**AC-17.a** 조직은 허용되는 각 유형의 원격 액세스에 대한 사용 제한, 구성/연결 요구 사항 및 구현 참고 자료를 설정하고 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책은 원격 액세스 사용 제한을 정의할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-17b"></a>NIST 800-53 제어 AC-17.b

#### <a name="remote-access"></a>원격 액세스

**AC-17.b** 조직은 이러한 연결을 허용하기 전에 정보 시스템에 대한 원격 액세스 권한을 부여합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 절차는 원격 액세스 권한 부여 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-17-1"></a>NIST 800-53 제어 AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>원격 액세스 | 자동화된 모니터링/제어

**AC-17 (1)** 정보 시스템은 원격 액세스 방법을 모니터링하고 제어합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Azure Portal, jumpbox를 통한 원격 데스크톱 연결, 고객 구현 웹 응용 프로그램을 통해 정보 시스템에 대한 원격 액세스를 제공합니다. Azure Portal 및 원격 데스크톱 세션을 통한 액세스는 감사되며 Log Analytics를 통해 모니터링할 수 있습니다. 고객은 필요에 따라 웹 응용 프로그램에 원격 액세스 제어를 구현해야 합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-17-2"></a>NIST 800-53 제어 AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>원격 액세스 | 암호화를 사용하여 기밀성/무결성 보호

**AC-17 (2)** 정보 시스템은 원격 액세스 세션의 기밀성과 무결성을 보호하기 위해 암호화 메커니즘을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Portal, 원격 데스크톱 연결 및 웹 응용 프로그램 게이트웨이를 비롯하여 이 Blueprint에서 배포한 리소스에 대한 원격 액세스는 TLS를 사용하여 보호됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-17-3"></a>NIST 800-53 제어 AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>원격 액세스 | 관리되는 액세스 제어 지점

**AC-17 (3)** 정보 시스템은 [할당: 조직에서 정의한 수]의 관리되는 네트워크 액세스 제어 지점을 통해 모든 원격 액세스를 라우팅합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포한 추상적인 웹 응용 프로그램에 대한 원격 액세스는 응용 프로그램 게이트웨이를 통해 이루어집니다. 다른 모든 리소스에 대한 원격 액세스는 jumpbox를 통해 이루어집니다. 공개적으로 액세스할 수 있는 다른 끝점은 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-17-4a"></a>NIST 800-53 제어 AC-17 (4).a

#### <a name="remote-access--privileged-commands--access"></a>원격 액세스 | 권한 있는 명령/액세스

**AC-17 (4).a** 조직은 권한 있는 명령의 실행을 인증하고 [할당: 조직에서 정의한 요구 사항]에 대해서만 원격 액세스를 통해 보안 관련 정보에 대한 액세스 권한을 부여합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책은 원격으로 액세스할 수 있는 권한 있는 명령을 정의하고 근거를 포함시킬 수 있습니다. 참고: 고객은 Azure 인프라에 대한 직접 네트워크 액세스 권한이 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-17-4b"></a>NIST 800-53 제어 AC-17 (4).b

#### <a name="remote-access--privileged-commands--access"></a>원격 액세스 | 권한 있는 명령/액세스

**AC-17 (4).b** 조직은 정보 시스템의 보안 계획에서 이러한 액세스에 대한 근거를 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책은 원격으로 액세스할 수 있는 권한 있는 명령을 정의하고 근거를 포함시킬 수 있습니다. 참고: 고객은 Azure 인프라에 대한 직접 네트워크 액세스 권한이 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-17-9"></a>NIST 800-53 제어 AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>원격 액세스 | 액세스 연결 끊기/사용 안 함

**AC-17 (9)** 조직은 [할당: 조직에서 정의한 기간] 내에 정보 시스템에 대한 원격 액세스를 신속하게 연결 끊기 또는 사용하지 않도록 설정할 수 있는 기능을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Azure Portal, jumpbox를 통한 원격 데스크톱 연결, 웹 응용 프로그램을 통해 정보 시스템에 대한 원격 액세스를 제공합니다. Azure Active Directory 계정이 사용하지 않도록 설정되거나 제거되면 Azure Portal 액세스의 연결이 즉시 끊어집니다. 마찬가지로 가상 머신 OS 수준 계정이 사용하지 않도록 설정되거나 제거되면 jumpbox를 통한 원격 데스크톱 액세스의 연결이 즉시 끊어집니다. 고객은 웹 응용 프로그램에 대한 원격 액세스 연결 끊기를 구현해야 합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-18a"></a>NIST 800-53 제어 AC-18.a

#### <a name="wireless-access"></a>무선 액세스

**AC-18.a** 조직은 무선 액세스에 대한 사용 제한, 구성/연결 요구 사항 및 구현 참고 자료를 설정합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에 무선 액세스가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Microsoft Azure 환경에서 무선 사용을 명시적으로 금지하는 네트워크 보안 표준을 통해 무선 액세스에 대한 사용 제한, 구성/연결 요구 사항 및 구현 참고 자료를 설정합니다. |


 ## <a name="nist-800-53-control-ac-18b"></a>NIST 800-53 제어 AC-18.b

#### <a name="wireless-access"></a>무선 액세스

**AC-18.b** 조직은 이러한 연결을 허용하기 전에 정보 시스템에 대한 무선 액세스 권한을 부여합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에 무선 액세스가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Microsoft Azure 데이터 센터 내에서 무선 액세스를 허용하지 않습니다. |


 ### <a name="nist-800-53-control-ac-18-1"></a>NIST 800-53 제어 AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>무선 액세스 | 인증 및 암호화

**AC-18 (1)** 정보 시스템은 [선택(하나 이상): 사용자, 장치] 인증 및 암호화를 사용하여 시스템에 대한 무선 액세스를 보호합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에 무선 액세스가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Microsoft Azure 환경 내에서 무선 액세스를 허용하지 않습니다. |


 ### <a name="nist-800-53-control-ac-18-3"></a>NIST 800-53 제어 AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>무선 액세스 | 무선 네트워킹 사용 안 함

**AC-18 (3)** 조직은 정보 시스템 구성 요소에 내장된 무선 네트워킹 기능을 사용하지 않을 경우 발급 및 배포 전에 사용하지 않도록 설정합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에 무선 액세스가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Microsoft Azure 환경 내에서 무선 액세스를 허용하지 않습니다. |


 ### <a name="nist-800-53-control-ac-18-4"></a>NIST 800-53 제어 AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>무선 액세스 | 사용자가 구성 제한

**AC-18 (4)** 조직은 무선 네트워킹 기능을 독립적으로 구성할 수 있는 사용자를 식별하고 명시적으로 인증합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에 무선 액세스가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Microsoft Azure 환경 내에서 무선 액세스를 허용하지 않습니다. |


 ### <a name="nist-800-53-control-ac-18-5"></a>NIST 800-53 제어 AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>무선 액세스 | 안테나/송전 수준

**AC-18 (5)** 조직은 무선 안테나를 선택하고 송전 수준을 보정하여 조직에서 제어하는 ​​경계 밖에서 사용 가능한 신호를 수신할 가능성을 줄입니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에 무선 액세스가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Microsoft Azure 환경 내에서 무선 액세스를 허용하지 않습니다. |


 ## <a name="nist-800-53-control-ac-19a"></a>NIST 800-53 제어 AC-19.a

#### <a name="access-control-for-mobile-devices"></a>모바일 장치에 대한 액세스 제어

**AC-19.a** 조직은 조직에서 제어하는 모바일 장치에 대한 사용 제한, 구성 요구 사항, 연결 요구 사항 및 구현 참고 자료를 설정합니다.

**책임:** `Not Applicable`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에는 고객이 제어하는 모바일 장치가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Azure 경계 내에서 모바일 장치를 허용하지 않습니다. 따라서 이 제어는 Microsoft Azure에 적용되지 않습니다. |


 ## <a name="nist-800-53-control-ac-19b"></a>NIST 800-53 제어 AC-19.b

#### <a name="access-control-for-mobile-devices"></a>모바일 장치에 대한 액세스 제어

**AC-19.b** 조직은 모바일 장치를 조직 정보 시스템에 연결하는 권한을 부여합니다.

**책임:** `Not Applicable`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에는 고객이 제어하는 모바일 장치가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Azure 경계 내에서 모바일 장치를 허용하지 않습니다. 따라서 이 제어는 Microsoft Azure에 적용되지 않습니다. |


 ### <a name="nist-800-53-control-ac-19-5"></a>NIST 800-53 제어 AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>모바일 장치에 대한 액세스 제어 | 전체 장치/컨테이너 기반 암호화

**AC-19 (5)** 조직은 [선택: 전체 장치 암호화, 컨테이너 암호화]를 사용하여 [할당: 조직에서 정의한 모바일 장치]에 대한 정보의 기밀성과 무결성을 보호합니다.

**책임:** `Not Applicable`

|||
|---|---|
| **고객** | Azure에 배포된 시스템 범위에는 고객이 제어하는 모바일 장치가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Azure 경계 내에서 모바일 장치를 허용하지 않습니다. 따라서 이 제어는 Microsoft Azure에 적용되지 않습니다. |


 ## <a name="nist-800-53-control-ac-20a"></a>NIST 800-53 제어 AC-20.a

#### <a name="use-of-external-information-systems"></a>외부 정보 시스템의 사용

**AC-20.a** 조직은 외부 정보 시스템을 소유, 운영 및/또는 유지 관리하는 다른 조직과 설정된 트러스트 관계에 부합하는 계약조건을 설정하여 권한 있는 개인이 외부 정보 시스템에서 정보 시스템에 액세스할 수 있도록 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책에는 FedRAMP 하에서 클라우드 서비스 제공 사용에 관한 규정이 포함될 수 있습니다. Azure는 정부 기관에 의해 Azure Cloud Services의 취득 사용을 가능하게 하는 FedRAMP JAB(Joint Authorization Board)에 의해 P-ATO(provisional authorization to operate)를 부여받았습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-20b"></a>NIST 800-53 제어 AC-20.b

#### <a name="use-of-external-information-systems"></a>외부 정보 시스템의 사용

**AC-20.b** 조직은 외부 정보 시스템을 소유, 운영 및/또는 유지 관리하는 다른 조직과 설정된 트러스트 관계에 부합하는 계약조건을 설정하여 인증된 개인이 외부 정보 시스템을 통해 조직에서 제어하는 정보를 처리, 저장 또는 전송할 수 있도록 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책에는 FedRAMP 하에서 클라우드 서비스 제공 사용에 관한 규정이 포함될 수 있습니다. Azure는 정부 기관에 의해 Azure Cloud Services의 취득 사용을 가능하게 하는 FedRAMP JAB(Joint Authorization Board)에 의해 P-ATO(provisional authorization to operate)를 부여받았습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-20-1"></a>NIST 800-53 제어 AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>외부 정보 시스템의 사용 | 권한 있는 사용에 대한 제한

**AC-20 (1)** 조직은 조직의 정보 보안 정책 및 보안 계획에 지정된 대로 외부 시스템에 대해 필요한 보안 제어의 구현을 조직이 확인하는 경우에 한해 권한 있는 개인이 외부 정보 시스템을 사용하여 정보 시스템에 액세스 또는 조직에서 제어하는 정보를 처리, 저장 또는 전송하도록 허용하거나, 승인된 정보 시스템 연결 또는 외부 정보 시스템을 호스팅하는 조직 엔터티와의 처리 계약을 유지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 정보 기술 그룹은 클라우드 서비스 공급자가 조직 정보 보안 요구 사항을 준수하는지 확인한 다음 관련 클라우드 서비스 제공을 사용할 수 있도록 엔터프라이즈 수준 승인을 부여할 수 있습니다. Azure는 FedRAMP JAB(Joint Authorization Board)에 의해 P-ATO(provisional authorization to operate)를 부여받았습니다. Azure는 FedRAMP 보안 제어 및 기타 요구 사항을 준수하는지 확인하기 위해 FedRAMP에서 승인한 3PAO(타사 평가 조직)에 의해 평가됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ac-20-2"></a>NIST 800-53 제어 AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>외부 정보 시스템의 사용 | 휴대용 저장 장치

**AC-20 (2)** 조직은 외부 정보 시스템에서 권한 있는 개인이 조직에서 제어하는 휴대용 저장 장치를 사용하는 것을 [선택: 제한; 금지]합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | 고객은 Azure 데이터 센터의 시스템 리소스에 물리적으로 액세스할 권한이 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft는 Microsoft Azure 환경에서 고객이 제어하는 ​​휴대용 저장 장치를 허용하지 않습니다. |


 ## <a name="nist-800-53-control-ac-21a"></a>NIST 800-53 제어 AC-21.a

#### <a name="information-sharing"></a>정보 공유

**AC-21.a** 조직은 권한 있는 사용자가 공유 파트너에게 할당된 액세스 권한이 [할당: 사용자 판단이 필요한 조직에서 정의한 정보 공유 환경]의 정보에 대한 액세스 제한과 일치하는지 여부를 확인할 수 있도록 하여 정보 공유를 용이하게 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 정책에는 정보 공유에 관한 규정이 포함될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-21b"></a>NIST 800-53 제어 AC-21.b

#### <a name="information-sharing"></a>정보 공유

**AC-21.b** 조직은 사용자가 정보 공유/협업 의사 결정을 내릴 수 있도록 [할당: 조직에서 정의한 자동화된 메커니즘 또는 수동 프로세스]를 사용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 엔터프라이즈 수준 정보 공유 의사 결정 지원 기능을 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-22a"></a>NIST 800-53 제어 AC-22.a

#### <a name="publicly-accessible-content"></a>공개적으로 액세스할 수 있는 콘텐츠

**AC-22.a** 조직은 공개적으로 액세스할 수 있는 정보 시스템에 정보를 게시할 권한이 있는 개인을 지정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 절차는 공개적으로 액세스할 수 있는 정보를 게시할 권한이 있는 개인을 지정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-22b"></a>NIST 800-53 제어 AC-22.b

#### <a name="publicly-accessible-content"></a>공개적으로 액세스할 수 있는 콘텐츠

**AC-22.b** 조직은 권한 있는 개인이 공개적으로 액세스할 수 있는 정보에 비공개 정보가 포함되지 않도록 교육시킵니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 공개적으로 액세스할 수 있는 정보를 게시할 권한이 있는 개인에 대해 엔터프라이즈 수준 교육을 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-22c"></a>NIST 800-53 제어 AC-22.c

#### <a name="publicly-accessible-content"></a>공개적으로 액세스할 수 있는 콘텐츠

**AC-22.c** 조직은 비공개 정보가 포함되지 않도록 공개적으로 액세스할 수 있는 정보 시스템에 게시하기 전에 제안된 정보 콘텐츠를 검토합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 절차는 공개적으로 액세스 가능한 시스템에 게시되도록 제안된 콘텐츠에 대해 검토 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ac-22d"></a>NIST 800-53 제어 AC-22.d

#### <a name="publicly-accessible-content"></a>공개적으로 액세스할 수 있는 콘텐츠

**AC-22.d** 조직은 [할당: 조직에서 정의한 빈도]마다 비공개 정보에 대해 공개적으로 액세스할 수 있는 정보 시스템의 콘텐츠를 검토하고 그러한 정보가 발견되면 제거합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 액세스 제어 절차는 공개적으로 액세스할 수 있는 시스템에 게시된 콘텐츠를 주기적으로 검토하는 프로세스를 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |

