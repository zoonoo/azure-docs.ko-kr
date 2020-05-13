---
title: Azure Security Center의 새로운 기능 보관
description: 6 개월 전 및 이전에 Azure Security Center의 새로운 기능 및 변경 된 기능에 대 한 설명입니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: c0883e91d5e806fb166c3ddeafc4ce130ff3f66f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210844"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Azure Security Center의 새로운 기능에 대 한 보관

기본 제공 [Azure Active Directory의 새로운 기능](release-notes.md) 릴리스 정보 페이지에는 지난 6 개월 동안 업데이트가 포함 되어 있지만이 페이지에는 이전 항목이 포함 되어 있습니다.

이 페이지에서는 다음에 대 한 정보를 제공 합니다.

- 새 기능
- 버그 수정
- 사용되지 않는 기능

## <a name="november-2019"></a>2019년 11월

### <a name="threat-protection-for-azure-key-vault-in-public-preview-in-north-america-regions"></a>북아메리카 지역에서 공개 미리 보기로 제공 되는 Azure Key Vault에 대 한 위협 방지

Azure Key Vault는 클라우드에서 키, 비밀, 암호화 키 및 정책을 중앙에서 관리 하는 기능을 제공 하 여 데이터를 보호 하 고 클라우드 응용 프로그램의 성능을 향상 시키는 데 필수적인 서비스입니다. Azure Key Vault 중요 한 중요 업무용 데이터를 저장 하므로 키 자격 증명 모음 및 해당 데이터에 저장 된 데이터에 대 한 보안을 강화 해야 합니다.

Azure Key Vault에 대 한 위협 방지를 지 원하는 Azure Security Center은 키 자격 증명 모음에 액세스 하거나 악용 하려는 비정상적인 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 이 새로운 보호 계층을 통해 고객은 보안 전문가가 되거나 보안 모니터링 시스템을 관리 하지 않고도 주요 자격 증명 모음에 대 한 위협을 해결할 수 있습니다. 이 기능은 북아메리카 지역에서 공개 미리 보기로 제공 됩니다.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure Storage에 대 한 위협 방지에는 맬웨어 평판 심사 포함

Azure Storage에 대 한 위협 방지는 Microsoft 위협 인텔리전스에서 제공 하는 새로운 검색 기능을 제공 하 여 해시 평판 분석 및 활성 연결 종료 노드 (익명화 프록시)의 의심 스러운 액세스를 사용 하 여 Azure Storage에 맬웨어 업로드를 검색 합니다. 이제 Azure Security Center를 사용 하 여 저장소 계정에서 검색 된 맬웨어를 볼 수 있습니다.


### <a name="workflow-automation-with-logic-apps-preview"></a>Logic Apps를 사용 하는 워크플로 자동화 (미리 보기)

중앙에서 관리 되는 보안 및 IT/운영을 가진 조직은 내부 워크플로 프로세스를 구현 하 여 환경에서 불일치가 발견 될 때 조직 내에서 필요한 작업을 수행 합니다. 대부분의 경우 이러한 워크플로는 반복 가능한 프로세스 이며 자동화는 조직 내에서 프로세스를 대폭 간소화할 수 있습니다.

현재는 고객이 Azure Logic Apps를 활용 하는 자동화 구성을 만들고 권장 사항 또는 경고와 같은 특정 ASC 결과에 따라 자동으로 트리거하는 정책을 만들 수 있도록 하는 Security Center의 새로운 기능을 소개 합니다. Azure 논리 앱은 다양 한 논리 앱 커넥터 커뮤니티에서 지 원하는 사용자 지정 작업을 수행 하도록 구성 하거나, 전자 메일 보내기 또는 ServiceNow™ 티켓 열기와 같은 Security Center에서 제공 하는 템플릿 중 하나를 사용할 수 있습니다.

워크플로를 실행 하기 위한 자동 및 수동 Security Center 기능에 대 한 자세한 내용은 [워크플로 자동화](workflow-automation.md)를 참조 하세요.

