---
title: Azure Security Benchmark V2 개요
description: Azure Security Benchmark V2 개요
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3bc97a932e622838315f717cc7b036a559787805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369160"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Azure Security Benchmark(V2) 개요

ASB(Azure Security Benchmark)는 Azure에서 워크로드, 데이터, 서비스의 보안을 개선하는 데 도움이 되는 규범적인 모범 사례 및 권장 사항을 제공합니다.

이 벤치마크는 다음을 포함하는 전체적인 보안 지침의 일부입니다.

- **클라우드 채택 프레임워크** – [전략](/azure/cloud-adoption-framework/strategy/define-security-strategy), [역할 및 책임](/azure/cloud-adoption-framework/organize/cloud-security), [최우수 Azure 보안 모범 사례 10가지](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices), [참조 구현](/azure/cloud-adoption-framework/ready/enterprise-scale/)을 포함한 보안에 대한 지침입니다.
- **Azure Well-Architected Framework** – Azure에서 [워크로드를 보호](/assessments/?mode=pre-assessment&session=local)하기 위한 지침입니다.
- **Microsoft 보안 모범 사례** – [권장 사항](/security/compass/microsoft-security-compass-introduction) 및 Azure에서의 예시입니다.

 Azure Security Benchmark는 클라우드 중심 제어 영역에 중점을 둡니다. 이러한 컨트롤은 CIS(Center for Internet Security) Controls 버전 7.1 및 NIST(미국 국립표준기술원) SP 800-53에 설명된 것과 같은 잘 알려진 보안 벤치마크와 일치합니다.
Azure Security Benchmark에는 다음 컨트롤이 포함되어 있습니다.

| ASB 제어 도메인 | 설명 
|--|--|
| [NS(네트워크&nbsp;보안)](security-controls-v2-network-security.md)&nbsp; | 네트워크 보안은 가상 네트워크 보안 유지, 프라이빗 연결 구축, 외부 공격 방지 및 완화, DNS 보안 등 Azure 네트워크의 보안 및 보호를 위한 컨트롤을 포함합니다. |
| [IM(ID&nbsp;관리)](security-controls-v2-identity-management.md)&nbsp; | ID 관리에서는 애플리케이션, 조건부 액세스, 계정 변칙 모니터링을 위해 Single Sign-On, 강력한 인증, 관리 ID(및 서비스 원칙)를 사용하여 Azure Active Directory를 사용하는 보안 ID 및 액세스 제어를 설정하는 컨트롤을 다룹니다. |
| [PA(권한&nbsp;액세스)](security-controls-v2-privileged-access.md)&nbsp; | 권한 액세스는 의도적인 위험과 우발적인 위험으로부터 관리 모델, 관리 계정, 권한 있는 액세스 워크스테이션을 보호하는 다양한 컨트롤을 포함하여 Azure 테넌트 및 리소스에 대한 권한 액세스를 보호하는 컨트롤을 포함합니다. |
| [DP(데이터&nbsp;보호)](security-controls-v2-data-protection.md)&nbsp; | 데이터 보호는 Azure에서 액세스 제어, 암호화, 로깅을 사용하여 중요한 데이터 자산을 검색, 분류, 보호, 모니터링하는 것을 포함하여 미사용, 전송 중, 권한 액세스 메커니즘을 통한 데이터 보호 제어를 포함합니다. |
| [AM(자산&nbsp;관리)](security-controls-v2-asset-management.md)&nbsp; | 자산 관리는 보안 담당자, 자산 인벤토리에 대한 보안 액세스, 서비스 및 리소스에 대한 승인 관리(인벤토리, 추적, 수정) 권한에 대한 권장 사항을 포함하여 Azure 리소스에 대한 보안 가시성 및 거버넌스를 보장하는 컨트롤을 포함합니다. |
| [LT(로깅&nbsp;및&nbsp;위협&nbsp;탐지)](security-controls-v2-logging-threat-detection.md) | 로깅 및 위협 탐지는 Azure에서 위협을 탐지하고, Azure 서비스의 기본 위협 탐지를 사용하여 높은 품질의 경고를 생성하는 컨트롤을 통해 탐지, 조사, 수정 프로세스를 사용하도록 설정하는 것을 비롯하여 Azure에서 위협을 탐지하고 Azure 서비스에 대한 감사 로그를 활성화, 수집, 저장하고는 컨트롤을 포함합니다. 또한 Azure Monitor를 통한 로그 수집, Azure Sentinel을 통한 보안 분석 중앙 집중화, 시간 동기화, 로그 보존을 포함합니다. |
| [IR(인시던트&nbsp;응답)](security-controls-v2-incident-response.md)&nbsp; | 인시던트 응답은 인시던트 응답 프로세스를 자동화는 Azure Security Center 및 Sentinel과 같은 Azure 서비스를 사용하는 것을 포함하여 인시던트 응답 수명 주기(준비, 탐지 및 분석, 저지, 인시던트 사후 작업)의 컨트롤을 포함합니다. |
| [PV(태세&nbsp;및&nbsp;취약성&nbsp;관리)](security-controls-v2-posture-vulnerability-management.md)&nbsp; | 태세 및 취약성 관리는 취약성 검사, 침투 테스트, 문제 해결, Azure 리소스의 보안 구성 추적, 보고, 수정 등 Azure 보안 태세를 평가하고 개선하기 위한 제어에 중점을 둡니다. |
| [ES(엔드포인트&nbsp;보안)](security-controls-v2-endpoint-security.md)&nbsp; | 엔드포인트 보안은 Azure 환경에서 엔드포인트에 대해 EDR(엔드포인트 검색 및 응답) 및 맬웨어 방지 서비스를 사용하는 것을 포함하여 엔드포인트 검색 및 응답의 컨트롤을 포함합니다. |
| [BR(백업&nbsp;및&nbsp;복구)](security-controls-v2-backup-recovery.md)&nbsp; | 백업 및 복구는 여러 서비스 계층의 데이터 및 구성 백업이 수행되고, 유효성이 검사되고, 보호되도록 하는 컨트롤을 포함합니다. |
| [GS(거버넌스&nbsp;및&nbsp;전략)](security-controls-v2-governance-strategy.md)&nbsp; | 거버넌스 및 전략은 다양한 클라우드 보안 기능, 통합 기술 전략, 지원 정책, 표준에 대한 역할 및 책임을 설정하는 것을 포함하여 보안 보장을 안내하고 유지하기 위한 일관된 보안 전략 및 문서화된 거버넌스 접근 방식을 지원하는 참고 자료를 제공합니다. |

