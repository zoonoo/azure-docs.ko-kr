---
title: Azure Security 벤치 마크 V2-거 버 넌 스 및 전략
description: Azure 보안 벤치 마크 V2 거 버 넌 스 및 전략
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 420ab87591e230592ec8f728c6acb155266b5912
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403529"
---
# <a name="security-control-v2-governance-and-strategy"></a>보안 제어 V2: 거 버 넌 스 및 전략

거 버 넌 스 및 전략은 다양 한 클라우드 보안 기능, 통합 기술 전략, 지원 정책 및 표준에 대 한 역할 및 책임을 설정 하는 것을 포함 하 여 일관 된 보안 전략 및 문서화 된 거 버 넌 스 접근 방법을 제공 하는 지침을 제공 합니다.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

시스템 및 데이터를 지속적으로 모니터링 하 고 보호 하기 위한 명확한 전략을 문서화 하 고 통신 해야 합니다. 비즈니스에 중요 한 데이터 및 시스템의 검색, 평가, 보호 및 모니터링에 우선 순위를 지정 합니다. 

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

- 비즈니스 위험에 따라 데이터 분류 표준

- 위험 및 자산 인벤토리에 대 한 보안 조직 가시성 

- 사용할 Azure 서비스의 보안 조직 승인 

- 수명 주기를 통한 자산의 보안

- 조직 데이터 분류에 따라 필요한 액세스 제어 전략

- Azure 기본 및 타사 데이터 보호 기능 사용

- 전송 중 및 미사용 사용 사례에 대 한 데이터 암호화 요구 사항

- 적절 한 암호화 표준

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 아키텍처 권장 사항-저장소, 데이터 및 암호화](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 보안 기본 사항-Azure 데이터 보안, 암호화 및 저장소](../fundamentals/encryption-overview.md)

- [클라우드 채택 프레임 워크-Azure 데이터 보안 및 암호화 모범 사례](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 보안 벤치 마크-자산 관리](security-controls-v2-asset-management.md)

