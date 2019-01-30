---
title: Azure AD Connect의 성능에 영향을 주는 요인
description: 이 문서에서는 다양한 요인이 Azure AD Connect 프로비저닝 엔진에 어떻게 영향을 미치는지에 대해 설명합니다. 이러한 요소는 동기화 요구 사항을 충족하도록 조직에서 Azure AD Connect 배포를 계획하는 데 도움이 됩니다.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.openlocfilehash: 801082164a0110178034c5fbe050ebe8e02b2772
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474135"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Azure AD Connect의 성능에 영향을 주는 요인

Azure AD Connect는 Active Directory를 Azure AD에 동기화합니다. 이 서버는 사용자 ID를 클라우드로 이동하는 데 중요한 구성 요소입니다. Azure AD Connect의 성능에 영향을 주는 주요 요소는 다음과 같습니다.

| **디자인 요소**| **정의** |
|:-|-|
| 토폴로지| Azure AD Connect의 엔드포인트 및 구성 요소 배포는 네트워크에서 관리해야 합니다. |
| 확장| Azure AD Connect에서 관리되는 사용자, 그룹 및 OU와 같은 개체의 수입니다. |
| 하드웨어| Azure AD Connect에 대한 하드웨어(물리적 또는 가상) 및 CPU, 메모리, 네트워크 및 하드 드라이브 구성을 비롯한 각 하드웨어 구성 요소에 대한 종속 성능 용량입니다. |
| 구성| Azure AD Connect는 디렉터리 및 정보를 처리합니다. |
| 로드| 개체 변경의 빈도입니다. 로드는 시간, 일 또는 주 동안에 달라질 수 있습니다. 구성 요소에 따라 최대 부하 또는 평균 부하에 맞게 디자인해야 합니다. |

이 문서의 목적은 Azure AD Connect 프로비저닝 엔진의 성능에 영향을 미치는 요소를 설명하는 것입니다. 대규모 또는 복잡한 조직(100,000개 초과의 개체를 프로비전하는 조직)은 여기에 설명된 성능 문제가 발생하는 경우 Azure AD Connect 구현을 최적화하기 위한 권장 사항을 사용할 수 있습니다. [Azure AD Connect 상태](how-to-connect-health-agent-install.md)와 같은 Azure AD Connect의 다른 구성 요소 및 에이전트는 여기에서 다루지 않습니다.

> [!IMPORTANT]
> Microsoft는 공식적으로 문서화된 작업 외의 Azure AD Connect 동기화에 대한 수정 또는 작업을 지원하지 않습니다. 이러한 동작 중 하나는 Azure AD Connect 동기화의 불일치하거나 지원되지 않는 상태가 될 수 있습니다. 결과적으로, Microsoft는 해당 배포에 대해 기술 지원을 제공할 수 없습니다.

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect 구성 요소

다음 다이어그램은 다중 포리스트는 지원되긴 하지만 단일 포리스트에 연결된 엔진 프로비전의 개략적인 아키텍처를 보여줍니다. 이 아키텍처는 다양한 구성 요소가 서로 어떻게 상호 작용하는지를 보여줍니다.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

프로비전 엔진은 각 Active Directory 포리스트와 Azure AD에 연결되어 있습니다. 각 디렉터리에서 정보를 읽는 프로세스를 가져오기라고 합니다. 내보내기는 프로비전 엔진에서 디렉터리를 업데이트하는 것을 가리킵니다. 동기화는 프로비전 엔진 내에서 개체가 흐르는 방식의 규칙을 평가합니다. 자세한 내용은 [Azure AD Connect 동기화: 아키텍처 이해](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)를 참조하세요.

Azure AD Connect는 Active Directory를 Azure AD에 동기화할 수 있도록 다음 준비 영역, 규칙 및 프로세스를 사용합니다.

