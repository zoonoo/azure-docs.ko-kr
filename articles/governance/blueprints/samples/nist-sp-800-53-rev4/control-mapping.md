---
title: 샘플 - NIST SP 800-53 R4 청사진 - 컨트롤 매핑
description: NIST SP 800-53 R4 청사진 샘플을 Azure Policy 및 RBAC로 컨트롤 매핑합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b887c4e6812d201dc83465a578f71e1742e8e9cf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343110"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>NIST SP 800-53 R4 청사진 샘플의 컨트롤 매핑

다음 문서에서는 Azure Blueprints NIST SP NIST SP 800-53 R4 청사진 샘플이 NIST SP 800-53 R4 컨트롤에 매핑되는 방법을 자세히 설명합니다. 컨트롤에 대한 자세한 내용은 [NIST SP 800-53](https://nvd.nist.gov/800-53)을 참조하세요.

다음은 **NIST SP 800-53(개정 4)** 컨트롤에 대한 매핑입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, **[미리 보기]: NIST SP 800-53 R4 컨트롤 감사 및 감사 요구 사항을 지원하기 위한 특정 VM 확장 배포** 기본 제공 정책 이니셔티브를 찾아서 선택합니다.

## <a name="ac-2-account-management"></a>AC-2 계정 관리

이 청사진은 조직의 계정 관리 요구 사항을 준수하지 않는 계정을 검토하는 데 도움이 됩니다. 이 청사진은 구독 및 사용되지 않는 계정에 대한 읽기, 쓰기 및 소유자 권한을 사용하여 외부 계정을 감사하는 5가지 Azure Policy 정의를 할당합니다. 이러한 정책에 의해 감사된 계정을 검토하면 계정 관리 요구 사항이 충족되도록 적절한 조치를 취할 수 있습니다.

- [미리 보기]: Audit deprecated accounts on a subscription
- [미리 보기]: Audit deprecated accounts with owner permissions on a subscription
- [미리 보기]: Audit external accounts with owner permissions on a subscription
- [미리 보기]: Audit external accounts with read permissions on a subscription
- [미리 보기]: Audit external accounts with write permissions on a subscription

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) 계정 관리 | 역할 기반 체계

Azure는 Azure의 리소스에 대한 액세스 권한이 있는 사용자를 관리할 수 있도록 [RBAC(역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 또한 이 청사진은 SQL Servers 및 Service Fabric에 대한 Azure Active Directory 인증 사용을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당합니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다.

- SQL Server에 대한 Azure Active Directory 관리자의 프로비전 감사
- Service Fabric의 클라이언트 인증에 대한 Azure Active Directory의 사용 감사

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) 계정 관리 | 계정 모니터링/비정상적 사용

JIT(Just-In-Time) 가상 머신 액세스를 사용하면 Azure 가상 머신으로의 인바운드 트래픽이 잠기기 때문에, 필요할 때 VM에 쉽게 연결할 수 있으면서도 공격 노출을 줄일 수 있습니다. JIT의 가상 머신 액세스 요청이 모두 활동 로그에 기록되므로 비정상적인 사용을 모니터링할 수 있습니다. 이 청사진은 JIT 액세스를 지원할 수 있지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- [미리 보기]: Azure Security Center에서 가능한 네트워크 JIT(Just-In-time) 액세스 모니터링

## <a name="ac-4-information-flow-enforcement"></a>AC-4 정보 흐름 적용

CORS(교차 원본 리소스 공유)를 사용하면 App Services 리소스를 외부 도메인에서 요청할 수 있습니다. 필요한 도메인만 API, 함수 및 웹 애플리케이션과 상호 작용할 수 있도록 허용하는 것이 좋습니다. 이 청사진은 Azure Security Center에서 CORS 리소스 액세스 제한을 모니터링할 수 있는 [Azure Policy](../../../policy/overview.md) 정책을 할당합니다.
CORS 구현을 이해하면 정보 흐름 제어가 구현되었는지 확인하는 데 도움이 됩니다.

- [미리 보기]: Audit CORS resource access restrictions for a Web Application

## <a name="ac-5-separation-of-duties"></a>AC-5 의무 분리

Azure 구독 소유자가 한 명만 있으면 관리 중복이 허용되지 않습니다. 반대로, Azure 구독 소유자가 너무 많으면 손상된 소유자 계정을 통한 위반 가능성이 증가할 수 있습니다. 이 청사진을 사용하면 Azure 구독에 대한 소유자 수를 감사하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당하여 Azure 구독 소유자 수를 적절하게 유지할 수 있습니다. 또한 이 청사진은 Windows 가상 머신의 Administrators 그룹 구성원을 제어할 수 있는 Azure Policy 정의를 할당합니다. 구독 소유자 및 가상 머신 관리자 권한을 관리하면 적절한 임무 분리를 구현하는 데 도움이 될 수 있습니다.

- [미리 보기]: Audit maximum number of owners for a subscription
- [미리 보기]: Audit minimum number of owners for subscription
- Windows VM 내부의 Administrators 그룹이 지정된 구성원을 제외하는지 감사
- Windows VM 내부의 Administrators 그룹이 지정된 구성원을 포함하는지 감사
- VM 확장을 배포하여 Windows VM 내부의 Administrators 그룹이 지정된 구성원을 제외하는지 감사
- VM 확장을 배포하여 Windows VM 내부의 Administrators 그룹이 지정된 구성원을 포함하는지 감사

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) 최소 권한 | 사용자 권한 검토