- [Azure 보안 벤치 마크-데이터 보호](security-controls-v2-data-protection.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 조각화 전략 정의

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Id, 네트워크, 응용 프로그램, 구독, 관리 그룹 및 기타 제어의 조합을 사용 하 여 자산에 대 한 액세스를 분할 하는 엔터프라이즈 수준의 전략을 수립 합니다.

서로 통신 해야 하는 시스템의 일상 작업을 수행 하 고 데이터에 액세스 해야 하는 경우에는 보안 분리의 필요성을 신중 하 게 조정 해야 합니다.

조각화 전략은 네트워크 보안, id 및 액세스 모델, 응용 프로그램 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯 하 여 컨트롤 형식 간에 일관 되 게 구현 되는지 확인 합니다.

- [Azure의 조각화 전략에 대 한 지침 (비디오)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 조각화 전략에 대 한 지침 (문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [기업 구분 전략을 사용 하 여 네트워크 조각화 맞추기](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 상태 관리 전략 정의

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

개별 자산과 해당 자산이 호스트 되는 환경에 대 한 위험을 지속적으로 측정 하 고 완화 합니다. 게시 된 응용 프로그램, 네트워크 수신 및 송신 지점과 사용자 및 관리자 끝점 등의 높은 가치 자산 및 높은 수준의 공격 노출 영역에 우선 순위를 지정 합니다.

- [Azure 보안 벤치 마크-상태 및 취약성 관리](security-controls-v2-posture-vulnerability-management.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임 및 accountabilities 정렬

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| GS-4 | 해당 없음 | PL, PM |

보안 조직의 역할 및 책임에 대 한 명확한 전략을 문서화 하 고 전달 하는지 확인 합니다. 보안에 대 한 책임을 명확 하 게 제공 하 고, 공유 책임 모델에 대 한 모든 것을 교육 하 고, 클라우드를 보호 하는 기술 팀을 교육 합니다.

- [Azure 보안 모범 사례 1 – 사람: 클라우드 보안 경험에 대 한 팀 교육](https://aka.ms/AzSec1)

- [Azure 보안 모범 사례 2-사람: 클라우드 보안 기술에 대 한 팀 교육](https://aka.ms/AzSec2)

- [Azure 보안 모범 사례 3-프로세스: 클라우드 보안 결정에 대 한 책임 할당](https://aka.ms/AzSec3)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| GS-5 | 9 | CA, SC |

조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정 합니다.  

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

- 중앙 집중식 네트워크 관리 및 보안 책임

- 엔터프라이즈 조각화 전략에 맞춘 가상 네트워크 조각화 모델

- 다양 한 위협 및 공격 시나리오에서 수정 전략

- 인터넷에 지 및 수신 및 송신 전략

- 하이브리드 클라우드 및 온-프레미스 상호 연결과 전략

- 최신 네트워크 보안 아티팩트 (예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure 보안 모범 사례 11-아키텍처. 단일 통합 보안 전략](https://aka.ms/AzSec11)

- [Azure 보안 벤치 마크-네트워크 보안](security-controls-v2-network-security.md)

- [Azure 네트워크 보안 개요](../fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: id 및 권한 있는 액세스 전략 정의

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure id 및 권한 있는 액세스 접근 방법을 설정 합니다.  

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

- 다른 내부 및 외부 id 시스템을 사용 하는 중앙 집중식 id와 인증 시스템 및 상호 연결과

- 다양 한 사용 사례 및 조건에서 강력한 인증 방법

- 높은 권한이 있는 사용자의 보호

- 변칙 사용자 활동 모니터링 및 처리  

- 사용자 id 및 액세스 검토 및 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure 보안 벤치 마크-Id 관리](security-controls-v2-identity-management.md)

- [Azure 보안 벤치 마크-권한 있는 액세스](security-controls-v2-privileged-access.md)

- [Azure 보안 모범 사례 11-아키텍처. 단일 통합 보안 전략](https://aka.ms/AzSec11)

- [Azure ID 관리 보안 개요](../fundamentals/identity-management-overview.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 응답 전략 정의

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

규정 준수 요구 사항을 충족 하면서 신속 하 게 위협을 감지 하 고 수정할 수 있는 로깅 및 위협 대응 전략을 수립 합니다. 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 고품질 경고와 원활한 환경을 제공 하는 분석가의 우선 순위를 지정 합니다. 

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

- 보안 작업 (SecOps) 조직의 역할 및 책임 

- NIST 또는 다른 업계 프레임 워크를 사용 하 여 잘 정의 된 인시던트 응답 프로세스 

- 위협 감지, 인시던트 대응 및 규정 준수 요구 사항을 지원 하기 위한 로그 캡처 및 보존

- SIEM, 네이티브 Azure 기능 및 기타 소스를 사용 하 여 위협에 대 한 중앙 집중식 및 상관 관계 정보 표시 

- 고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

- 로깅 및 위협 검색, 관련자, 공격 수정 및 eradication 같은 인시던트 처리를 위해 Azure native 및 타사 플랫폼 사용

- 학습 및 증거 보관과 같은 인시던트 및 인시던트 후 활동을 처리 하는 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 벤치 마크-로깅 및 위협 검색](security-controls-v2-logging-threat-detection.md)

- [Azure 보안 벤치 마크-인시던트 응답](security-controls-v2-incident-response.md)

- [Azure 보안 모범 사례 4-프로세스. 클라우드에 대 한 인시던트 응답 프로세스 업데이트](https://aka.ms/AzSec4)

- [Azure 채택 프레임 워크, 로깅 및 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리 및 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: 백업 및 복구 전략 정의

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| GS-8 | 10 | CP |

조직의 Azure backup 및 복구 전략을 설정 합니다. 

이 전략에는 다음 요소에 대 한 문서화 된 지침, 정책 및 표준이 포함 되어야 합니다. 

- 비즈니스 복원 력 목표에 따라 RTO (복구 시간 목표) 및 RPO (복구 지점 목표) 정의

- 응용 프로그램 및 인프라 설정의 중복성 설계

- 액세스 제어 및 데이터 암호화를 사용한 백업 보호

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 벤치 마크-백업 및 복구](security-controls-v2-backup-recovery.md)

- [Azure 응용 프로그램에 대 한 azure 잘 아키텍처 프레임 워크-백업 및 재해 복구](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure 채택 프레임 워크-비즈니스 연속성 및 재해 복구](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

