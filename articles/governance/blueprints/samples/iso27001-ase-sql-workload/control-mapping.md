---
title: 샘플 - ISO 27001 ASE/SQL 워크로드 청사진 - 컨트롤 매핑
description: Azure Policy 및 RBAC에 대한 ISO 27001 App Service Environment/SQL Database 워크로드 청사진 샘플의 컨트롤 매핑입니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 7a760bfe70fa2a83c43a0b41b77ba9bf45e809ca
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258610"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>ISO 27001 ASE/SQL 워크로드 청사진 샘플에 대한 컨트롤 매핑

다음 문서에서는 Azure Blueprints ISO 27001 ASE/SQL 워크로드 청사진 샘플이 ISO 27001 컨트롤에 매핑되는 방법을 자세히 설명합니다. 컨트롤에 대한 자세한 내용은 [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)을 참조하세요.

다음은 **ISO 27001:2013** 컨트롤에 대한 매핑입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, ISO 27001:2013 컨트롤 **감사 [미리 보기]를 찾아서 선택하고 감사 요구 사항**이 담긴 정책 이니셔티브를 지원하는 특정 VM 확장을 배포합니다.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 의무 분리

Azure 구독 소유자가 한 명만 있으면 관리 중복이 허용되지 않습니다. 반대로, Azure 구독 소유자가 너무 많으면 손상된 소유자 계정을 통한 위반 가능성이 증가할 수 있습니다. 이 청사진을 사용하면 Azure 구독에 대한 소유자 수를 감사하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당하여 Azure 구독 소유자 수를 적당하게 유지할 수 있습니다. 구독 소유자 권한을 관리하면 적절한 임무 분리를 구현할 수 있습니다.

- [미리 보기]: Audit minimum number of owners for subscription
- [미리 보기]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 네트워크 및 네트워크 서비스 액세스