Azure는 Azure의 리소스에 대한 액세스 권한이 있는 사용자를 관리할 수 있도록 [RBAC(역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 이 청사진은 우선적으로 검토해야 하는 계정을 감사하는 6개 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이러한 계정 지표는 최소 권한 컨트롤이 구현되었는지 확인하는 데 도움이 될 수 있습니다.

- [미리 보기]: Audit maximum number of owners for a subscription
- [미리 보기]: Audit minimum number of owners for subscription
- Windows VM 내부의 Administrators 그룹이 지정된 구성원을 제외하는지 감사
- Windows VM 내부의 Administrators 그룹이 지정된 구성원을 포함하는지 감사
- VM 확장을 배포하여 Windows VM 내부의 Administrators 그룹이 지정된 구성원을 제외하는지 감사
- VM 확장을 배포하여 Windows VM 내부의 Administrators 그룹이 지정된 구성원을 포함하는지 감사

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) 원격 액세스 | 자동화된 모니터링/제어

이 청사진은 Azure App Service 애플리케이션의 원격 디버깅이 꺼졌는지 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 3개와 암호 없이 계정에서 원격 연결을 허용하는 Linux 가상 머신을 감사하는 정책 2개를 할당하여 원격 액세스의 모니터링 및 제어를 도와줍니다. 또한 이 청사진은 스토리지 계정에 제한 없이 액세스할 수 있는 Azure Policy 정의를 할당합니다. 이러한 지표를 모니터링하면 원격 액세스 방법이 보안 정책을 준수하는 데 도움이 될 수 있습니다.

- [미리 보기]: Audit remote debugging state for a Function App
- [미리 보기]: Audit remote debugging state for a Web Application
- [미리 보기]: Audit remote debugging state for an API App
- [미리 보기]: Audit that Linux VMs do not allow remote connections from accounts without passwords
- [미리 보기]: Deploy VM extension to audit that Linux VMs do not allow remote connections from accounts without passwords
- 스토리지 계정에 대한 무제한 네트워크 액세스 감사

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) 감사 레코드의 콘텐츠 | 계획된 감사 레코드 콘텐츠의 중앙 집중식 관리

Azure Monitor를 통해 수집되는 로그 데이터는 Log Analytics 작업 영역에 저장되므로 중앙 집중식 구성 및 관리가 가능합니다. 이 청사진은 이벤트가 기록되도록 Azure 가상 머신에 Log Analytics 에이전트를 배포하고 감사하는 [Azure Policy](../../../policy/overview.md) 정의 7개를 할당합니다.

