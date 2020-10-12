---
title: Azure Security 벤치 마크 V2의 개요
description: Azure Security 벤치 마크 V2 개요
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6628d693a8df3614097e23785ac234a451565ca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777108"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Azure 보안 벤치 마크 (V2) 개요

ASB (Azure 보안 벤치 마크)는 Azure에서 작업, 데이터 및 서비스의 보안을 개선 하는 데 도움이 되는 규범적인 모범 사례 및 권장 사항을 제공 합니다.

이 벤치 마크는 다음을 포함 하는 전체적인 보안 지침 집합의 일부입니다.

- **클라우드 채택 프레임 워크** – [전략](/azure/cloud-adoption-framework/strategy/define-security-strategy), [역할 및 책임](/azure/cloud-adoption-framework/organize/cloud-security), [Azure 상위 10 가지 보안 모범 사례](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)및 [참조 구현을](/azure/cloud-adoption-framework/ready/enterprise-scale/)포함 하 여 보안에 대 한 지침입니다.
- **Azure Well-Architected Framework** – azure에서 [워크 로드를 보호 하](https://docs.microsoft.com/assessments/?mode=pre-assessment&session=local) 는 방법에 대 한 지침입니다.
- **Microsoft 보안 모범 사례** – Azure에 대 한 예제에 대 한 [권장 사항](/security/compass/microsoft-security-compass-introduction) 입니다.

 Azure 보안 벤치 마크는 클라우드 중심 제어 영역을 집중적으로 다룹니다. 이러한 컨트롤은 CI (Internet Security)의 중앙 컨트롤 버전 7.1 및 NIST (표준 및 기술) SP800-53에 설명 된 것과 같은 잘 알려진 보안 벤치 마크와 일치 합니다.
Azure 보안 벤치 마크에는 다음 컨트롤이 포함 되어 있습니다.

| ASB 제어 도메인 | 설명 
|--|--|
| [네트워크 &nbsp; 보안 &nbsp; (NS)](security-controls-v2-network-security.md) | 네트워크 보안은 가상 네트워크 보안 유지, 개인 연결 설정, 외부 공격 방지 및 방지, DNS 보안 등 Azure 네트워크를 보호 하 고 보호 하는 컨트롤을 포함 합니다. |
| [Id &nbsp; 관리 &nbsp; (IM)](security-controls-v2-identity-management.md) | Id 관리에서는 Single Sign-On, 강력한 인증, 관리 되는 id (및 서비스 원칙)를 사용 하 여 응용 프로그램에 대 한 조건부 액세스 및 계정 변칙 모니터링을 비롯 하 여 Azure Active Directory를 사용 하는 보안 id 및 액세스 제어를 설정 하는 컨트롤을 다룹니다. |
| [&nbsp;PA (권한 있는 액세스 &nbsp; )](security-controls-v2-privileged-access.md) | 권한 있는 액세스는 의도적인 위험에 대해 관리 모델, 관리 계정 및 권한 있는 액세스 워크스테이션을 보호 하는 다양 한 컨트롤을 포함 하 여 Azure 테 넌 트 및 리소스에 대 한 권한 있는 액세스를 보호 하는 컨트롤을 포함 합니다. |
| [데이터 &nbsp; 보호 &nbsp; (DP)](security-controls-v2-data-protection.md) | 데이터 보호는 Azure에서 액세스 제어, 암호화 및 로깅을 사용 하 여 중요 한 데이터 자산을 검색, 분류, 보호 및 모니터링 하는 것을 포함 하 여 미사용 데이터 보호, 전송 중, 권한 있는 액세스 메커니즘을 통한 제어를 포함 합니다. |
| [자산 &nbsp; 관리 &nbsp; (AM)](security-controls-v2-asset-management.md) | 자산 관리는 보안 담당자, 자산 인벤토리에 대 한 보안 액세스, 서비스 및 리소스에 대 한 승인 관리 (인벤토리, 추적, 수정)에 대 한 권장 사항을 포함 하 여 Azure 리소스에 대 한 보안 표시 및 관리를 보장 하는 컨트롤을 포함 합니다. |
| [로깅 &nbsp; 및 &nbsp; 위협 &nbsp; 감지 (LT)](security-controls-v2-logging-threat-detection.md) | 로깅 및 위협 검색은 azure에서 위협을 감지 하 고, azure 서비스에서 기본 위협 검색을 사용 하 여 높은 품질의 경고를 생성 하는 컨트롤을 통해 검색, 조사 및 재구성 프로세스를 사용 하도록 설정 하 여 azure 서비스에 대 한 감사 로그를 활성화, 수집 및 저장 하는 컨트롤을 포함 합니다. 또한 Azure Monitor를 사용 하 여 로그를 수집 하 고, Azure 센티널, 시간 동기화 및 로그 보존으로 보안 분석을 중앙화 합니다. |
| [인시던트 &nbsp; 응답 &nbsp; (IR)](security-controls-v2-incident-response.md) | 인시던트 응답은 인시던트 응답 프로세스를 자동화 하는 Azure Security Center 및 센티널과 같은 Azure 서비스를 사용 하는 것을 포함 하 여 인시던트 응답 수명 주기 (준비, 검색 및 분석, 포함 및 인시던트 사후 작업)의 컨트롤을 포함 합니다. |
| [상황 &nbsp; 및 &nbsp; 취약성 &nbsp; 관리 &nbsp; (PV)](security-controls-v2-posture-vulnerability-management.md) | 환경 및 취약성 관리는 취약성 검색, 침투 테스트 및 수정, Azure 리소스의 보안 구성 추적, 보고, 수정 등 Azure 보안 상태를 평가 하 고 개선 하기 위한 제어에 중점을 둘 것입니다. |
| [끝점 &nbsp; 보안 &nbsp;](security-controls-v2-endpoint-security.md) | 끝점 보안은 Azure 환경에서 끝점에 대해 EDR (끝점 검색 및 응답) 및 맬웨어 방지 서비스를 사용 하는 것을 포함 하 여 끝점 검색 및 응답의 컨트롤을 포함 합니다. |
| [백업 &nbsp; 및 &nbsp; 복구 &nbsp; (BR)](security-controls-v2-backup-recovery.md) | 백업 및 복구는 서로 다른 서비스 계층의 데이터 및 구성 백업이 수행 되 고, 유효성을 검사 하 고, 보호 되도록 하는 컨트롤을 포함 합니다. |
| [거 버 넌 스 &nbsp; 및 &nbsp; 전략 &nbsp; (GS)](security-controls-v2-governance-strategy.md) | 거 버 넌 스 및 전략은 다양 한 클라우드 보안 기능, 통합 기술 전략, 지원 정책 및 표준에 대 한 역할 및 책임을 설정 하는 것을 포함 하 여 일관 된 보안 전략 및 문서화 된 거 버 넌 스 접근 방법을 제공 하는 지침을 제공 합니다. |

## <a name="azure-security-benchmark-recommendations"></a>Azure 보안 벤치 마크 권장 사항

각 권장 사항에는 다음 정보가 포함 됩니다.

- **AZURE ID**: 권장 사항에 해당 하는 Azure 보안 벤치 마크 ID입니다.
- **Cis 컨트롤 v 7.1 ID**: cis는이 권장 사항에 해당 하는 v 7.1 제어를 제어 합니다.
- **NIST SP800-533ID (s)**:이 권장 사항에 해당 하는 nist SP800-53 4 (보통) 컨트롤입니다.
- **세부 정보**: 권장 사항 및 구현 방법에 대 한 지침에 대 한 링크를 제공 합니다. Azure Security Center에서 권장 사항을 지원 하면 해당 정보도 나열 됩니다.
- **책임**: 고객, 서비스 공급자 또는 둘 다가이 권장 사항을 구현할 책임이 있는지 여부입니다. 공용 클라우드에서 보안 책임이 공유 됩니다. 일부 보안 제어는 클라우드 서비스 공급자에만 사용할 수 있으므로 공급자는 이러한 제어의 주소를 지정 해야 합니다. 이러한 사항은 일반적인 관찰입니다. 일부 개별 서비스의 경우에는 Azure 보안 벤치 마크에 나열 된 것과는 다른 책임이 있습니다. 이러한 차이점은 개별 서비스에 대 한 기본 권장 사항에 설명 되어 있습니다.
- **고객 보안 관련자**: 고객 조직의 [보안 기능](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) 으로, 해당 컨트롤에 대해 책임을 지지 하거나 담당 하거나이를 검색할 수 있습니다. 회사의 보안 조직 구조와 Azure 보안과 관련 하 여 설정 하는 역할 및 책임에 따라 조직 마다 다를 수 있습니다.

> [!NOTE]
> ASB와 업계 벤치 마크 (예: NIST 및 CI) 간의 제어 매핑은 특정 Azure 기능을 사용 하 여 NIST 또는 CI에 정의 된 제어 요구 사항을 완전히 또는 부분적으로 처리할 수 있음을 표시 합니다. 이러한 구현이 CI 또는 NIST에서 해당 컨트롤의 완전 한 규정 준수로 반드시 변환 되는 것은 아닙니다.

Azure 보안 벤치 마크 활동에 대 한 자세한 피드백 및 활성 참여를 환영 합니다. Azure 보안 벤치 마크 팀 다이렉트 입력을 제공 하려는 경우 다음 양식을 작성 하세요. https://aka.ms/AzSecBenchmark

## <a name="download"></a>다운로드

[스프레드시트 형식](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)으로 Azure 보안 벤치 마크를 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계 
- 첫 번째 보안 제어: [네트워크 보안](security-control-network-security.md) 을 참조 하세요.
- [Azure 보안 벤치 마크 소개](introduction.md) 읽기
- [Azure 보안 기본 사항](../fundamentals/index.yml) 알아보기