Azure는 Azure 리소스에 대한 액세스 권한이 있는 사용자를 관리하기 위해 [RBAC(역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. 이 청사진을 통해 [Azure Policy](../../../policy/overview.md) 정의 7개를 할당하여 Azure 리소스에 대한 액세스 권한을 제어할 수 있습니다. 이 정책은 리소스에 더 많은 권한의 액세스를 허용할 수 있는 리소스 유형의 사용을 감사합니다.
이 정책을 위반하는 리소스를 이해하면 Azure 리소스 액세스가 권한 있는 사용자로 제한되도록 정정 작업을 실행하는 데 도움이 될 수 있습니다.

- [미리 보기]: Deploy VM extension to audit Linux VM accounts with no passwords
- [미리 보기]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  암호s
- [미리 보기]: Audit Linux VM accounts with no passwords
- [미리 보기]: Audit Linux VM allowing remote connections from accounts with no passwords
- 클래식 스토리지 계정의 사용 감사
- 클래식 가상 머신의 사용 감사
- 관리 디스크를 사용하지 않는 VM 감사

## <a name="a922-user-access-provisioning"></a>A.9.2.2 사용자 액세스 프로비전

Azure는 Azure 리소스에 대한 액세스 권한이 있는 사용자를 관리하기 위해 [RBAC(역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. 이 청사진은 SQL Servers 및 [Service Fabric](../../../../service-fabric/service-fabric-overview.md)에 대한 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) 인증 사용을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 3개를 할당합니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. 이 청사진은 사용자 지정 RBAC 규칙의 사용을 감사하는 Azure Policy 정의도 할당합니다. 사용자 지정 RBAC 규칙은 오류가 발생할 가능성이 높기 때문에 사용자 지정 RBAC 규칙이 구현되는 경우를 이해하면 어떤 구현이 필요하고 적절한지 확인하는 데 도움이 될 수 있습니다.

- SQL Server에 대한 Azure Active Directory 관리자의 프로비전 감사
- Service Fabric의 클라이언트 인증에 대한 Azure Active Directory의 사용 감사
- 사용자 지정 RBAC 규칙의 사용 감사

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 권한 있는 액세스 권한 관리

이 청사진은 소유자 및/또는 쓰기 권한이 있는 외부 계정 및 다단계 인증을 사용하지 않는 소유자 및/또는 쓰기 권한을 가진 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 4개를 할당하여, 권한 있는 액세스 권한을 제한 및 제어하는 데 도움이 됩니다.

- [미리 보기]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [미리 보기]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [미리 보기]: Audit external accounts with owner permissions on a subscription
- [미리 보기]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 사용자의 비밀 인증 정보 관리

이 청사진은 다단계 인증을 사용하지 않는 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 3개를 할당합니다. 다단계 인증을 사용하면 인증 정보 중 한 부분이 손상되더라도 계정을 안전하게 유지할 수 있습니다. 다단계 인증을 사용하지 않고 계정을 모니터링하면 손상될 가능성이 더 높은 계정을 식별할 수 있습니다. 또한 이 청사진은 Linux VM 암호 파일 권한을 감사하여 잘못 설정된 경우 경고하는 Azure Policy 정의 2개도 할당합니다. 이 설정을 사용하여 인증이 손상되지 않도록 정정 작업을 실행할 수 있습니다.

- [미리 보기]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [미리 보기]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [미리 보기]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [미리 보기]: Deploy VM extension to audit Linux VM passwd file permissions
- [미리 보기]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 사용자 액세스 권한 검토

Azure는 Azure의 리소스에 대한 액세스 권한이 있는 사용자를 관리할 수 있도록 [RBAC(역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 이 청사진은 사용 중단된 계정 및 상승된 권한이 있는 외부 계정을 포함하여 우선적으로 검토해야 하는 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 4개를 할당합니다.

- [미리 보기]: Audit deprecated accounts on a subscription
- [미리 보기]: Audit deprecated accounts with owner permissions on a subscription
- [미리 보기]: Audit external accounts with owner permissions on a subscription
- [미리 보기]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 액세스 권한 제거 또는 조정

Azure는 Azure의 리소스에 대한 액세스 권한이 있는 사용자를 관리할 수 있도록 [RBAC(역할 기반 액세스 제어)](../../../../role-based-access-control/overview.md)를 구현합니다. [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) 및 RBAC를 사용하여 사용자 역할을 조직 변경이 반영되도록 업데이트할 수 있습니다. 필요한 경우 계정을 로그인하지 못하게 차단하거나 제거하여 Azure 리소스에 대한 액세스 권한을 즉시 제거할 수 있습니다. 이 청사진은 제거 대상으로 고려해야 하는 사용 중단 계정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당합니다.

- [미리 보기]: Audit deprecated accounts on a subscription
- [미리 보기]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 암호 관리 시스템

이 청사진은 최소 강도 및 기타 암호 요구 사항을 적용하지 않는 Windows VM을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 10개를 할당하여 강력한 암호를 적용하는 데 도움이 됩니다. 암호 강도 정책을 위반하는 VM을 인식하면 사용자 계정의 암호가 정책을 준수하도록 정정 작업을 수행하는 데 도움이 됩니다.

- [미리 보기]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [미리 보기]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [미리 보기]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [미리 보기]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [미리 보기]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [미리 보기]: Audit Windows VM enforces password complexity requirements
- [미리 보기]: Audit Windows VM maximum password age 70 days
- [미리 보기]: Audit Windows VM minimum password age 1 day
- [미리 보기]: Audit Windows VM passwords must be at least 14 characters
- [미리 보기]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 암호화 컨트롤의 사용에 대한 정책

이 청사진은 특정 암호화 컨트롤을 적용하고 취약한 암호 설정의 사용을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 13개를 할당하여 암호화 컨트롤 사용에 대한 정책을 적용하는 데 도움이 됩니다.
Azure 리소스의 암호화 구성이 최적이 아닐 수 있는 경우를 이해하면 리소스가 정보 보안 정책에 따라 구성되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다. 구체적으로, 이 청사진에서 할당하는 정책을 적용하려면 BLOB 스토리지 계정 및 Data Lake 스토리지 계정에 대한 암호화가 필요하며 SQL 데이터베이스에 대해 투명한 암호화가 필요합니다.
또한 스토리지 계정, SQL 데이터베이스, 가상 머신 디스크 및 자동화 계정 변수에 대한 암호화 누락을 감사하고, 스토리지 계정, Function App, 웹앱, API 앱 및 Redis Cache에 대한 안전하지 않은 연결을 감사하고, 취약한 가상 머신 암호 암호화를 감사하고, 암호화되지 않은 Service Fabric 통신을 감사해야 합니다.

- [미리 보기]: Audit HTTPS only access for a Function App
- [미리 보기]: Audit HTTPS only access for a Web Application
- [미리 보기]: Audit HTTPS only access for an API App
- [미리 보기]: Audit missing blob encryption for storage accounts
- [미리 보기]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  암호화n
- [미리 보기]: Audit Windows VM should not store passwords using reversible encryption
- [미리 보기]: Monitor unencrypted SQL database in Azure Security Center
- [미리 보기]: Monitor unencrypted VM Disks in Azure Security Center
- 자동화 계정 변수 암호화의 사용 감사
- Redis Cache에 대한 보안 연결의 사용만 감사
- 스토리지 계정에 대한 보안 전송 감사
- Service Fabric에서 EncryptAndSign에 대한 ClusterProtectionLevel 속성의 설정 감사
- 투명한 데이터 암호화 상태 감사

## <a name="a1241-event-logging"></a>A.12.4.1 이벤트 로깅

이 청사진은 Azure 리소스에 대한 로그 설정을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 7개를 할당하여 시스템 이벤트가 로깅되도록 하는 데 도움이 됩니다. 또한 할당된 정책은 가상 머신이 지정된 로그 분석 작업 영역으로 로그를 보내지 않는지도 감사합니다.

- [미리 보기]: Dependency Agent 배포 감사 - VM 이미지(OS)가 나열 취소됨
- [미리 보기]: VMSS의 Dependency Agent 배포 감사 - VM 이미지(OS)가 나열 취소됨
- [미리 보기]: Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- [미리 보기]: VMSS의 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨
- [미리 보기]: Monitor unaudited SQL database in Azure Security Center
- 진단 설정 감사
- SQL 감사 서버 수준 감사 설정

## <a name="a121-management-of-technical-vulnerabilities"></a>A.12.1 기술 취약성 관리

이 청사진은 누락된 시스템 업데이트, 운영 체제 취약성, SQL 취약성 및 가상 머신 취약성을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 5개를 할당하여 정보 시스템 취약성을 관리하는 데 도움이 됩니다. 이러한 인사이트는 배포된 리소스의 보안 상태에 대한 실시간 정보를 제공하며, 업데이트 관리 작업의 우선 순위를 지정하는 데 도움이 될 수 있습니다.

- [미리 보기]: Monitor missing Endpoint Protection in Azure Security Center
- [미리 보기]: Monitor missing system updates in Azure Security Center
- [미리 보기]: Monitor OS vulnerabilities in Azure Security Center
- [미리 보기]: Monitor SQL vulnerability assessment results in Azure Security Center
- [미리 보기]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 소프트웨어 설치에 대한 제한 사항

적응형 애플리케이션 제어는 Azure에 있는 VM에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 되는 Azure Security Center의 솔루션입니다. 이 청사진은 허용된 애플리케이션 세트에 대한 변경 사항을 모니터링하는 Azure Policy 정의를 할당합니다. 소프트웨어 설치에 대한 제한 사항은 소프트웨어 취약성이 도입될 가능성을 줄이는 데 도움이 될 수 있습니다.

- [미리 보기]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 네트워크 제어

이 청사진은 관대한 규칙을 사용하여 네트워크 보안 그룹을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 네트워크를 관리하고 제어하는 데 도움이 됩니다. 너무 관대한 규칙은 의도치 않은 네트워크 액세스를 허용할 수 있으며, 검토를 거쳐야 합니다.

- [미리 보기]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 정보 전송 정책 및 절차

이 청사진은 스토리지 계정 및 Redis Cache에 대한 비보안 연결을 감사하는 [Azure Policy](../../../policy/overview.md) 정의 2개를 할당하여 Azure 서비스를 사용한 정보 전송이 안전하게 이루어지도록 하는 데 도움이 됩니다.

- Redis Cache에 대한 보안 연결의 사용만 감사
- 스토리지 계정에 대한 보안 전송 감사

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 애플리케이션 서비스 트랜잭션 보호

이 청사진은 보호되지 않는 엔드포인트, 애플리케이션 및 스토리지 계정을 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 3개를 할당하여 정보 시스템 자산을 보호하는 데 도움이 됩니다. 방화벽으로 보호되지 않는 엔드포인트 및 애플리케이션과 액세스가 제한되지 않는 스토리지 계정은 정보 시스템 내에 포함된 정보에 대한 원치 않는 액세스를 허용할 수 있습니다.

- [미리 보기]: Monitor unprotected network endpoints in Azure Security Center
- [미리 보기]: Monitor unprotected web application in Azure Security Center
- 스토리지 계정에 대한 무제한 네트워크 액세스 감사

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 정보 보안 약점 보고

이 청사진은 취약성, 패치 상태 및 Azure Security Center의 맬웨어 경고를 모니터링하는 [Azure Policy](../../../policy/overview.md) 정의 5개를 할당하여 시스템 취약성을 인식하는 데 도움이 됩니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 확보할 수 있게 해주는 보고 기능을 제공합니다.

- [미리 보기]: Monitor missing Endpoint Protection in Azure Security Center
- [미리 보기]: Monitor missing system updates in Azure Security Center
- [미리 보기]: Monitor OS vulnerabilities in Azure Security Center
- [미리 보기]: Monitor SQL vulnerability assessment results in Azure Security Center
- [미리 보기]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>다음 단계

지금까지 ISO 27001 App Service Environment/SQL Database 워크로드 청사진 샘플의 컨트롤 매핑을 살펴보았으므로 다음 문서를 참조하여 아키텍처와 이 샘플을 배포하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database 워크로드 청사진 - 개요](./index.md)
> [ISO 27001 App Service Environment/SQL Database 워크로드 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기