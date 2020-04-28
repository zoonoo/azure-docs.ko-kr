---
title: Azure 보안 벤치마크 청사진 샘플 제어
description: Azure Policy에 대한 Azure 보안 벤치마크 청사진 샘플의 제어 매핑입니다.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538665"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Azure 보안 벤치마크 청사진 샘플의 제어 매핑

다음 문서에서는 Azure Blueprints Azure 보안 벤치마크 청사진 샘플이 Azure 보안 벤치마크 제어에 매핑되는 방법에 대해 자세히 설명합니다. 제어에 대한 자세한 내용은 [Azure 보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview)를 참조하세요.

다음은 **Azure 보안 벤치마크** 제어에 대한 매핑입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 매핑된 제어는 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, **\[미리 보기\]: Azure 보안 벤치마크 추천 사항 감사 및 지원하는 특정 VM 확장 배포** 기본 제공 정책 이니셔티브를 찾아서 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../../../policy/overview.md) 정의와 연결되어 있습니다. 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../../../policy/how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수**는 정책 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않는 제어가 포함될 수 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 청사진 샘플에 대한 컨트롤과 Azure Policy 정의 간의 연결은 시간이 지남에 따라 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md)을 참조하세요.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Virtual Network에서 네트워크 보안 그룹 또는 Azure Firewall을 사용하여 리소스 보호

- 서브넷을 네트워크 보안 그룹과 연결해야 합니다.
- 적응형 네트워크 강화 추천 사항은 인터넷에 연결된 가상 머신에 적용해야 합니다.
- 가상 머신은 승인된 가상 네트워크에 연결되어야 합니다.
- 인터넷 연결 가상 머신은 네트워크 보안 그룹과 함께 보호되어야 합니다.
- Service Bus는 가상 네트워크 서비스 엔드포인트를 사용해야 함
- App Service는 가상 네트워크 서비스 엔드포인트를 사용해야 함
- SQL Server는 가상 네트워크 서비스 엔드포인트를 사용해야 함
- Event Hub는 가상 네트워크 서비스 엔드포인트를 사용해야 함
- Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함
- Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함
- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- 스토리지 계정은 가상 네트워크 서비스 엔드포인트를 사용해야 함
- Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함
- 가상 네트워크는 지정된 가상 네트워크 게이트웨이를 사용해야 함
- Kubernetes 서비스에 권한 있는 IP 범위를 정의해야 함
- \[미리 보기\]: 가상 머신에서 IP 전달을 사용하지 않도록 설정해야 함
- 인터넷 연결 가상 머신은 네트워크 보안 그룹과 함께 보호되어야 합니다.
- 가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.
- 가상 머신에서 관리 포트를 닫아야 합니다.

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Vnet, 서브넷 및 NIC의 구성과 트래픽에 대한 모니터링 및 기록

- Network Watcher를 사용하도록 설정해야 합니다.

## <a name="13-protect-critical-web-applications"></a>1.3 중요한 웹 애플리케이션 보호

- 웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.
- CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 합니다.
- CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용하지 않아야 합니다.
- CORS에서 모든 리소스가 API 앱에 액세스하도록 허용하지 않아야 합니다.
- 웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.
- 함수 앱에 대해 원격 디버깅을 해제해야 합니다.
- API Apps에 대해 원격 디버깅을 해제해야 합니다.

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 알려진 악성 IP 주소와의 통신 거부

- DDoS Protection 표준을 사용하도록 설정해야 합니다.
- 가상 머신에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.
- 적응형 네트워크 강화 추천 사항은 인터넷에 연결된 가상 머신에 적용해야 합니다.

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 네트워크 패킷 및 흐름 로그 기록

- Network Watcher를 사용하도록 설정해야 합니다.

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

- 필수 조건을 배포하여 '보안 옵션 - 네트워크 액세스'에서 Windows VM 구성 감사
- '보안 옵션 - Microsoft 네트워크 클라이언트'에서 Windows VM 구성의 감사 결과 표시
- 필수 조건을 배포하여 '보안 옵션 - 네트워크 보안'에서 Windows VM 구성 감사
- '보안 옵션 - 네트워크 보안'에서 Windows VM 구성의 감사 결과 표시
- 필수 조건을 배포하여 '보안 옵션 - Microsoft 네트워크 서버'에서 Windows VM 구성 감사
- '보안 옵션 - Microsoft 네트워크 서버'에서 Windows VM 구성의 감사 결과 표시
- 필수 조건을 배포하여 '관리 템플릿 - 네트워크'에서 Windows VM 구성 감사
- '관리 템플릿 - 네트워크'에서 Windows VM 구성의 감사 결과 표시

## <a name="22-configure-central-security-log-management"></a>2.2 중앙 보안 로그 관리 구성