- [미리 보기]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- [미리 보기]: VMSS의 Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- [미리 보기]: Audit Log Analytics Workspace for VM - Report Mismatch
- [미리 보기]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [미리 보기]: Deploy Log Analytics Agent for Linux VMs
- [미리 보기]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [미리 보기]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 감사 처리 실패에 대한 응답

이 청사진은 감사 및 이벤트 로깅 구성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 5개를 할당합니다. 이러한 구성을 모니터링하면 감사 시스템 오류 또는 구성 오류 지표를 제공하고 정정 작업을 수행하는 데 도움이 될 수 있습니다.

- [미리 보기]: Monitor unaudited SQL servers in Azure Security Center
- 진단 설정 감사
- Advanced Data Security 없이 SQL 관리형 인스턴스 감사
- SQL 감사 서버 수준 감사 설정
- Advanced Data Security 없이 SQL 서버 감사

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) 감사 검토, 분석 및 보고 | 중앙 검토 및 분석

Azure Monitor를 통해 수집되는 로그 데이터는 Log Analytics 작업 영역에 저장되므로 중앙 집중식 보고 및 분석이 가능합니다. 이 청사진은 이벤트가 기록되도록 Azure 가상 머신에 Log Analytics 에이전트를 배포하고 감사하는 [Azure Policy](../../../policy/overview.md) 정의 7개를 할당합니다.

- [미리 보기]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- [미리 보기]: VMSS의 Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- [미리 보기]: Audit Log Analytics Workspace for VM - Report Mismatch
- [미리 보기]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [미리 보기]: Deploy Log Analytics Agent for Linux VMs
- [미리 보기]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [미리 보기]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-12-audit-generation"></a>AU-12 감사 생성

이 청사진은 Azure 리소스에 대한 로그 설정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 15개를 할당하여 시스템 이벤트가 기록되도록 보장합니다. 이러한 정책 정의는 Azure 가상 머신에 대한 Log Analytics 에이전트의 배포와 다른 Azure 리소스 종류의 감사 설정 구성을 감사하고 적용합니다. 또한 이러한 정책은 진단 로그 구성을 감사하여 Azure 리소스 내에서 수행되는 작업에 대한 인사이트를 제공합니다. 뿐만 아니라 SQL 서버에 감사 및 Advanced Data Security가 구성됩니다.

- [미리 보기]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- [미리 보기]: VMSS의 Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- [미리 보기]: Audit Log Analytics Workspace for VM - Report Mismatch
- [미리 보기]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [미리 보기]: Deploy Log Analytics Agent for Linux VMs
- [미리 보기]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [미리 보기]: Deploy Log Analytics Agent for Windows VMs
- [미리 보기]: Monitor unaudited SQL servers in Azure Security Center
- 네트워크 보안 그룹에 대한 진단 설정 적용
- 진단 설정 감사
- Advanced Data Security 없이 SQL 관리형 인스턴스 감사
- SQL 감사 서버 수준 감사 설정
- Advanced Data Security 없이 SQL 서버 감사
- SQL 서버에 Advanced Data Security 배포
- SQL Server에 감사 배포

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) 최소한의 기능 | 프로그램 실행 금지

Azure Security Center의 적응형 애플리케이션 제어는 특정 소프트웨어가 가상 머신에서 실행되지 않도록 차단하는 자동화된 인텔리전트 엔드투엔드 애플리케이션 허용 목록 관리 솔루션입니다. 애플리케이션 제어는 승인되지 않은 애플리케이션의 실행을 금지하는 적용 모드에서 실행할 수 있습니다. 이 청사진은 애플리케이션 허용 목록을 사용하는 것이 좋지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 Azure Policy 정의를 할당합니다.

- [미리 보기]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) 최소한의 기능 | 권한이 부여된 소프트웨어/허용 목록