* **CS(커넥터 공간)** - 실제 디렉터리인 각 CD(연결된 디렉터리)의 개체는 먼저 여기에서 준비되어야 프로비전 엔진에서 처리할 수 있습니다. Azure AD에 자체 CS가 있고, 연결한 각 포리스트에 자체 CS가 있습니다.
* **MV(메타버스)** - 동기화해야 하는 개체가 동기화 규칙에 따라 여기에서 생성됩니다. 개체가 MV에 존재해야만 다른 연결된 디렉터리에 개체 및 특성을 채울 수 있습니다. MV는 하나만 있습니다.
* **동기화 규칙** - MV에서 개체에 연결(조인)하거나 생성(프로젝트)할 개체를 결정합니다. 또한 동기화 규칙은 다른 곳에서 디렉터리로 또는 그 반대로 복사하거나 변환할 특성 값을 결정합니다.
* **실행 프로필** - 준비 영역과 연결된 디렉터리 간의 동기화 규칙에 따라 개체 및 해당 특성 값을 복사하는 프로세스 단계를 묶습니다.

프로비전 엔진의 성능을 최적화하기 위한 다양한 실행 프로필이 있습니다. 대부분의 조직에서는 일반 작업에 대해 기본 일정 및 실행 프로필을 사용하지만, 일부 조직에서는 일반적이지 않은 상황에 대처하기 위해 [일정을 변경](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler)하거나 다른 실행 프로필을 트리거해야 할 수도 있습니다. 사용할 수 있는 실행 프로필은 다음과 같습니다.

### <a name="initial-sync-profile"></a>초기 동기화 프로필

초기 동기화 프로필은 Active Directory 포리스트와 같이 연결된 디렉터리를 처음으로 읽는 프로세스입니다. 그런 다음, 동기화 엔진 데이터베이스의 모든 항목에서 분석을 수행합니다. 초기 주기에서 Azure AD에 새 개체를 만들며, Active Directory 포리스트가 큰 경우 완료하는 데 시간이 오래 걸립니다. 초기 동기화에는 다음 단계가 포함됩니다.

1. 모든 커넥터에서 전체 가져오기
2. 모든 커넥터에서 전체 동기화
3. 모든 커넥터에서 내보내기

### <a name="delta-sync-profile"></a>델타 동기화 프로필

동기화 프로세스를 최적화하기 위해 이 실행 프로필은 마지막 동기화 프로세스 이후의 연결된 디렉터리 내 개체의 변경 내용(만들기, 삭제 및 업데이트)만 처리합니다. 기본적으로 델타 동기화 프로필은 30분마다 실행됩니다. 조직은 Azure AD를 최신 상태로 유지하기 위해 시간을 30분 미만으로 설정해야 합니다. Azure AD Connect의 상태를 모니터링하려면 [상태 모니터링 에이전트](how-to-connect-health-sync.md)를 사용하여 프로세스를 사용한 모든 문제를 확인합니다. 델타 동기화 주기에는 다음 단계가 포함됩니다.

1. 모든 커넥터에서 델타 가져오기
2. 모든 커넥터에서 델타 동기화
3. 모든 커넥터에서 내보내기

일반적인 엔터프라이즈 조직 델타 동기화 시나리오는 다음과 같습니다.

- 개체의 ~1%가 삭제됨
- 개체의 ~1%가 생성됨
- 개체의 ~5%가 수정됨

변경률은 조직에서 Active Directory의 사용자를 업데이트하는 빈도에 따라 달라질 수 있습니다. 예를 들어, 고용과 인력 감축의 계절적 변동에 따라 더 높은 변경률이 일어날 수 있습니다.

### <a name="full-sync-profile"></a>전체 동기화 프로필

전체 동기화 주기는 다음 구성 변경 사항 중 하나를 수행한 경우 필요합니다.



- 연결된 디렉터리에서 가져올 개체나 특성의 범위가 증가했습니다. 예를 들어 도메인 또는 OU를 가져오기 범위에 추가하는 경우입니다.
- 동기화 규칙을 변경했습니다. 예를 들어, Active Directory의 extension_attribute3에서 Azure AD의 사용자 직함을 채우도록 새 규칙을 만드는 경우입니다. 이 업데이트를 수행하려면 프로비전 엔진이 모든 기존 사용자를 다시 검사하여 변경 내용을 계속 적용되도록 해당 사용자의 직함을 업데이트해야 합니다.