- 가상 머신에 Log Analytics 에이전트를 설치해야 합니다.
- Virtual Machine Scale Sets에 Log Analytics 에이전트를 설치해야 합니다.
- 필수 조건을 배포하여 Log Analytics 에이전트가 예상대로 연결되지 않은 Windows VM 감사
- Log Analytics 에이전트가 예상대로 연결되지 않은 Windows VM의 감사 결과 표시
- Azure Monitor 로그 프로필은 'write'(쓰기), 'delete'(삭제) 및 'action'(작업) 범주에 대한 로그를 수집해야 합니다.
- Azure Monitor는 모든 지역의 활동 로그를 수집해야 합니다.
- 구독에서 Log Analytics 모니터링 에이전트의 자동 프로비저닝을 사용하도록 설정해야 합니다.

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Azure 리소스에 대한 감사 로깅 사용

- Azure Data Lake Store의 진단 로그를 사용하도록 설정해야 합니다.
- Logic Apps의 진단 로그를 사용하도록 설정해야 함
- IoT Hub의 진단 로그를 사용하도록 설정해야 합니다.
- 배치 계정의 진단 로그를 사용하도록 설정해야 합니다.
- Virtual Machine Scale Sets의 진단 로그를 사용하도록 설정해야 합니다.
- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.
- Search Service의 진단 로그를 사용하도록 설정해야 함
- App Services의 진단 로그를 사용하도록 설정해야 합니다.
- Data Lake Analytics의 진단 로그를 사용하도록 설정해야 합니다.
- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.
- Service Bus의 진단 로그를 사용하도록 설정해야 합니다.
- Azure Stream Analytics에서 진단 로그를 사용하도록 설정
- SQL Server에 대한 감사가 사용되도록 설정되어야 함
- 진단 설정 감사

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 운영 체제에서 보안 로그 수집

- 구독에서 Log Analytics 모니터링 에이전트의 자동 프로비저닝을 사용하도록 설정해야 합니다.
- 가상 머신에 Log Analytics 에이전트를 설치해야 합니다.
- Virtual Machine Scale Sets에 Log Analytics 에이전트를 설치해야 합니다.
- 필수 조건을 배포하여 Log Analytics 에이전트가 예상대로 연결되지 않은 Windows VM 감사
- Log Analytics 에이전트가 예상대로 연결되지 않은 Windows VM의 감사 결과 표시

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 비정상 활동에 대한 경고 사용

- Security Center 표준 가격 책정 계층을 선택해야 합니다.
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL Server의 Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.
- SQL 관리형 인스턴스의 Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.

## <a name="28-centralize-anti-malware-logging"></a>2.8 맬웨어 방지 로깅 중앙 집중화

- 보호 서명을 자동으로 업데이트하려면 Azure용 Microsoft Antimalware를 구성해야 합니다.
- Azure Security Center에서 누락된 Endpoint Protection 모니터링
- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 관리 계정 인벤토리 유지 관리

- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 전용 관리 계정 사용

- 관리자 그룹에 지정한 구성원만 포함되지 않은 Windows VM을 감사하기 위한 필수 조건 배포
- 관리자 그룹에 지정한 구성원만 포함되지 않은 Windows VM의 감사 결과 표시
- 지정한 멤버 중 일부가 관리자 그룹에 없는 Windows VM을 감사하기 위한 필수 조건을 배포합니다.
- 지정한 멤버 중 일부가 관리자 그룹에 없는 Windows VM의 감사 결과를 표시합니다.
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM을 감사하기 위한 필수 구성 요소 배포
- 관리자 그룹에 지정한 구성원이 포함된 Windows VM의 감사 결과 표시
- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 관리 계정의 의심스러운 활동에 대한 로그 및 경고

- Security Center 표준 가격 책정 계층을 선택해야 합니다.

## <a name="39-use-azure-active-directory"></a>3.9 Azure Active Directory 사용

- SQL Server에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.
- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.
- API 앱에서 [Azure Active Directory에 등록]을 사용하도록 설정되어 있는지 확인합니다.
- 웹앱에서 [Azure Active Directory에 등록]을 사용하도록 설정되어 있는지 확인합니다.
- 함수 앱에서 [Azure Active Directory에 등록]을 사용하도록 설정되어 있는지 확인합니다.

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 정기적으로 사용자 액세스 검토 및 조정

- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 중요한 정보의 인벤토리 유지 관리

- SQL 데이터베이스에서 중요한 데이터를 분류해야 합니다.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 전송 중인 모든 중요한 정보 암호화

- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.
- API 앱에서 최신 TLS 버전을 사용해야 합니다.
- 웹앱에서 최신 TLS 버전을 사용해야 합니다.
- 함수 앱에서 최신 TLS 버전을 사용해야 합니다.
- 함수 앱에 HTTPS를 통해서만 액세스 가능
- 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능
- API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.
- MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.
- PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.
- Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 활성 검색 도구를 사용하여 중요한 데이터 식별