Logic Apps를 만드는 방법에 대 한 자세한 내용은 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)을 참조 하세요.


### <a name="quick-fix-for-bulk-resources-generally-available"></a>일반적으로 사용 가능한 대량 리소스에 대 한 빠른 수정

보안 점수의 일부로 사용자가 제공 하는 많은 작업이 포함 되어 있으므로 대기업에서 문제를 효과적으로 해결 하는 기능이 어려울 수 있습니다.

보안 구성 오류를 간소화 하 고 대량의 리소스에 대 한 권장 사항을 빠르게 수정 하 고 보안 점수를 향상할 수 있도록 하려면 빠른 수정 재구성을 사용 합니다.

이 작업을 통해 관리를 적용 하려는 리소스를 선택 하 고 사용자 대신 설정을 구성 하는 수정 작업을 시작할 수 있습니다.

빠른 수정은 일반적으로 현재 고객이 Security Center 권장 사항 페이지의 일부로 제공 됩니다.

[보안 권장 사항에 대 한 참조 가이드](recommendations-reference.md)에서 빠른 수정이 사용 하도록 설정 된 권장 사항을 확인 하세요.


### <a name="scan-container-images-for-vulnerabilities-preview"></a>취약성에 대 한 컨테이너 이미지 검색 (미리 보기)

이제 Azure Security Center Azure Container Registry의 컨테이너 이미지를 검색 하 여 취약성을 확인할 수 있습니다.

이미지 검사는 컨테이너 이미지 파일을 구문 분석 한 다음 알려진 취약점 (Qualys으로 구동)이 있는지 확인 하는 방식으로 작동 합니다.

검색 자체는 Azure Container Registry에 새 컨테이너 이미지를 푸시할 때 자동으로 트리거됩니다. 발견 된 취약점은 Security Center 권장 사항으로 노출 되 고 Azure 보안 점수에 포함 되며, 허용 되는 공격 노출 영역을 줄이기 위해 패치 하는 방법에 대 한 정보와 함께 제공 됩니다.


### <a name="additional-regulatory-compliance-standards-preview"></a>추가 규정 준수 표준 (미리 보기)

규정 준수 대시보드는 Security Center 평가에 따라 규정 준수 상태에 대 한 통찰력을 제공 합니다. 이 대시보드는 특정 규제 표준 및 업계 벤치 마크에 지정 된 컨트롤 및 요구 사항을 준수 하는 환경을 보여 주고 이러한 요구 사항을 해결 하는 방법에 대 한 규범적인 권장 사항을 제공 합니다.

규정 준수 대시보드는 Azure CIS 1.1.0, PCI DSS, ISO 27001 및 SOC-TSP의 네 가지 기본 제공 표준을 지원 했습니다. 이제 지원 되는 추가 표준의 공개 미리 보기 릴리스를 발표 하 고 있습니다. NIST SP 800-53 4, SWIFT CSP CSCF v2020, 캐나다 연방 PMM 및 영국 공식 (영국 NHS) 또한 표준에서 더 많은 제어를 제공 하 고 확장성을 개선 하는 Azure CIS 1.1.0의 업데이트 된 버전을 출시 하 고 있습니다.