전체 동기화 주기에는 다음 작업이 포함됩니다.

1. 모든 커넥터에서 전체 가져오기
2. 모든 커넥터에서 전체/델타 동기화
3. 모든 커넥터에서 내보내기

> [!NOTE]
> Active Directory 또는 Azure AD에서 여러 개체에 대한 대량 업데이트를 수행하는 경우에는 신중한 계획이 필요합니다. 대량 업데이트를 사용하면 많은 양의 개체가 변경되므로 가져올 때 델타 동기화 프로세스에 시간이 더 오래 걸릴 수 있습니다. 대량 업데이트가 동기화 프로세스에 영향을 주지 않더라도 가져오기에 시간이 오래 걸릴 수 있습니다. 예를 들어 Azure AD에서 많은 사용자에게 라이선스를 할당하면 Azure AD에서 가져오기 주기가 길어지지만, Active Directory에서 특성은 변경되지 않습니다.

### <a name="synchronization"></a>동기화

동기화 프로세스 런타임에는 다음과 같은 성능 특징이 있습니다.

* 동기화는 단일 스레드로, 프로비전 엔진은 연결된 디렉터리, 개체 또는 특성의 실행 프로필에 대한 병렬 처리를 수행하지 않습니다.
* 가져오기 시간은 동기화되는 개체 수에 대해 선형으로 증가합니다. 예를 들어, 10,000개 개체를 가져오는 데 10분이 걸리면, 동일한 서버에서 20,000개 개체에는 약 20분이 소요됩니다.
* 내보내기도 선형입니다.
* 동기화는 다른 개체에 대한 참조를 사용한 개체의 수에 따라 기하급수적으로 증가합니다. 그룹 멤버 자격 및 중첩된 그룹에는 주요 성능 영향이 있습니다. 해당 멤버는 사용자 개체 또는 다른 그룹을 참조하기 때문입니다. 동기화 주기를 완료하려면 MV에서 이러한 참조를 발견하고 실제 개체에 참조해야 합니다.

### <a name="filtering"></a>필터링

가져오려는 Active Directory 토폴로지의 크기는 프로비전 엔진의 내부 구성 요소가 사용하는 전체 시간 및 성능에 영향을 미치는 최고 요소입니다.

[필터링](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering)은 동기화할 개체를 줄이는 데 사용해야 합니다. 불필요한 개체가 처리되어 Azure AD로 내보내지 않도록 방지합니다. 우선 순위로 필터링의 다음 기술을 사용할 수 있습니다.



- **도메인 기반 필터링** – Azure AD에 동기화할 특정 도메인을 선택하려면 이 옵션을 사용합니다. 또한 Azure AD Connect 동기화를 설치한 후 온-프레미스 인프라를 변경하는 경우 동기화 엔진 구성에서 도메인을 추가하고 제거해야 합니다.
- **OU(조직 단위) 필터링** - OU를 사용하여 Active Directory 도메인의 특정 개체를 Azure AD로 프로비전할 대상으로 지정합니다. OU 필터링은 간단한 LDAP 범위 쿼리를 사용하여 Active Directory에서 더 작은 개체 하위 집합을 가져오므로 두 번째로 권장되는 필터링 메커니즘입니다.
- **개체별 특성 필터링** - 개체에 대한 특성 값을 사용하여 Active Directory의 특정 개체가 Azure AD에서 프로비전되었는지 여부를 결정합니다. 특성 필터링은 도메인 및 OU 필터링이 특정 필터링 요구 사항을 충족하지 않는 경우 필터를 미세 조정하기에 적합합니다. 특성 필터링은 가져오기 시간이 단축되지는 않지만 동기화 및 내보내기 횟수는 줄일 수 있습니다.
- **그룹 기반 필터링** - 그룹 멤버 자격을 사용하여 개체가 Azure AD에서 프로비전되어야 하는지 여부를 결정합니다. 그룹 기반 필터링은 테스트 상황에서만 적합하며, 동기화 주기 동안 그룹 멤버 자격을 확인하는 데 필요한 오버헤드때문에 프로덕션에는 권장되지 않습니다.