- SQL 데이터베이스에서 중요한 데이터를 분류해야 합니다.
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 Advanced Data Security를 사용하도록 설정해야 합니다.

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Azure RBAC를 사용하여 리소스에 대한 액세스 제어

- Kubernetes 서비스에서 RBAC(역할 기반 액세스 제어)를 사용해야 합니다.
- 사용자 지정 RBAC 규칙의 사용 감사

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 중요한 저장 정보 암호화

- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.
- 가상 머신에서 디스크 암호화를 적용해야 합니다.
- 연결되지 않은 디스크는 암호화되어야 합니다.
- SQL Server TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.
- SQL 관리형 인스턴스 TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.
- 자동화 계정 변수를 암호화해야 합니다.
- Service Fabric 클러스터의 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 합니다.

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

- Azure Monitor는 모든 지역의 활동 로그를 수집해야 합니다.

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 자동화된 취약성 검사 도구 실행

- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.
- \[미리 보기\] Virtual Machines에서 취약성 평가를 사용하도록 설정해야 합니다.
- SQL Server에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 자동화된 운영 체제 패치 관리 솔루션 배포

- 시스템 업데이트를 머신에 설치해야 합니다.
- 가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.
- 함수 앱의 일부로 사용되는 경우 '.NET Framework' 버전이 최신 상태인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 '.NET Framework' 버전이 최신 상태인지 확인합니다.
- API 앱의 일부로 사용되는 경우 '.NET Framework' 버전이 최신 상태인지 확인합니다.

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

- API 앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.
- API 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.
- 웹앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.
- 함수 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.
- API 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.
- Kubernetes Services를 취약하지 않은 Kubernetes 버전으로 업그레이드해야 함

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.
- 머신 보안 구성의 취약성을 수정해야 합니다.
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- SQL 데이터베이스의 취약성을 수정해야 합니다.

## <a name="68-use-only-approved-applications"></a>6.8 승인된 애플리케이션만 사용

- Security Center 표준 가격 책정 계층을 선택해야 합니다.
- 가상 머신에서 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="69-use-only-approved-azure-services"></a>6.9 승인된 Azure 서비스만 사용

- 가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- 스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.

## <a name="610-implement-approved-application-list"></a>6.10 승인된 애플리케이션 목록 구현

- Security Center 표준 가격 책정 계층을 선택해야 합니다.
- 가상 머신에서 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 보안 Azure 리소스 구성 유지 관리

- \[미리 보기\]: Kubernetes 서비스에 Pod 보안 정책을 정의해야 합니다.

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 보안 운영 체제 구성 유지 관리

- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Azure 서비스에 대한 자동화된 구성 모니터링 구현

- \[미리 보기\]: Kubernetes 서비스에 Pod 보안 정책을 정의해야 합니다.

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 운영 체제에 대한 자동화된 구성 모니터링 구현

- 머신 보안 구성의 취약성을 수정해야 합니다.
- 컨테이너 보안 구성의 취약성을 수정해야 합니다.
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.

## <a name="711-manage-azure-secrets-securely"></a>7.11 안전하게 Azure 비밀 관리

- Key Vault 개체를 복구할 수 있어야 합니다.

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 안전하게 자동으로 ID 관리

- 함수 앱에서 관리 ID를 사용해야 합니다.
- 웹앱에서 관리 ID를 사용해야 합니다.
- API 앱에서 관리 ID를 사용해야 합니다.

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 중앙에서 관리하는 맬웨어 방지 소프트웨어 사용

- Azure Security Center에서 누락된 Endpoint Protection 모니터링
- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

- Security Center 표준 가격 책정 계층을 선택해야 합니다.

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

- 보호 서명을 자동으로 업데이트하려면 Azure용 Microsoft Antimalware를 구성해야 합니다.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 정기적으로 자동화된 백업 보장

- Azure SQL Database에 대해 장기 지역 중복 백업을 사용하도록 설정해야 함
- Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 전체 시스템 백업 수행 및 고객 관리형 키 백업

- Azure SQL Database에 대해 장기 지역 중복 백업을 사용하도록 설정해야 함
- Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 함
- Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 백업 및 고객 관리형 키 보호 보장

- Key Vault 개체를 복구할 수 있어야 합니다.

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

- Security Center 표준 가격 책정 계층을 선택해야 합니다.

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

- 구독에 대한 보안 연락처 이메일 주소를 제공해야 합니다.
- 구독에 대한 보안 연락처 전화 번호를 제공해야 합니다.
- SQL Server에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- SQL 관리형 인스턴스에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- SQL Server Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.
- SQL 관리형 인스턴스 Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure 보안 벤치마크 청사진의 제어 매핑을 검토했으므로 Azure Portal에서 Azure Policy를 방문하여 이니셔티브를 할당합니다.

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기