Azure Security Center의 적응형 애플리케이션 제어는 특정 소프트웨어가 가상 머신에서 실행되지 않도록 차단하는 자동화된 인텔리전트 엔드투엔드 애플리케이션 허용 목록 관리 솔루션입니다. 애플리케이션 제어를 사용하면 가상 머신에 사용하도록 승인인 애플리케이션 목록을 만들 수 있습니다. 이 청사진은 애플리케이션 허용 목록을 사용하는 것이 좋지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- [미리 보기]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-11-user-installed-software"></a>CM-11 사용자가 설치한 소프트웨어

Azure Security Center의 적응형 애플리케이션 제어는 특정 소프트웨어가 가상 머신에서 실행되지 않도록 차단하는 자동화된 인텔리전트 엔드투엔드 애플리케이션 허용 목록 관리 솔루션입니다. 애플리케이션 제어를 사용하면 소프트웨어 제한 정책을 적용하고 규정 준수 여부를 모니터링할 수 있습니다. 이 청사진은 애플리케이션 허용 목록을 사용하는 것이 좋지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- [미리 보기]: Monitor possible app whitelisting in Azure Security Center

## <a name="cp-7-alternate-processing-site"></a>CP-7 대체 처리 사이트

Azure Site Recovery는 가상 머신에서 실행되는 워크로드를 기본 위치에서 보조 위치로 복제합니다. 기본 사이트가 중단되는 경우 워크로드가 보조 위치로 장애 조치(failover)됩니다. 이 청사진은 재해 복구를 구성하지 않고 가상 머신을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 이 지표를 모니터링하면 필요한 비상 제어를 준비할 수 있습니다.

- 재해 복구를 구성하지 않고 가상 머신 감사

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) 식별 및 인증(조직 사용자) | 네트워크를 통해 권한 있는 계정에 액세스

이 청사진은 소유자 및/또는 쓰기 권한이 있지만 다단계 인증을 사용하지 않는 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당하여 권한 있는 액세스 권한을 제한 및 제어할 수 있습니다. 다단계 인증은 인증 정보 중 한 부분이 손상되더라도 계정을 안전하게 유지하는 데 도움이 됩니다. 다단계 인증을 사용하지 않고 계정을 모니터링하면 손상될 가능성이 더 높은 계정을 식별할 수 있습니다.

- [미리 보기]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [미리 보기]: Audit accounts with write permissions who are not MFA enabled on a subscription

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) 식별 및 인증(조직 사용자) | 권한 없는 계정에 대한 네트워크 액세스

이 청사진은 읽기 권한이 있지만 다단계 인증을 사용하지 않는 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 액세스 권한을 제한 및 제어할 수 있습니다. 다단계 인증은 인증 정보 중 한 부분이 손상되더라도 계정을 안전하게 유지하는 데 도움이 됩니다. 다단계 인증을 사용하지 않고 계정을 모니터링하면 손상될 가능성이 더 높은 계정을 식별할 수 있습니다.

- [미리 보기]: Audit accounts with read permissions who are not MFA enabled on a subscription

## <a name="ia-5-authenticator-management"></a>IA-5 인증자 관리

이 청사진은 암호 없이 계정에서 원격으로 연결하는 것을 허용하고/허용하거나 암호 파일에 대한 권한이 잘못 설정된 Linux 가상 머신을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 5개를 할당합니다. 또한 이 청사진은 Windows 가상 머신에 대한 암호 암호화 사용 유형을 감사하는 정책 정의를 할당합니다. 이러한 지표를 모니터링하면 시스템 인증자가 조직의 식별 및 인증 정책을 준수하는 데 도움이 됩니다.

- [미리 보기]: Audit that Linux VMs do not have accounts without passwords
- [미리 보기]: Deploy VM extension to audit that Linux VMs do not have accounts without passwords
- [미리 보기]: Audit that Linux VMs have the passwd file permissions set to 0644
- [미리 보기]: Audit that Windows VMs store passwords using reversible encryption
- [미리 보기]: Deploy VM extension to audit that Linux VMs have the passwd file permissions set to 0644

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) 인증자 관리 | 암호 기반 인증

