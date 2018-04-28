---
title: Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화
description: Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 10ed297180f68fcaf006f2778990879be02f994d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---fedramp-web-applications-automation"></a>Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화

## <a name="overview"></a>개요

[FedRAMP(Federal Risk and Authorization Management Program)](https://www.fedramp.gov)는 미국 정부 차원 프로그램으로 클라우드 제품 및 서비스에 대한 보안 평가, 권한 부여 및 연속 모니터링에 대한 표준화된 접근 방법을 제공합니다. 이 Azure Security 및 Compliance Blueprint Automation에서는 간단한 인터넷 연결 웹 응용 프로그램에 적합한 FedRAMP 규격 IaaS(Infrastructure as a Service) 환경의 배포를 위한 지침을 제공합니다. 이 솔루션은 공통 참조 아키텍처를 위한 Azure 리소스 배포 및 구성을 자동화하여 고객이 특정 보안 및 규정 준수 요구 사항에 부합할 수 있는 방법을 보여 주며 고객이 Azure에서 자체 솔루션을 구축 및 구성하기 위한 기초 역할을 합니다. 이 솔루션은 NIST SP 800-53을 기준으로 FedRAMP 높음 기준에서 제어의 하위 집합을 구현합니다. FedRAMP 높음 요구 사항 및 이 솔루션에 대한 자세한 내용은 [FedRAMP 높음 요구 사항 - 고급 개요](fedramp-controls-overview.md)를 참조하세요. ***참고: 이 솔루션은 Azure Government에 배포됩니다.***

이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반 형태로 제공되며 있는 그대로 프로덕션 환경에서 사용해서는 안 됩니다. 응용 프로그램을 수정 없이 이러한 환경에 배포하는 것은 FedRAMP 높음 기준의 요구 사항을 온전히 충족하기에는 부족합니다. 다음 사항에 유의하세요.
- 이 아키텍처는 고객이 FedRAMP 규격에 맞게 Azure를 사용하기 위한 기준을 제공합니다.
- 요구 사항은 각 고객의 구현에서 특정 상황에 따라 달라질 수 있으므로 이 아키텍처를 사용하여 구축된 솔루션의 적합한 보안 및 준수 평가의 수행은 고객의 책임입니다.  

이 솔루션의 작동 방식을 간략히 살펴보려면 배포에 대해 설명하고 보여 주는 이 [비디오](https://aka.ms/fedrampblueprintvideo)를 확인하세요.

배포 지침을 보려면 [여기](https://aka.ms/fedrampblueprintrepo)를 클릭하세요.

## <a name="solution-components"></a>솔루션 구성 요소

이 Azure Security 및 Compliance Blueprint Automation은 미리 구성된 보안 제어가 있는 IaaS 웹 응용 프로그램 참조 아키텍처를 자동으로 배포하여 고객이 FedRAMP 요구 사항을 준수할 수 있게 합니다. 이 솔루션은 리소스 배포 및 구성을 안내하는 Azure Resource Manager 템플릿과 PowerShell 스크립트로 구성되었습니다. NIST SP 800-53 보안 제어에 부합하는 Azure로부터의 보안 제어 상속과 배포된 리소스 및 구성을 나타내는 [준수 설명서](#compliance-documentation)가 함께 제공되므로 조직이 규정 준수 의무를 신속히 이행할 수 있습니다.

## <a name="architecture-diagram"></a>아키텍처 다이어그램

이 솔루션은 데이터베이스 백엔드가 있는 IaaS 웹 응용 프로그램에 대한 참조 아키텍처를 배포합니다. 이 아키텍처는 웹 계층, 데이터 계층, Active Directory 인프라, 응용 프로그램 게이트웨이 및 부하 분산 장치를 포함합니다. 웹 및 데이터 계층에 배포된 Virtual Machines는 가용성 집합에서 구성되며 SQL Server 인스턴스는 고가용성을 위해 AlwaysOn 가용성 그룹에 구성됩니다. Virtual Machines는 도메인에 조인되며 Active Directory 그룹 정책을 사용하여 운영 체제 수준에서 보안 및 규정 준수 구성을 적용합니다.  관리 점프박스(요새 호스트)는 관리자가 배포된 리소스에 액세스할 수 있게 보안 연결을 제공합니다.

![대체 텍스트](images/fedramp-architectural-diagram.png?raw=true "Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화")

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처의 세부 정보는 [배포 아키텍처](#deployment-architecture) 섹션에 있습니다.

* **Azure Virtual Machines**
    - (1) 관리/요새(Windows Server 2016 Datacenter)
    - (2) Active Directory 도메인 컨트롤러(Windows Server 2016 Datacenter)
    - (2) SQL Server 클러스터 노드(Windows Server 2012 R2의 SQL Server 2016)
    - (1) SQL Server 감시(Windows Server 2016 Datacenter)
    - (2) Web/IIS(Windows Server 2016 Datacenter)
* **가용성 집합**
    - (1) Active Directory 도메인 컨트롤러
    - (1) SQL 클러스터 노드 및 감시
    - (1) 웹/IIS
* **Azure Virtual Network**
    - (1) /16 가상 네트워크
    - (5) /24 서브넷
    - DNS 설정이 모든 도메인 컨트롤러에 설정됨
* **Azure 부하 분산 장치**
    - (1) SQL 부하 분산 장치
* **Azure Application Gateway**
    - (1) WAF 응용 프로그램 게이트웨이 사용
      - 방화벽 모드: 방지
      - 규칙 집합: OWASP 3.0
      - 수신기: 포트 443
* **Azure Storage**
    - (7) 지역 중복 저장소 계정
* **Azure Backup**
    - (1) Recovery Services 자격 증명 모음
* **Azure Key Vault**
    - (1) Key Vault
* **Azure Active Directory**
* **Azure 리소스 관리자**
* **Azure Log Analytics**
    - (1) Log Analytics 작업 영역
* **Azure Automation**
    - (1) Automation 계정

## <a name="deployment-architecture"></a>배포 아키텍처

다음 섹션에서는 개발 및 구현 요소를 자세히 설명합니다.

### <a name="network-segmentation-and-security"></a>네트워크 분할 및 보안

#### <a name="application-gateway"></a>Application Gateway

아키텍처는 WAF(웹 응용 프로그램 방화벽)과 OWASP 규칙 집합을 사용하는 Application Gateway를 통해 보안상 위험을 줄입니다. 추가적인 기능은 다음과 같습니다.

- [종단 간 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL 오프로드](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal) 사용
- [TLS v1.0 및 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) 사용 안 함
- [WAF(웹 응용 프로그램 방화벽)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) 모드
- [방지 모드](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal), OWASP 3.0 규칙 집합 사용

#### <a name="virtual-network"></a>가상 네트워크

아키텍처는 10.200.0.0/16 주소 공간으로 개인 가상 네트워크를 정의합니다.

#### <a name="network-security-groups"></a>네트워크 보안 그룹

이 솔루션은 가상 네트워크 내부에서 별도 웹 서브넷, 데이터베이스 서브넷, Active Directory 서브넷 및 서브넷 관리를 포함하는 아키텍처에 리소스를 배포합니다. 서브넷은 서브넷 간 트래픽을 시스템 및 관리 기능에 필요한 경우로만 제한하기 위해 개별 서브넷에 적용되는 네트워크 보안 그룹 규칙에 의해 논리적으로 구분됩니다.

이 솔루션으로 배포된 [네트워크 보안 그룹](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)에 대한 구성을 참조하세요. 조직에서는 [이 설명서](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)를 참조하여 위의 파일을 편집하여 네트워크 보안 그룹을 구성할 수 있습니다.

각 서브넷에는 전용 NSG(네트워크 보안 그룹)가 있습니다.
- 1 NSG - Application Gateway용(LBNSG)
- 1 NSG - 점프박스용(MGTNSG)
- 1 NSG - 기본 및 백업 도메인 컨트롤러용(ADNSG)
- 1 NSG - SQL Server 및 파일 공유 감시용(SQLNSG)
- 1 NSG - 웹 계층용(WEBNSG)

#### <a name="subnets"></a>서브넷

각 서브넷이 해당 NSG에 연결되어야 합니다.

### <a name="data-at-rest"></a>미사용 데이터

이 아키텍처는 여러 암호화 방법을 사용하여 미사용 데이터를 보호합니다.

#### <a name="azure-storage"></a>Azure Storage

미사용 데이터 암호화 요구 사항에 부합하기 위해 모든 저장소 계정은[Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)을 사용합니다.

#### <a name="sql-database"></a>SQL Database

SQL Database는 [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)를 사용하도록 구성됩니다. 이 기능은 데이터 및 로그 파일의 실시간 암호화 및 암호 해독을 수행하여 미사용 정보를 보호합니다. TDE는 저장된 데이터가 무단으로 액세스되지 못하게 합니다. 

#### <a name="azure-disk-encryption"></a>Azure 디스크 암호화

Azure Disk Encryption은 Windows IaaS 가상 머신 디스크를 암호화하는 데 사용됩니다. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Windows의 BitLocker 기능을 활용하여 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어 및 관리할 수 있도록 합니다.

### <a name="logging-and-auditing"></a>로깅 및 감사

[Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)는 시스템 및 사용자 활동과 시스템 상태에 대한 광범위 로깅을 제공합니다. 

- **활동 로그:** [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 자세한 정보를 제공합니다.
- **진단 로그:** [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 모든 리소스에서 내보낸 모든 로그입니다.   이러한 로그에는 Windows 이벤트 시스템 로그, Azure Storage 로그, Key Vault 감사 로그 및 Application Gateway 액세스 및 방화벽 로그가 포함됩니다.
- **로그 보관:** Azure 활동 로그 및 진단 로그는 처리, 저장 및 대시보드 작업을 위해 Azure Log Analytics에 연결할 수 있습니다. 보존은 조직 특정 보존 요구 사항에 맞게 최대 730일까지 사용자가 구성할 수 있습니다.

### <a name="secrets-management"></a>비밀 관리

이 솔루션에서는 Azure Key Vault를 사용하여 키와 비밀을 관리합니다.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)는 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. 
- 이 솔루션은 Azure Key Vault와 통합되어 IaaS 가상 머신 디스크 암호화 키와 비밀을 관리할 수 있도록 합니다.

### <a name="identity-management"></a>ID 관리

다음 기술은 Azure 환경에서 ID 관리 기능을 제공합니다.
- [Azure AD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다.
- 고객 배포 웹 응용 프로그램에 대한 인증은 Azure AD를 사용하여 수행할 수 있습니다. 자세한 내용은 [Azure Active Directory와 응용 프로그램 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요.  
- [Azure RBAC(Role-based Access Control)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)은 Azure에 대해 세밀하게 초점을 맞춘 액세스 관리를 구현합니다. 구독 액세스는 구독 관리자로 제한되며 리소스 액세스는 사용자 역할에 따라 제한될 수 있습니다.
- 배포된 IaaS Active Directory 인스턴스는 배포된 IaaS 가상 머신에 대한 OS 수준의 ID 관리를 제공합니다.
   
### <a name="compute-resources"></a>Compute 리소스

#### <a name="web-tier"></a>웹 계층

이 솔루션은 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)에 웹 계층 가상 머신을 배포합니다. 가용성 집합을 사용하면 가상 머신을 격리된 여러 하드웨어 클러스터에 분산하여 가용성을 높일 수 있습니다.

#### <a name="database-tier"></a>데이터베이스 계층

이 솔루션은 가용성 집합에서 데이터베이스 계층 가상 머신을 [AlwaysOn 가용성 그룹](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)으로 배포합니다. Always On 가용성 그룹 기능은 고가용성 및 재해 복구 기능을 제공합니다. 

#### <a name="active-directory"></a>Active Directory

솔루션이 배포한 모든 가상 머신은 도메인에 조인되며 Active Directory 그룹 정책을 사용하여 운영 체제 수준에서 보안 및 규정 준수 구성을 적용합니다.  Active Directory 가상 머신은 가용성 집합에 배포됩니다.


#### <a name="jumpbox-bastion-host"></a>점프박스(요새 호스트)

관리 점프박스(요새 호스트)는 관리자가 배포된 리소스에 액세스할 수 있게 보안 연결을 제공합니다. 점프박스 가상 머신이 위치한 관리 서브넷에 연결된 NSG는 RDP에 대해 TCP 포트 3389에서만 연결을 허용합니다. 

### <a name="malware-protection"></a>맬웨어 방지

Virtual Machine용 [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)는 보호되는 Virtual Machines에서 악성 또는 원치 않는 소프트웨어가 사용자 시스템에서 스스로의 설치나 실행을 시도할 때 구성 가능한 알림을 통해 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 실시간 보호 기능을 제공합니다.

### <a name="patch-management"></a>패치 관리

이 Azure Security 및 Compliance Blueprint Automation에서 배포한 Windows 가상 머신은 기본적으로 Windows 업데이트 서비스에서 자동 업데이트를 받도록 구성됩니다. 이 솔루션은 필요할 때 Windows 서버에 패치를 배포하기 위해 배포 업데이트를 만들 수 있는 Azure Automation 솔루션도 배포합니다.

### <a name="operations-management"></a>운영 관리

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/)는 Azure 및 온-프레미스 환경의 리소스로 생성된 데이터를 수집 및 분석할 수 있게 하는 서비스입니다.

#### <a name="management-solutions"></a>관리 솔루션

다음 관리 솔루션은 이 솔루션의 일부로 미리 설치됩니다.
- [AD 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [맬웨어 방지 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [보안 및 감사](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [SQL 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [업데이트 관리](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [에이전트 상태](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Azure Activity Logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [변경 내용 추적](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>규정 준수 설명서

### <a name="customer-responsibility-matrix"></a>고객 책임 매트릭스

[고객 책임 매트릭스](https://aka.ms/blueprinthighcrm)(Excel 통합 문서)는 FedRAMP 높음 기준에서 요구하는 모든 보안 제어를 나열합니다. 이 매트릭스에서는 각 제어(또는 제어 하위 요소)에 대해 제어의 구현 책임의 소재가 Microsoft, 고객 또는 공동인지 표시합니다. 

### <a name="control-implementation-matrix"></a>제어 구현 매트릭스

[제어 구현 매트릭스](https://aka.ms/blueprintwacim)(Excel 통합 문서)는 FedRAMP 높음 기준에서 요구하는 모든 보안 제어를 나열합니다. 이 매트릭스에서는 고객 책임 매트릭스에서 고객 책임으로 지정된 각 제어(또는 제어 하위 요소)에 대해 Blueprint Automation이 제어를 구현하는 경우 1), 구현이 제어 요구 사항에 어떻게 부합하는지에 대한 설명에 2)를 표시합니다. 이 콘텐츠는 [여기](fedramp-controls-overview.md)에서 사용할 수 있습니다.

## <a name="deploy-the-solution"></a>솔루션 배포

이 Azure Security 및 Compliance Blueprint Automation은 Azure Resource Manager의 API 서비스에서 Azure 내에 리소스를 배포하기 위해 처리하는 JSON 구성 파일과 PowerShell 스크립트로 구성됩니다. 자세한 배포 지침은 [여기](https://aka.ms/fedrampblueprintrepo)에 있습니다. ***참고: 이 솔루션은 Azure Government에 배포됩니다.***

#### <a name="quickstart"></a>빠른 시작
1. [이](https://aka.ms/fedrampblueprintrepo) GitHub 리포지토리를 로컬 워크스테이션에 복제하거나 다운로드합니다.

2. 미리 배포된 PowerShell 스크립트 azure-blueprint/predeploy/Orchestration_InitialSetup.ps1을 실행합니다.

3. 아래 단추를 클릭하고 Azure Portal에 로그인한 다음 필요한 ARM 템플릿 매개 변수를 입력하고 **구매**를 클릭합니다.

    [![Azure에 배포](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>고지 사항

- 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.  
- 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.  
- 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.  
- 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.  
- 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
- 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