Active Directory CS의 여러 영구적인 [디스커넥터 개체](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects)는 프로비전 엔진이 동기화 주기에서 가능한 연결에 대해 각 디스커넥터 개체를 다시 평가해야 하므로 동기화 시간이 더 길어질 수 있습니다. 이 문제를 해결하려면 다음 권장 사항 중 하나를 고려하세요.



- 디스커넥터 개체를 도메인 또는 OU 필터링을 사용한 가져오기 범위 내에 배치하지 마세요.
- 개체를 MV에 프로젝트/조인하고 [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) 특성을 True와 동일하게 설정하여 Azure AD CS에서 이러한 개체의 프로비전을 방지합니다.

> [!NOTE]
> 너무 많은 개체가 필터링되는 경우 사용자가 혼동하거나 애플리케이션 사용 권한 문제가 발생할 수 있습니다. 예를 들어 하이브리드 Exchange Online 구현에서 온-프레미스 사서함이 있는 사용자는 Exchange Online에 사서함이 있는 사용자보다 더 많은 사용자가 전체 주소 목록에 표시됩니다. 다른 경우에 사용자는 개체의 필터링된 집합의 범위가 아닌 다른 사용자에게 클라우드 앱에 대한 액세스 권한을 부여할 수도 있습니다.

### <a name="attribute-flows"></a>특성 흐름

특성 흐름은 개체의 특성 값을 연결된 디렉터리에서 다른 연결된 디렉터리로 복사 또는 변환하기 위한 프로세스입니다. 동기화 규칙의 일부로 정의됩니다. 예를 들어, Active Directory에서 사용자의 전화 번호가 변경되면 Azure AD의 전화 번호도 업데이트됩니다. 조직은 다양한 요구 사항에 적합하도록 [특성 흐름을 수정](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration)할 수 있습니다. 변경하기 전에 기존 특성 흐름을 복사하는 것이 좋습니다.

특성 값을 다른 특성으로 전달하는 것 같이 간단한 리디렉션은 자재 성능에 영향을 미치지 않습니다. 리디렉션의 예로는 Active Directory의 휴대폰 번호를 Azure AD의 사무실 전화 번호로 전달하는 것이 있습니다.

특성 값 변환은 동기화 프로세스에서 성능에 영향을 미칠 수 있습니다. 특성 값 변환에는 특성 값의 수정, 다시 포맷, 연결 또는 빼기가 포함됩니다.

조직은 특정 특성이 Azure AD로 전달되지 않도록 방지할 수 있지만, 프로비전 엔진의 성능에는 영향을 미치지 않습니다.

> [!NOTE]
> 동기화 규칙에서 원치 않는 특성 흐름을 삭제하지 마십시오. Azure AD Connect를 업그레이드하는 동안 삭제된 규칙은 다시 만들어지기 때문에 중지시키는 것이 좋습니다.

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect 종속성 요소

Azure AD Connect의 성능은 가져오고 내보내는 연결된 디렉터리의 성능에 따라 달라 집니다. 예를 들어 가져와야 하는 Active Directory의 크기 또는 Azure AD 서비스에 대한 네트워크 대기 시간이 있습니다. 프로비전 엔진이 사용하는 SQL Database도 동기화 주기의 전반적인 성능에 영향을 줍니다.

### <a name="active-directory-factors"></a>Active Directory 요소

이전에 설명한 대로 가져올 개체 수는 성능에 상당한 영향을 줍니다. [Azure AD Connect에 대한 하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md)에서는 배포의 크기를 기준으로 특정 하드웨어 계층을 간략하게 설명합니다. Azure AD Connect는 [Azure AD Connect에 대한 토폴로지](plan-connect-topologies.md)에 간략히 설명된 대로 특정 토폴로지만 지원합니다. 성능 최적화 및 지원되지 않는 토폴로지에 대한 권장 사항은 없습니다.