이 청사진은 최소 강도 및 기타 암호 요구 사항을 적용하지 않는 Windows 가상 머신을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 12개를 할당하여 강력한 암호를 적용합니다. 암호 강도 정책을 위반하는 가상 머신을 파악할 수 있으면 모든 가상 머신 사용자 계정의 암호가 조직의 암호 정책을 준수하도록 정정 작업을 수행하는 데 도움이 됩니다.

- [미리 보기]: Audit that Windows VMs cannot re-use the previous 24 passwords
- [미리 보기]: Audit that Windows VMs have a maximum password age of 70 days
- [미리 보기]: Audit that Windows VMs have a minimum password age of 1 day
- [미리 보기]: Audit that Windows VMs have the password complexity setting enabled
- [미리 보기]: Audit that Windows VMs restrict the minimum password length to 14 characters
- [미리 보기]: Audit that Windows VMs store passwords using reversible encryption
- [미리 보기]: Deploy VM extension to audit that Windows VMs cannot re-use the previous 24 passwords
- [미리 보기]: Deploy VM extension to audit that Windows VMs have a maximum password age of 70 days
- [미리 보기]: Deploy VM extension to audit that Windows VMs have a minimum password age of 1 day
- [미리 보기]: Deploy VM extension to audit that Windows VMs have the password complexity setting enabled
- [미리 보기]: Deploy VM extension to audit that Windows VMs restrict the minimum password length to 14 characters
- [미리 보기]: Deploy VM extension to audit that Windows VMs store passwords using reversible encryption

## <a name="ra-5-vulnerability-scanning"></a>RA-5 취약성 검색

이 청사진은 Azure Security Center의 운영 체제 취약성, SQL 취약성 및 가상 머신 취약성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 4개를 할당하여 정보 시스템 취약성 관리를 도와줍니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 가질 수 있도록 하는 보고 기능을 제공합니다. 또한 이 청사진은 SQL 서버에 Advanced Data Security를 적용하고 감사하는 정책 정의 3개를 할당합니다. Advanced Data Security는 배포된 리소스의 취약성을 이해할 수 있는 취약성 평가 및 고급 위협 보호 기능이 포함되어 있습니다.

- Advanced Data Security 없이 SQL 관리형 인스턴스 감사
- Advanced Data Security 없이 SQL 서버 감사
- SQL 서버에 Advanced Data Security 배포
- [미리 보기]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [미리 보기]: Monitor OS vulnerabilities in Azure Security Center
- [미리 보기]: Monitor SQL vulnerability assessment results in Azure Security Center
- [미리 보기]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="sc-5-denial-of-service-protection"></a>SC-5 서비스 거부 방지

Azure의 DDoS(분선 서비스 거부) 표준 계층은 기본 서비스 계층보다 강력한 추가 기능 및 위험 완화 기능을 제공합니다. 이러한 추가 기능으로는 Azure Monitor 통합과 공격 후 완화 보고서를 검토하는 기능이 포함됩니다. 이 청사진은 DDoS 표준 계층을 사용하는지 감사하는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다. 서비스 계층 간의 기능 차이를 이해하면 해당 Azure 환경의 서비스 거부를 방지하는 최적의 솔루션을 선택할 수 있습니다.

- [미리 보기]: Audit standard tier of DDoS protection is enabled for a virtual network

## <a name="sc-7-boundary-protection"></a>SC-7 경계 보호