[규정 준수 대시보드의 표준 집합을 사용자 지정 하는 방법에 대해 자세히 알아보세요](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service에 대 한 위협 방지 (미리 보기)

Kubernetes는 클라우드에서 소프트웨어를 배포 하 고 관리 하기 위한 새로운 표준으로 신속 하 게 진행 됩니다. Kubernetes에 대 한 광범위 한 경험을 보유 하 고 있는 사람들은 일반적으로 일반적인 엔지니어링 및 관리에 중점을 둘 뿐만 아니라 보안 측면을 간과 합니다. Kubernetes 환경을 안전 하 게 구성 해야 하 고, 컨테이너에 초점을 맞춘 공격 노출 영역 문이 공격자에 게 노출 되지 않도록 해야 합니다. Security Center는 컨테이너 공간의 지원 기능을 Azure AKS (Azure Kubernetes Service)에서 가장 빠르게 증가 하는 서비스 중 하나로 확장 합니다.

이 공개 미리 보기 릴리스의 새로운 기능은 다음과 같습니다.

- **검색 & 표시 유형** -Security Center 등록 된 구독 내에서 관리 되는 AKS 인스턴스를 지속적으로 검색 합니다.
- **보안 점수 권장 사항** -고객의 보안 점수에 AKS의 보안 모범 사례를 준수 하는 데 도움이 되는 조치 가능한 항목 (예: "역할 기반 Access Control Kubernetes 서비스 클러스터에 대 한 액세스를 제한 하는 데 사용 해야 함").
- **위협 검색** -호스트 및 클러스터 기반 분석 (예: "특권 컨테이너 검색 됨")


### <a name="virtual-machine-vulnerability-assessment-preview"></a>가상 컴퓨터 취약성 평가 (미리 보기)

가상 컴퓨터에 설치 된 응용 프로그램에는 가상 컴퓨터의 위반으로 이어질 수 있는 취약성이 있을 수 있습니다. Security Center 표준 계층에는 추가 비용 없이 가상 컴퓨터에 대 한 기본 제공 취약성 평가가 포함 되어 있습니다. 공개 미리 보기에서 Qualys에 의해 제공 되는 취약성 평가를 통해 가상 머신에서 설치 된 모든 응용 프로그램을 지속적으로 검색 하 여 취약 한 응용 프로그램을 찾고 Security Center 포털의 경험에 대 한 결과를 제공할 수 있습니다. Security Center는 사용자의 추가 작업이 필요 하지 않도록 모든 배포 작업을 처리 합니다. 향후 고객의 고유한 비즈니스 요구 사항을 지원 하기 위한 취약성 평가 옵션을 제공할 계획입니다.

[Azure Virtual Machines에 대 한 취약성 평가에 대해 자세히 알아보세요](security-center-vulnerability-assessment-recommendations.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure Virtual Machines의 SQL server에 대 한 고급 데이터 보안 (미리 보기)

IaaS Vm에서 실행 되는 SQL Db에 대 한 위협 방지 및 취약성 평가에 대 한 Azure Security Center는 현재 미리 보기로 제공 됩니다.

[취약성 평가](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) 는 잠재적인 데이터베이스 취약성을 검색, 추적 및 해결 하는 데 도움이 되는 쉽게 구성할 수 있는 서비스입니다. Azure 보안 점수의 일부로 보안 상태에 대 한 가시성을 제공 하 고, 보안 문제를 해결 하 고 fortifications 데이터베이스를 개선 하는 단계를 포함 합니다.

[Advanced threat protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) 은 비정상적인 활동을 검색 하 여 SQL server에 액세스 하거나 악용 하려는 비정상적인 시도를 검색 합니다. 데이터베이스에서 의심 스러운 활동을 지속적으로 모니터링 하 고 비정상적인 데이터베이스 액세스 패턴에 대해 액션 지향 보안 경고를 제공 합니다. 이러한 경고는 의심 스러운 활동 세부 정보 및 위협을 조사 하 고 완화 하기 위한 권장 조치를 제공 합니다.


### <a name="support-for-custom-policies-preview"></a>사용자 지정 정책 지원 (미리 보기)

이제 Azure Security Center에서 사용자 지정 정책 (미리 보기)을 지원 합니다.

고객은 Azure Policy에서 만든 정책에 따라 자신의 보안 평가를 통해 Security Center의 현재 보안 평가 범위를 연장 하려고 했습니다. 이제 사용자 지정 정책에 대 한 지원을 통해이 기능을 사용할 수 있습니다.

이러한 새 정책은 Security Center 권장 사항 환경, 보안 점수 및 규정 준수 표준 대시보드에 포함 됩니다. 사용자 지정 정책에 대 한 지원을 통해 이제 Azure Policy에서 사용자 지정 이니셔티브를 만든 다음 Security Center에서 정책으로 추가 하 고 권장 사항으로 시각화할 수 있습니다.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>커뮤니티 및 파트너를 위한 플랫폼으로 Azure Security Center 범위 확장

Security Center를 사용 하 여 Microsoft 뿐만 아니라 더 많은 통합이 제공 되는 Check Point, Tenable 및 CyberArk와 같은 파트너의 기존 솔루션에서 권장 사항을 받을 수 있습니다.  Security Center의 간단한 온 보 딩 흐름은 기존 솔루션을 Security Center에 연결 하 여 보안 상태 권장 사항을 한 장소에서 확인 하 고, 통합 보고서를 실행 하 고, 기본 제공 및 파트너 권장 사항에 대 한 모든 Security Center 기능을 활용할 수 있도록 합니다. 파트너 제품에 대 한 Security Center 권장 사항을 내보낼 수도 있습니다.

[Microsoft 지능형 보안 연결에 대해 자세히 알아보세요](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>권장 구성 및 경고 내보내기와 고급 통합 (미리 보기)

Security Center 위에서 엔터프라이즈 수준 시나리오를 사용 하도록 설정 하기 위해 이제 Azure Portal 또는 API를 제외 하 고 추가 위치에서 Security Center 경고 및 권장 사항을 사용할 수 있습니다. 이러한 작업 영역을 이벤트 허브로 직접 내보내고 Log Analytics 작업 영역을 만들 수 있습니다. 이러한 새 기능을 통해 만들 수 있는 몇 가지 워크플로는 다음과 같습니다.

- Log Analytics 작업 영역으로 내보내기를 사용 하면 Power BI를 사용 하 여 사용자 지정 대시보드를 만들 수 있습니다.
- 이벤트 허브로 내보내기를 사용 하면 타사 SIEMs에 대 한 Security Center 경고 및 권장 사항, 데이터 탐색기 실시간으로 타사 솔루션으로 내보낼 수 있습니다.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>온-프레미스 서버를 Windows 관리 센터 (미리 보기)에서 Security Center에 등록

Windows 관리 센터는 백업 및 시스템 업데이트와 같은 몇 가지 Azure 관리 기능을 제공 하는 Azure에 배포 되지 않은 Windows 서버용 관리 포털입니다. 최근에 이러한 비 Azure 서버를 등록 하는 기능을 Windows 관리 센터 환경에서 바로 ASC로 보호 하는 기능을 추가 했습니다.

이 새로운 경험을 통해 사용자는 Azure Security Center에 WAC 서버를 등록 하 고 Windows 관리 센터 환경에서 직접 보안 경고 및 권장 사항을 볼 수 있습니다.


## <a name="september-2019"></a>2019년 9월

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>적응 응용 프로그램 제어 기능을 사용 하 여 규칙 관리 향상

적응 응용 프로그램 컨트롤을 사용 하 여 가상 컴퓨터에 대 한 규칙을 관리 하는 환경이 개선 되었습니다. Azure Security Center의 적응 응용 프로그램 컨트롤은 가상 머신에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 됩니다. 규칙 관리의 일반적인 향상 외에도 새로운 혜택을 통해 새 규칙을 추가할 때 보호할 파일 형식을 제어할 수 있습니다.

[적응 응용 프로그램 컨트롤에 대해 자세히 알아보세요](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Azure Policy를 사용 하 여 컨테이너 보안 권장 사항 제어

컨테이너 보안의 취약점을 해결 하는 Azure Security Center 권장 사항은 이제 Azure Policy를 통해 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

사용 하도록 설정 된 보안 정책을 보려면 Security Center에서 보안 정책 페이지를 엽니다.


## <a name="august-2019"></a>2019년 8월

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure 방화벽에 대 한 JIT (just-in-time) VM 액세스 

이제 Azure 방화벽에 대 한 JIT (just-in-time) VM 액세스를 일반적으로 사용할 수 있습니다. 이를 사용 하 여 NSG 보호 된 환경 외에도 Azure 방화벽 보호 된 환경을 보호 합니다.

JIT VM 액세스는 NSG 및 Azure 방화벽 규칙을 사용 하 여 필요한 경우에만 Vm에 대 한 제어 된 액세스를 제공 함으로써 네트워크 대규모 공격에 대 한 노출을 줄입니다.

Vm에 대해 JIT를 사용 하도록 설정 하는 경우 보호 되는 포트, 포트가 열려 있는 상태로 유지 되는 기간 및 이러한 포트에 액세스할 수 있는 승인 된 IP 주소를 결정 하는 정책을 만듭니다. 이 정책은 사용자가 액세스를 요청할 때 수행할 수 있는 작업을 제어 하는 데 도움이 됩니다.

요청은 Azure 활동 로그에 기록 되므로 액세스를 쉽게 모니터링 하 고 감사할 수 있습니다. Jit (just-in-time) 페이지를 사용 하면 jit를 사용 하는 기존 Vm과 JIT를 사용 하는 Vm을 빠르게 식별할 수 있습니다.

[Azure 방화벽에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>보안 상태를 높이려면 단일 클릭 재구성 (미리 보기)

보안 점수는 워크로드 보안에 대한 사용자의 대비 상태를 평가할 수 있는 도구입니다. 보안 권장 사항을 검토 하 고 우선 순위를 결정 하 여 먼저 수행할 권장 사항을 파악 합니다. 이를 통해 조사 우선 순위를 지정 하는 가장 심각한 보안 취약성을 찾을 수 있습니다.

보안 구성 오류를 간소화 하 고 보안 점수를 빠르게 개선 하기 위해 한 번의 클릭으로 대량의 리소스에 대 한 권장 사항을 수정할 수 있는 새로운 기능을 추가 했습니다.

이 작업을 통해 관리를 적용 하려는 리소스를 선택 하 고 사용자 대신 설정을 구성 하는 수정 작업을 시작할 수 있습니다.

[보안 권장 사항에 대 한 참조 가이드](recommendations-reference.md)에서 빠른 수정이 사용 하도록 설정 된 권장 사항을 확인 하세요.


### <a name="cross-tenant-management"></a>테넌트 간 관리

Security Center는 이제 Azure Lighthouse의 일부로 교차 테 넌 트 관리 시나리오를 지원 합니다. 이를 통해 Security Center에서 여러 테 넌 트의 보안 상태를 파악 하 고 관리할 수 있습니다. 

다중 [테 넌 트 관리 환경에 대해 자세히 알아보세요](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>2019년 7월

### <a name="updates-to-network-recommendations"></a>네트워크 권장 사항에 대 한 업데이트

ASC (Azure Security Center)는 새로운 네트워킹 권장 사항을 시작 하 고 기존 권장 사항을 개선 했습니다. 이제 Security Center를 사용 하 여 리소스에 대해 훨씬 더 많은 네트워킹 보호를 보장 합니다. 

[네트워크 권장 사항에 대해 자세히 알아보세요](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>2019년 6월

### <a name="adaptive-network-hardening---generally-available"></a>적응 네트워크 강화-일반적으로 사용 가능

공용 클라우드에서 실행 되는 워크 로드에 대 한 가장 큰 공격 노출 영역 중 하나는 공용 인터넷에 대 한 연결입니다. Microsoft 고객은 Azure 워크 로드를 필요한 원본 범위에만 사용할 수 있도록 하기 위해 어떤 네트워크 보안 그룹 (NSG) 규칙을 사용할 수 있는지 파악 하기 어렵습니다. 이 기능을 사용 하 Security Center는 Azure 워크 로드의 네트워크 트래픽 및 연결 패턴을 학습 하 고 인터넷 연결 가상 머신에 대 한 NSG 규칙 권장 사항을 제공 합니다. 이를 통해 고객은 네트워크 액세스 정책을 더 잘 구성 하 고 공격에 대 한 노출을 제한할 수 있습니다. 

[적응 네트워크 강화에 대해 자세히 알아보세요](security-center-adaptive-network-hardening.md).
