---
title: Azure Security Benchmark V2 - 거버넌스 및 전략
description: Azure Security Benchmark V2 거버넌스 및 전략
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 277033e41ec7e02b89eca8cf74fe6854acb51cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727025"
---
# <a name="security-control-v2-governance-and-strategy"></a>보안 제어 V2: 거버넌스 및 전략

거버넌스 및 전략은 다양한 클라우드 보안 기능, 통합 기술 전략, 지원 정책, 표준에 대한 역할 및 책임을 설정하는 것을 포함하여 보안 보장을 안내하고 유지하기 위한 일관된 보안 전략 및 문서화된 거버넌스 접근 방식을 지원하는 참고 자료를 제공합니다.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

시스템과 데이터를 지속적으로 모니터링하고 보호하기 위한 명확한 전략을 문서화하고 전달합니다. 중요 비즈니스용 데이터 및 시스템의 검색, 평가, 보호, 모니터링에 우선 순위를 지정합니다.

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

- 비즈니스 위험에 따른 데이터 분류 표준

- 위험 및 자산 인벤토리에 대한 보안 조직의 가시성

- 사용할 Azure 서비스에 대한 보안 조직의 승인

- 수명 주기 전체에서 자산 보안

- 조직 데이터 분류에 따른 필수 액세스 제어 전략

- Azure 기본 및 타사 데이터 보호 기능 사용

- 전송 중 및 저장 사용 사례에 대한 데이터 암호화 요구 사항

- 적절한 암호화 표준

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - 자산 관리](security-controls-v2-asset-management.md)

- [Azure Security Benchmark - 데이터 보호](security-controls-v2-data-protection.md)

**책임**: Customer

**고객 보안 관련자**([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 구분 전략 정의

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

ID, 네트워크, 애플리케이션, 구독, 관리 그룹 및 기타 제어를 조합하여 자산에 대한 액세스를 조각화하는 엔터프라이즈 수준 전략을 수립합니다.

서로 통신하고 데이터에 액세스해야 하는 시스템의 일상 작업을 사용하도록 설정해야 할 필요성과 보안 분리의 필요성을 신중하게 조정해야 합니다.

구분 전략이 네트워크 보안, ID 및 액세스 모델, 애플리케이션 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯하여 컨트롤 형식 간에 일관되게 구현되는지 확인합니다.

- [Azure의 구분 전략에 대한 지침(동영상)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 구분 전략에 대한 지침(문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [엔터프라이즈 구분 전략에 맞춰 네트워크 구분 조정](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**책임**: Customer

**고객 보안 관련자**([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 태세 관리 전략 정의

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

개별 자산과 해당 자산이 호스팅되는 환경에 대한 위험을 지속적으로 측정하고 완화합니다. 게시된 애플리케이션, 네트워크 수신 및 송신 지점, 사용자 및 관리자 엔드포인트 등과 같은 고가치 자산과 노출이 많은 공격 노출 영역에 우선 순위를 지정합니다.

- [Azure Security Benchmark - 태세 및 취약성 관리](security-controls-v2-posture-vulnerability-management.md)

**책임**: Customer

**고객 보안 관련자**([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임, 의무 조정

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-4 | 해당 없음 | PL, PM |

보안 조직의 역할 및 책임에 대한 명확한 전략을 문서화하고 전달합니다. 보안 의사 결정에 대한 명확한 책임을 제시하고, 공유 책임 모델을 모두에게 교육하고, 클라우드를 보호하는 기술에 관해 기술 팀을 교육하는 일에 우선 순위를 지정합니다.

- [Azure 보안 모범 사례 1 – 사용자: 클라우드 보안 경험에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2 – 사용자: 클라우드 보안 기술에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3 – 프로세스: 클라우드 보안 결정에 대한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**책임**: Customer

**고객 보안 관련자**([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-5 | 9 | CA, SC |

조직의 전체 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정합니다.

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다.

- 중앙 집중식 네트워크 관리 및 보안 책임

- 엔터프라이즈 구분 전략에 맞춰 조정된 가상 네트워크 구분 모델

- 다양한 위협 및 공격 시나리오에서 수정 전략

- 인터넷 에지 및 수신/송신 전략

- 하이브리드 클라우드 및 온-프레미스 상호 연결 전략

- 최신 네트워크 보안 아티팩트(예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - 네트워크 보안](security-controls-v2-network-security.md)

- [Azure 네트워크 보안 개요](../fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**책임**: Customer

**고객 보안 관련자**([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID 및 권한 있는 액세스 전략 정의

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

조직의 전체 보안 액세스 제어 전략의 일부로 Azure ID 및 권한 있는 액세스 방법을 설정합니다.

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다.

- 중앙 집중식 ID 및 인증 시스템과 다른 내부 및 외부 ID 시스템 간의 상호 연결

- 다양한 사용 사례 및 조건에서 강력한 인증 방법

- 권한이 높은 사용자 보호

- 변칙 사용자 활동 모니터링 및 처리

- 사용자 ID 및 액세스 검토와 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - ID 관리](security-controls-v2-identity-management.md)

- [Azure Security Benchmark - 권한 있는 액세스](security-controls-v2-privileged-access.md)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../fundamentals/identity-management-overview.md)

**책임**: Customer

**고객 보안 관련자**([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 대응 전략 정의

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

로그 및 위협 대응 전략을 수립하여 규정 준수 요구 사항을 충족하면서 위협을 신속하게 탐지하고 수정합니다. 분석가가 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 분석가에게 고품질 경고와 원활한 환경을 제공하는 데 적용되는 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

- 보안 작업(SecOps) 조직의 역할 및 책임 

- NIST 또는 다른 업계 프레임워크를 사용하여 잘 정의된 인시던트 응답 프로세스 

- 위협 탐지, 인시던트 응답 및 규정 준수 요구 사항을 지원하는 로그 캡처 및 보존

- SIEM, 네이티브 Azure 기능, 기타 소스를 사용하여 위협에 대한 중앙 집중식 가시성 및 상관 관계 정보 

- 고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

- 로깅 및 위협 탐지, 포렌식, 공격 수정, 제거와 같은 인시던트 처리를 위해 Azure 네이티브 및 타사 플랫폼 사용

- 확인한 상황 및 증거 보존을 포함하여 인시던트 및 인시던트 후 활동을 처리하는 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure Security Benchmark - 로깅 및 위협 탐지](security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - 인시던트 응답](security-controls-v2-incident-response.md)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리, 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**책임**: Customer

**고객 보안 관련자**([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: 백업 및 복구 전략 정의

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-8 | 10 | CP |

조직의 Azure 백업 및 복구 전략을 수립합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

- 비즈니스 복원력 목표에 따라 복구 시간 목표(RTO) 및 복구 지점 목표(RPO) 정의

- 애플리케이션 및 인프라 설정의 중복도 설계

- 액세스 제어 및 데이터 암호화를 사용한 백업 보호

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure Security Benchmark - 백업 및 복구](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework - Azure 애플리케이션에 대한 백업 및 재해 복구](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure Adoption Framework - 비즈니스 연속성 및 재해 복구](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**책임**: Customer

**고객 보안 관련자**([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [모든 관련자](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)