이 청사진은 관대한 규칙을 통해 네트워크 보안 그룹을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 시스템 경계를 관리하고 제어할 수 있습니다. 너무 관대한 규칙은 원치 않는 네트워크 액세스를 허용할 수 있으며, 검토를 거쳐야 합니다. 또한 이 청사진은 Azure Security Center의 네트워크 보안 그룹 강화 추천 사항을 모니터링하는 정책 정의를 할당합니다. Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하여 공격 노출 영역을 줄일 수 있는 네트워크 보안 그룹 규칙 추천 사항을 제공합니다.
뿐만 아니라 이 청사진은 보호되지 않는 엔드포인트, 애플리케이션 및 스토리지 계정을 모니터링하는 정책 정의 3개를 할당합니다. 방화벽으로 보호되지 않는 엔드포인트 및 애플리케이션과 액세스가 제한되지 않는 스토리지 계정은 정보 시스템 내에 포함된 정보에 대한 원치 않는 액세스를 허용할 수 있습니다.

- [미리 보기]: Monitor Internet-facing virtual machines for Network Security Group traffic hardening recommendations
- [미리 보기]: Monitor permissive network access in Azure Security Center
- [미리 보기]: Monitor unprotected network endpoints in Azure Security Center
- [미리 보기]: Monitor unprotected web application in Azure Security Center
- 스토리지 계정에 대한 무제한 네트워크 액세스 감사

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) 경계 보호 | 액세스 지점

JIT(Just-In-Time) 가상 머신 액세스를 사용하면 Azure 가상 머신으로의 인바운드 트래픽이 잠기기 때문에, 필요할 때 VM에 쉽게 연결할 수 있으면서도 공격 노출을 줄일 수 있습니다. JIT 가상 머신 액세스를 사용하면 Azure 리소스에 대한 외부 연결 수를 제한할 수 있습니다. 이 청사진은 JIT 액세스를 지원할 수 있지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- [미리 보기]: Azure Security Center에서 가능한 네트워크 JIT(Just-In-time) 액세스 모니터링

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) 경계 보호 | 외부 원격 통신 서비스

JIT(Just-In-Time) 가상 머신 액세스를 사용하면 Azure 가상 머신으로의 인바운드 트래픽이 잠기기 때문에, 필요할 때 VM에 쉽게 연결할 수 있으면서도 공격 노출을 줄일 수 있습니다. JIT 가상 머신 액세스를 사용하면 액세스 요청 및 승인 프로세스를 촉진하여 트래픽 흐름 정책 예외를 관리할 수 있습니다. 이 청사진은 JIT 액세스를 지원할 수 있지만 아직 구성하지 않은 가상 머신을 모니터링하도록 도와주는 [Azure Policy](../../../policy/overview.md) 정의를 할당합니다.

- [미리 보기]: Azure Security Center에서 가능한 네트워크 JIT(Just-In-time) 액세스 모니터링

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) 저장 정보 보호 | 암호화 보호

이 청사진은 특정 암호화 컨트롤을 적용하고 취약한 암호 설정의 사용을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 9개를 할당하여 저장 정보를 보호하도록 암호화 컨트롤 사용 정책을 적용하는 데 도움이 됩니다. Azure 리소스의 암호화 구성이 최적이 아닐 수 있는 경우를 이해하면 리소스가 정보 보안 정책에 따라 구성되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다. 특히 이 청사진이 할당하는 정책 정의는 데이터 레이크 스토리지 계정을 암호화하고, SQL 데이터베이스의 데이터를 투명하게 암호화하고, SQL 데이터베이스, 가상 머신 디스크 및 자동화 계정 변수에서 누락된 암호화를 감사할 것을 요구합니다.

- [미리 보기]: Monitor unencrypted SQL databases in Azure Security Center
- [미리 보기]: Monitor unencrypted VM Disks in Azure Security Center
- 스토리지 계정에 대한 보안 전송 감사
- Advanced Data Security 없이 SQL 관리형 인스턴스 감사
- Advanced Data Security 없이 SQL 서버 감사
- 투명한 데이터 암호화 상태 감사
- SQL 서버에 Advanced Data Security 배포
- SQL DB 투명한 데이터 암호화 배포
- Data Lake Store 계정에 암호화 적용

## <a name="si-2-flaw-remediation"></a>SI-2 결함 수정

