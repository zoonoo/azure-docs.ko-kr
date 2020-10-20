---
title: Cloud Shell에 대 한 Azure 보안 기준
description: Cloud Shell 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 463bbe637eee26ab098d1531976a18999497d12f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210241"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Cloud Shell에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Cloud Shell 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Cloud Shell에 적용 되는 관련 지침에 따라 그룹화 됩니다. Cloud Shell에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

 
Cloud Shell 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Cloud Shell 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 고객은 고객이 소유한 Virtual Network에 Azure Cloud Shell을 배포할 수 있습니다.

고객 소유 Virtual Network에 Azure Cloud Shell를 배포 하는 경우 기존 가상 네트워크를 만들거나 사용 해야 합니다. 선택한 가상 네트워크에 해당 서브넷에 적용 된 네트워크 보안 그룹 및 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 맞게 구성 된 네트워크 액세스 제어가 있는지 확인 합니다.

- [Azure 가상 네트워크에 Cloud Shell 배포](private-vnet.md)

- [보안 규칙을 사용 하 여 네트워크 보안 그룹을 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure 방화벽을 배포 및 구성 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조 하세요.*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure Cloud Shell는 Azure Portal에 액세스 하는 데 사용 되는 것과 동일한 권한 부여를 사용 하는 브라우저 기반 명령줄 환경입니다 .이 경우에는 Azure Portal에 대 한 SSO도 Cloud Shell를 사용 하 여 인증 합니다. 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Cloud Shell는 Azure Portal에 액세스 하는 데 사용 되는 것과 동일한 권한 부여를 사용 하는 브라우저 기반 명령줄 환경입니다 .이 경우에는 Azure Portal에 연결 하는 데 필요한 MFA도 Cloud Shell에 필요 합니다. 

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Azure Cloud Shell는 클라우드 리소스의 대화형 관리에 사용 되는 브라우저 기반 명령줄 환경입니다.  각 고객 컨테이너는 각 세션에 대해 새 컨테이너를 사용 하 여 삭제 됩니다.  컨테이너 이미지는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다.

Azure Cloud Shell를 사용 하면 고객이 조직의 요구에 따라 자체 이미지에 자신의 도구 또는 소프트웨어를 설치할 수 있습니다.

고객은 환경에서 실행 되는 소프트웨어에 대해 자동화 된 취약점 검사 도구를 실행 해야 합니다.  

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 Azure Cloud Shell는 클라우드 리소스의 대화형 관리에 사용 되는 브라우저 기반 명령줄 환경입니다.  각 고객 컨테이너는 각 세션에 대해 새 컨테이너를 사용 하 여 삭제 됩니다.  컨테이너 이미지는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다.

Azure Cloud Shell를 사용 하면 고객이 조직의 요구에 따라 자체 이미지에 자신의 도구 또는 소프트웨어를 설치할 수 있습니다.

고객은 자신의 환경에서 실행 되는 소프트웨어 패치 관리를 담당 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: Azure Cloud Shell는 클라우드 리소스의 대화형 관리에 사용 되는 브라우저 기반 명령줄 환경입니다.  각 고객 컨테이너는 각 세션에 대해 새 컨테이너를 사용 하 여 삭제 됩니다.  컨테이너 이미지는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다.

Azure Cloud Shell를 사용 하면 고객이 조직의 요구에 따라 자체 이미지에 자신의 도구 또는 소프트웨어를 설치할 수 있습니다.

고객은 소프트웨어 취약성 검색을 통해 검색 되는 취약성을 해결 해야 합니다. 검색 결과는 일정 한 간격으로 내보내고 결과와 이전 검색을 비교 하 여 취약점이 재구성 되었는지 확인 합니다. Azure Security Center에서 제안 하는 취약성 관리 권장 사항을 사용 하는 경우 선택한 솔루션의 포털로 피벗 하 여 기록 검색 데이터를 볼 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Azure Cloud Shell는 클라우드 리소스의 대화형 관리에 사용 되는 브라우저 기반 명령줄 환경입니다.  각 고객 컨테이너는 각 세션에 대해 새 컨테이너를 사용 하 여 삭제 됩니다.  컨테이너 이미지는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다.

Azure Cloud Shell를 사용 하면 고객이 조직의 요구에 따라 자체 이미지에 자신의 도구 또는 소프트웨어를 설치할 수 있습니다.

고객은 소프트웨어 취약성 검색을 통해 검색 되는 취약성을 해결 해야 합니다.  일반적인 위험 점수 매기기 프로그램 (예: 일반적인 취약성 점수 매기기 시스템) 또는 타사 검사 도구에서 제공 하는 기본 위험 등급을 사용 합니다. 

- [NIST 게시--일반적인 취약성 점수 매기기 시스템](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: Azure Cloud Shell는 클라우드 리소스의 대화형 관리에 사용 되는 브라우저 기반 명령줄 환경입니다.  각 고객 컨테이너는 각 세션에 대해 새 컨테이너를 사용 하 여 삭제 됩니다.  컨테이너 이미지와 도구는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다.  고객은 조직의 요구에 따라 자체 이미지에 고유한 도구를 설치할 수 있으며 설치 중에는 도구에 권한이 필요 하지 않습니다 `sudo` .

고객은 조직의 요구에 따라 Azure Cloud Shell를 통해 설치 되는 승인 된 소프트웨어의 인벤토리를 만드는 것이 좋습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Azure Cloud Shell은 고객이 소유 하는 자산이 없는 무료 서비스입니다.  컨테이너 이미지와 도구는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다. 

Azure Cloud Shell를 사용 하면 고객이 조직의 요구에 따라 자체 이미지에 자신의 도구 또는 소프트웨어를 설치할 수 있습니다.

고객은 환경에서 실행 중인 소프트웨어 응용 프로그램을 모니터링 하 여 조직 정책에 따라 승인 되었는지 확인 해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Cloud Shell은 고객이 소유 하는 자산이 없는 무료 서비스입니다.  컨테이너 이미지와 도구는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다. 

Azure Cloud Shell를 사용 하면 고객이 조직의 요구에 따라 자체 이미지에 자신의 도구 또는 소프트웨어를 설치할 수 있습니다.

고객은 환경에서 실행 되는 소프트웨어 응용 프로그램을 모니터링 하 여 승인 되지 않은 소프트웨어가 조직 정책에 따라 관리 되는지 확인 해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Azure Cloud Shell은 고객이 소유 하는 자산이 없는 무료 서비스입니다.  컨테이너 이미지와 도구는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다.  특정 도구는 고객이 제거할 수 없습니다.

Azure Cloud Shell를 사용 하면 고객이 조직의 요구에 따라 자체 이미지에 자체 도구나 응용 프로그램을 설치할 수 있습니다.

고객은 환경에서 실행 중인 응용 프로그램을 모니터링 하 여 조직 정책에 따라 승인 되었는지 확인 해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 사항 없음 Azure Cloud Shell는 클라우드 리소스의 대화형 관리에 사용 되는 브라우저 기반 명령줄 환경입니다.  각 고객 컨테이너는 각 세션에 대해 새 컨테이너를 사용 하 여 삭제 됩니다.  컨테이너 이미지는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다.

Azure Cloud Shell를 사용 하면 고객이 조직의 요구에 따라 자체 이미지에 자신의 도구 또는 소프트웨어를 설치할 수 있습니다.

고객은 환경에서 실행 중인 승인 된 소프트웨어의 인벤토리를 유지 관리 하 여 조직 정책에 따라 승인 된 소프트웨어 인지 확인 해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: 사용자가 계산 리소스에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: Azure Cloud Shell는 클라우드 리소스의 대화형 관리에 사용 되는 브라우저 기반 명령줄 환경입니다.  Cloud Shell 내에서 수행 되는 작업은 동일한 도구 또는 언어에서 수행 되는 작업이 로컬 환경에서 실행 되는 작업과 동일 하 게 작동 합니다.  사용자가 Cloud Shell에 대 한 액세스를 제한 하거나 사용자가 사용할 수 있는 항목을 제한할 수 없도록 개별 도구 및 언어의 작업을 제한 해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: Azure Cloud Shell 고객 가상 네트워크에서 격리할 수 있습니다.

- [Azure 가상 네트워크에 Cloud Shell 배포](private-vnet.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: Cloud Shell을 사용 하 여에서 스크립트를 실행 하 고,에서 작성 하 고, Cloud Shell 환경으로 업로드할 수 있습니다.  자격 증명을 Azure Key Vault로 이동 하는 것이 좋습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: 중앙에서 관리 되는 맬웨어 방지 소프트웨어 사용

**지침**: Azure Cloud Shell는 클라우드 리소스의 대화형 관리에 사용 되는 브라우저 기반 명령줄 환경입니다.  각 고객 컨테이너는 각 세션에 대해 새 컨테이너를 사용 하 여 삭제 됩니다.  컨테이너 이미지와 도구는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다.  고객은 조직의 요구에 따라 자체 이미지에 고유한 도구를 설치할 수 있으며 설치 중에는 도구에 권한이 필요 하지 않습니다 `sudo` .

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: Azure Cloud Shell는 클라우드 리소스의 대화형 관리에 사용 되는 브라우저 기반 명령줄 환경입니다.  각 고객 컨테이너는 각 세션에 대해 새 컨테이너를 사용 하 여 삭제 됩니다.  컨테이너 이미지와 도구는 Cloud Shell 팀에 의해 모니터링 및 업데이트 됩니다.  고객은 조직의 요구에 따라 자체 이미지에 고유한 도구를 설치할 수 있으며 설치 중에는 도구에 권한이 필요 하지 않습니다 `sudo` .

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.
- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md) 
- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 경고를 실행 하는 데 사용 되는 Security Center를 찾고 있는 경우와 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 따라 결정 됩니다.
또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.
- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 
- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md). 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.
- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.
- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고 Sentinel을 스트리밍할 수 있습니다.
- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md) 
- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.
- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.
- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 
- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