Azure AD Connect 서버가 가져오려는 Active Directory 크기에 따라 하드웨어 요구를 충족하는지 확인합니다. Azure AD Connect 서버와 Active Directory 도메인 컨트롤러 간의 잘못되거나 느린 네트워크 연결로 인해 가져오기가 느려질 수 있습니다.

### <a name="azure-ad-factors"></a>Azure AD 요소

Azure AD는 DoS(서비스 거부) 공격으로부터 클라우드 서비스를 보호하기 위해 제한을 사용합니다. 현재 Azure AD는 5분마다(시간당 84,000) 7,000회 쓰기 제한을 사용합니다. 예를 들어, 다음 작업은 제한될 수 있습니다.



- Azure AD에 내보내는 Azure AD Connect.
- 동적 그룹 멤버 자격과 같이 백그라운드에서도 Azure AD에 직접 업데이트하는 PowerShell 스크립트 또는 애플리케이션.
- MFA 또는 SSPR(셀프 서비스 암호 재설정)에 대한 등록과 같이 고유한 ID 레코드를 업데이트하는 사용자.
- 그래픽 사용자 인터페이스 내 작업.

사용자 Azure AD Connect 동기화 주기가 제한 한도의 영향을 받지 않도록 배포 및 유지 관리 작업을 계획합니다. 예를 들어, 수천 개의 사용자 ID를 만드는 대규모 채용이 있는 경우 동적 그룹 멤버 자격, 라이선스 할당 및 셀프 서비스 암호 재설정 등록 업데이트가 발생할 수 있습니다. 이러한 쓰기를 몇 시간 또는 며칠에 걸쳐 분산하는 것이 좋습니다.

### <a name="sql-database-factors"></a>SQL Database 요소

원본 Active Directory 토폴로지의 크기는 SQL Database 성능에 영향을 미칩니다. SQL 서버에 대한 [하드웨어 요구 사항](how-to-connect-install-prerequisites.md)을 수행하고 다음 권장 사항을 고려합니다.



- 사용자가 100,000명 초과인 조직은 SQL Database와 프로비전 엔진을 동일한 서버에 배치함으로써 네트워크 대기 시간을 줄일 수 있습니다.
- 동기화 프로세스의 높은 디스크 입력 및 출력(I/O) 요구 사항으로 인해 최적의 결과를 위해서 프로비전 엔진의 SQL Database에 SSD(반도체 드라이브)를 사용합니다. 불가능한 경우 RAID 0 또는 RAID 1 구성을 고려합니다.
- 미리 전체 동기화를 수행하지 마십시오. 불필요 한 변동 및 느린 응답 시간으로 이어질 수 있습니다.

## <a name="conclusion"></a>결론

Azure AD Connect 구현의 성능을 최적화하려면 다음 권장 사항을 고려합니다.



- Azure AD Connect 서버에 대한 구현 크기에 따라 [권장되는 하드웨어 구성](how-to-connect-install-prerequisites.md)을 사용합니다.
- 대규모 배포에서 Azure AD Connect를 업그레이드하는 경우 최소한의 가동 중지 시간 및 최고의 안정성을 보장하기 위해 [스윙 마이그레이션 방법](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration)을 사용하는 것을 고려합니다. 
- 최상의 쓰기 성능을 위해 SQL Database에 SSD를 사용합니다.
- 도메인, OU 또는 특성 필터링을 사용하여 Azure AD에서 프로비전해야 하는 개체만 포함하도록 Active Directory 범위를 필터링합니다.
- 기본 특성 흐름 규칙을 변경해야 하는 경우 먼저 규칙을 복사한 다음, 복사본을 변경하고 원래 규칙을 사용하지 않도록 설정합니다. 전체 동기화를 다시 실행해야 합니다.
- 초기 전체 동기화 실행 프로필에 적절한 시간을 계획합니다.
- 30분 이내에 델타 동기화 주기를 완료하도록 노력합니다. 델타 동기화 프로필을 30분 이내에 완료하지 못하면 전체 델타 동기화 주기를 포함하도록 기본 동기화 빈도를 수정합니다.
- Azure AD에서 [Azure AD Connect 동기화 상태](how-to-connect-health-agent-install.md)를 모니터링합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