이 청사진은 Azure Security Center에서 누락된 시스템 업데이트, 운영 체제 취약성, SQL 취약성 및 가상 머신 취약성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 6개를 할당하여 정보 시스템 결점 관리를 도와줍니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 가질 수 있도록 하는 보고 기능을 제공합니다. 또한 이 청사진은 가상 머신 확장 집합의 운영 체제 자동 업그레이드를 보장하는 정책 정의를 할당합니다.

- [미리 보기]: Audit any missing system updates on virtual machine scale sets in Azure Security Center
- [미리 보기]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [미리 보기]: Monitor missing system updates in Azure Security Center
- [미리 보기]: Monitor OS vulnerabilities in Azure Security Center
- [미리 보기]: Monitor SQL vulnerability assessment results in Azure Security Center
- [미리 보기]: Monitor VM Vulnerabilities in Azure Security Center
- VMSS에서 앱 상태 검사를 사용하여 자동 OS 업그레이드 적용

## <a name="si-3-malicious-code-protection"></a>SI-3 악성 코드 방지

이 청사진은 Azure Security Center에서 가상 머신의 엔드포인트 보호 누락을 모니터링하고 Windows 가상 머신에 Microsoft 맬웨어 방지 솔루션을 적용하는 [Azure Policy](../../../policy/overview.md) 정의 3개를 할당하여 악성 코드 방지를 비롯한 엔드포인트 보호 관리를 도와줍니다.

- [미리 보기]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [미리 보기]: Monitor missing Endpoint Protection in Azure Security Center
- Windows Server용 기본 Microsoft IaaSAntimalware 확장 배포

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) 악성 코드 방지 | 중앙 관리

이 청사진은 Azure Security Center에서 가상 머신의 엔드포인트 보호 누락을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당하여 악성 코드 방지를 비롯한 엔드포인트 보호 관리를 도와줍니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 얻을 수 있는 중앙 집중식 관리 및 보고 기능을 제공합니다.

- [미리 보기]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [미리 보기]: Monitor missing Endpoint Protection in Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI-4 정보 시스템 모니터링

이 청사진은 Azure 리소스에 로깅 및 데이터 보안을 적용하고 감사하여 시스템 모니터링을 도와줍니다. 특히, 이 정책은 Log Analytics 에이전트 배포를 감사 및 적용하고 SQL 데이터베이스, 스토리지 계정 및 네트워크 리소스의 보안 설정을 강화합니다. 이러한 기능은 비정상적인 동작과 공격 징후를 감지하여 적절한 조치를 취하도록 도와줍니다.

- [미리 보기]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- [미리 보기]: VMSS의 Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)
- [미리 보기]: Audit Log Analytics Workspace for VM - Report Mismatch
- [미리 보기]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [미리 보기]: Deploy Log Analytics Agent for Linux VMs
- [미리 보기]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [미리 보기]: Deploy Log Analytics Agent for Windows VMs
- Advanced Data Security 없이 SQL 관리형 인스턴스 감사
- Advanced Data Security 없이 SQL 서버 감사
- SQL 서버에 Advanced Data Security 배포
- 스토리지 계정에 Advanced Threat Protection 배포
- SQL Server에 감사 배포
- 가상 네트워크를 만들 때 Network Watcher 배포
- SQL 서버에 위협 탐지 배포

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) 정보 시스템 모니터링 | 트래픽 분석 / 비밀 반출

Azure Storage에 사용되는 Advanced Threat Protection은 스토리지 계정에 대한 비정상적이고 피해를 줄 수 있는 액세스 또는 악용 시도를 탐지합니다. 보호 경고 대상으로는 비정상적인 액세스 패턴, 비정상적인 추출/업로드 및 의심스러운 스토리지 작업이 포함됩니다. 이러한 지표는 정보를 몰래 반출하는 것을 탐지할 수 있습니다.

- 스토리지 계정에 Advanced Threat Protection 배포

## <a name="next-steps"></a>다음 단계

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기