## <a name="azure-security-benchmark-recommendations"></a>Azure Security Benchmark 권장 사항

각 권장 사항에는 다음 정보가 포함되어 있습니다.

- **Azure ID**: 권장 사항에 해당하는 Azure Security Benchmark ID입니다.
- **CIS Controls v7.1 ID**: 이 권장 사항에 해당하는 CIS는 Controls v7.1 컨트롤입니다.
- **NIST SP 800-53 r4 ID**: 이 권장 사항에 해당하는 NIST SP 800-53 r4(보통) 컨트롤입니다.
- **세부 정보**: 권장 사항의 근거와 구현 방법에 대한 지침 링크입니다. Azure Security Center에서 권장 사항을 지원하면 해당 정보도 나열됩니다.
- **책임**: 이 권장 사항을 고객과 서비스 제공 업체 중 하나만 실행하면 되는지 또는 둘 다 실행해야 하는지입니다. 보안 책임은 퍼블릭 클라우드에서 공유됩니다. 일부 보안 제어는 클라우드 서비스 공급자만 사용할 수 있으므로 공급자는 이러한 주소를 지정할 책임이 있습니다. 이는 일반적인 관찰 사항입니다. 일부 개별 서비스의 경우 책임은 Azure Security Benchmark에 나열된 것과 다릅니다. 해당 차이점은 개별 서비스에 대한 기본 권장 사항에 설명되어 있습니다.
- **고객 보안 관련자**: 고객 조직에서 해당 컨트롤을 담당하거나, 책임을 지거나, 자문을 제공하는 [보안 부서](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)입니다. 회사의 보안 조직 구조, Azure 보안과 관련하여 설정하는 역할 및 책임에 따라 조직마다 다를 수 있습니다.

> [!NOTE]
> ASB와 업계 벤치마크(예: NIST 및 CIS) 간의 제어 매핑은 특정 Azure 기능을 사용하여 NIST 또는 CIS에 정의된 제어 요구 사항을 전체적으로 또는 부분적으로 처리할 수 있음을 표시합니다. 이를 실행한다고 해서 CIS 또는 NIST에서 해당 컨트롤을 완전히 준수하는 것은 아닙니다.

Azure Security Benchmark 활동에 대한 자세한 피드백 및 활성 참여를 환영합니다. Azure 보안 벤치마크 팀에 직접 입력을 제공하려면 https://aka.ms/AzSecBenchmark 에서 양식을 작성하세요.

## <a name="download"></a>다운로드

[스프레드시트 형식](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)의 Azure Security Benchmark를 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계 
- 첫 번째 보안 제어: [네트워크 보안](security-control-network-security.md) 참조
- [Azure 보안 벤치마크 소개](introduction.md) 참조
- [Azure 보안 기본 사항](../fundamentals/index.yml)에 대해 알아보세요.