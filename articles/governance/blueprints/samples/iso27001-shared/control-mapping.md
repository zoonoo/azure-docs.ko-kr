---
title: 샘플-ISO 27001 blueprint-컨트롤 매핑
description: ISO 27001 blueprint 샘플의 컨트롤 매핑입니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f655db8643a16e6818f5becf25c8761fb4c9093b
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2019
ms.locfileid: "58008042"
---
# <a name="control-mapping-of-the-azure-blueprints-iso-27001-blueprint-sample"></a>Azure 청사진 ISO 27001 blueprint 샘플의 컨트롤 매핑

다음 문서는 Azure 청사진 ISO 27001 공유 서비스의 ISO 27001 컨트롤 샘플 맵 blueprint 하는 방법을 자세히 설명 합니다. 컨트롤에 대 한 자세한 내용은 참조 하세요. [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)합니다.

다음 매핑은 합니다 **ISO 27001:2013** 컨트롤입니다. 특정 컨트롤 매핑을으로 직접 이동 하려면 오른쪽 탐색을 사용 합니다. 매핑된 컨트롤은 대부분 사용 하 여 구현 됩니다는 [Azure 정책](../../../policy/overview.md) 이니셔티브입니다. 전체 이니셔티브를 검토 하려면 엽니다 **정책** azure 포털을 선택 합니다 **정의** 페이지. 그런 다음 찾아 선택 합니다  **[미리 보기] 감사 ISO 27001:2013 제어 및 감사 요구 사항을 지원 하도록 특정 VM 확장을 배포** 기본 제공 정책 이니셔티브입니다.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 의무 분리

하나의 Azure 구독 소유자만 있으면 관리 중복성을 위해 허용 하지 않습니다. 반대로, Azure 구독 소유자가 너무 많은 것의 손상 된 소유자 계정을 통해 위반 가능성을 높일 수 있습니다. 이 blueprint를 사용 하면 두 개를 할당 하 여 Azure 구독 소유자의 적절 한 수를 유지 [Azure 정책](../../../policy/overview.md) Azure 구독에 대 한 소유자의 수를 감사 하는 정의 합니다. 구독 소유자 권한 관리 업무를 적절 한 분리를 구현할 수 있습니다.

- [미리 보기]: Audit minimum number of owners for subscription
- [미리 보기]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>네트워크 및 네트워크 서비스에 대 한 A.9.1.2 액세스

Azure 구현 [역할 기반 액세스 제어](../../../../role-based-access-control/overview.md) (RBAC)로 Azure 리소스에 대 한 액세스 권한이 있는 사용자를 관리 합니다. 이 blueprint를 사용 하면 7 개를 할당 하 여 Azure 리소스에 대 한 액세스를 제어할 [Azure 정책](../../../policy/overview.md) 정의 합니다. 이러한 정책은 리소스 유형의 사용을 감사 및 리소스에 대 한 액세스 허용 범위가 더 허용 하는 구성 합니다.
권한이 있는 사용자를 위반 하 게 Azure 리소스 액세스를 보장 하기 위한 교정 조치를 수행 하는 도움말으로 제한 됩니다. 이러한 정책 수에 있는 리소스를 이해 합니다.

- [미리 보기]: Deploy VM extension to audit Linux VM accounts with no passwords
- [미리 보기]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  암호s
- [미리 보기]: Audit Linux VM accounts with no passwords
- [미리 보기]: Audit Linux VM allowing remote connections from accounts with no passwords
- 클래식 스토리지 계정의 사용 감사
- 클래식 가상 머신의 사용 감사
- 관리 되는 디스크를 사용 하지 않는 Vm 감사

## <a name="a922-user-access-provisioning"></a>A.9.2.2 사용자 액세스 프로 비전

Azure 구현 [역할 기반 액세스 제어](../../../../role-based-access-control/overview.md) (RBAC)로 Azure 리소스에 대 한 액세스 권한이 있는 사용자를 관리 합니다. 이 blueprint 할당 세 [Azure Policy](../../../policy/overview.md) 감사 정의 사용 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) SQL Server에 대 한 인증 및 [Service Fabric](../../../../service-fabric/service-fabric-overview.md)합니다. Azure Active Directory를 사용 하 여 인증을 사용 하면 간소화 된 사용 권한 관리 및 데이터베이스 사용자 및 다른 Microsoft 서비스의 중앙 집중식된 id 관리 합니다. 이 blueprint는 또한 사용자 지정 RBAC 규칙의 사용을 감사 하기 위한 Azure Policy 정의 할당 합니다. 사용자 지정 RBAC 규칙 구현에 있는 이해 사용자 지정 RBAC 규칙은 오류가 발생 하기 쉽습니다 필요 하 고 적절 한 구현을 확인 하는 데 도움이 됩니다.

- SQL Server에 대한 Azure Active Directory 관리자의 프로비전 감사
- Service Fabric의 클라이언트 인증에 대한 Azure Active Directory의 사용 감사
- 사용자 지정 RBAC 규칙의 사용 감사

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 관리 권한 있는 액세스 권한

이 blueprint를 사용 하면 제한 하 고 4 개를 할당 하 여 권한 있는 액세스 권한 제어 [Azure 정책](../../../policy/overview.md) 쓰기 권한 및/또는 쓰기 권한 및 계정 소유자 및/또는 외부 계정 소유자를 사용 하 여 감사 정의 multi-factor authentication 사용 하는 필요가 없습니다.

- [미리 보기]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [미리 보기]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [미리 보기]: Audit external accounts with owner permissions on a subscription
- [미리 보기]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>사용자의 암호 인증 정보의 A.9.2.4 관리

이 blueprint 할당 세 [Azure 정책](../../../policy/overview.md) multi-factor authentication 사용 되지 않은 계정을 감사 하는 정의 합니다. Multi-factor authentication은 인증 정보는 한 가지 손상 된 경우에 계정을 안전 하 게 유지 합니다. 계정을 사용 하도록 설정 하는 multi-factor authentication 없이 모니터링 하 여 손상 될 가능성이 수 있는 계정을 식별할 수 있습니다. 이 blueprint는 또한 Linux VM을 감사 하는 두 개의 Azure Policy 정의 하는 잘못 설정 하는 경우 경고를 발생 시 암호 파일 권한을 할당 합니다. 이 설치 프로그램을 사용 하면 인증자 손상 되지 않도록 조치를 취할 수 있습니다.

- [미리 보기]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [미리 보기]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [미리 보기]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [미리 보기]: Deploy VM extension to audit Linux VM passwd file permissions
- [미리 보기]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>사용자 액세스 권한 A.9.2.5 검토

Azure 구현 [역할 기반 액세스 제어](../../../../role-based-access-control/overview.md) (RBAC)를 사용 하면 Azure에서 리소스에 대 한 액세스 권한이 있는 사용자 관리 합니다. Azure portal을 사용 하 여 Azure 리소스 및 해당 사용 권한과 액세스 권한이 있는 사용자를 검토할 수 있습니다. 이 blueprint 할당 네 [Azure 정책](../../../policy/overview.md) 감가 상각된 계정 및 권한 있는 외부 계정을 포함 하 여 검토를 위해 우선 하는 계정을 감사 하는 정의 합니다.

- [미리 보기]: Audit deprecated accounts on a subscription
- [미리 보기]: Audit deprecated accounts with owner permissions on a subscription
- [미리 보기]: Audit external accounts with owner permissions on a subscription
- [미리 보기]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 제거 또는 액세스 권한 조정

Azure 구현 [역할 기반 액세스 제어](../../../../role-based-access-control/overview.md) (RBAC)를 사용 하면 Azure에서 리소스에 대 한 액세스 권한이 있는 사용자 관리 합니다. 사용 하 여 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) RBAC를 조직 변경 내용을 반영 하도록 사용자 역할을 업데이트할 수 있습니다. 필요한 경우 계정 로그인에서 차단 (하거나 제거할 수)를 즉시 제거 하는 Azure 리소스에 대 한 액세스 권한. 이 blueprint 할당 두 [Azure 정책](../../../policy/overview.md) 감가 상각된 계정 제거를 위해 고려해 야 하는 감사를 정의 합니다.

- [미리 보기]: Audit deprecated accounts on a subscription
- [미리 보기]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 암호 관리 시스템

이 blueprint를 사용 하면 10을 할당 하 여 강력한 암호 [Azure 정책](../../../policy/overview.md) 최소 수준 및 기타 암호 요구 사항을 적용 하지 않는 Windows Vm을 감사 하는 정의 합니다. 암호 강도 정책 위반에서 인식 vm은 모든 VM의 사용자 계정에 대 한 암호 정책을 준수 하도록 올바른 조치를 취할 수 있습니다.

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

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>암호화 컨트롤의 사용에 대 한 A.10.1.1 정책

이 blueprint를 사용 하면 13을 할당 하 여 간단히 컨트롤의 사용에 정책을 적용할 [Azure 정책](../../../policy/overview.md) 취약 한 암호화 설정을 사용 하 여 간단히 특정 제어 및 감사를 적용 하는 정의 합니다.
Azure 리소스 최적이 아닌 암호화 구성이 있을 수 있는 이해 수 리소스 정보 보안 정책에 따라 구성 되었는지 확인 하려면 올바른 조치를 취할 수 있습니다. 이 blueprint에 의해 할당 된 정책 특히 blob 저장소 계정 및 데이터 레이크 저장소 계정 암호화 필요 SQL 데이터베이스에서 투명 한 데이터 암호화 필요 저장소 계정, SQL 데이터베이스, 가상 머신 디스크 및 automation 계정 변수;에서 누락 된 암호화 감사 저장소 계정, 함수 앱, 웹 앱, API 앱 및 Redis Cache;에 대 한 안전 하지 않은 연결 감사 가상 머신 취약 한 암호 암호화 감사 및 암호화 되지 않은 Service Fabric 통신을 감사 합니다.

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

이 blueprint를 사용 하면 7 개를 할당 하 여 시스템 이벤트 기록 [Azure 정책](../../../policy/overview.md) 하는 감사 로그 Azure 리소스에 대 한 설정을 정의 합니다. 가상 컴퓨터를 지정 된 log analytics 작업 영역에 로그를 보내지 않는 경우에 할당된 된 정책 감사 합니다.

- [미리 보기]: 종속성 에이전트 배포-VM 이미지 (OS) 목록에 없는 감사
- [미리 보기]: VMSS-VM 이미지 (OS) 목록에 없는에서 종속성 에이전트 배포를 감사 합니다.
- [미리 보기]: Log Analytics 에이전트 배포-VM 이미지 (OS) 목록에 없는 감사
- [미리 보기]: VMSS-VM 이미지 (OS) 목록에 없는의 audit Log Analytics 에이전트 배포
- [미리 보기]: Monitor unaudited SQL database in Azure Security Center
- 진단 설정 감사
- SQL 감사 서버 수준 감사 설정

## <a name="a121-management-of-technical-vulnerabilities"></a>A.12.1 기술 취약성 관리

이 blueprint를 사용 하면 5 개를 할당 하 여 정보 시스템 취약성 관리 [Azure 정책](../../../policy/overview.md) 누락 된 시스템 업데이트, 운영 체제 취약점, SQL 취약성 및 가상 컴퓨터를 모니터링 하는 정의 취약점으로 인 한 합니다. 이러한 정보 배포 된 리소스의 보안 상태에 대 한 실시간 정보를 제공 및 수정 작업을 우선 순위를 지정 하는 데 도움이 됩니다.

- [미리 보기]: Monitor missing Endpoint Protection in Azure Security Center
- [미리 보기]: Monitor missing system updates in Azure Security Center
- [미리 보기]: Monitor OS vulnerabilities in Azure Security Center
- [미리 보기]: Monitor SQL vulnerability assessment results in Azure Security Center
- [미리 보기]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>소프트웨어 설치에 대 한 A.12.6.2 제한

적응 응용 프로그램 제어는 통해 응용 프로그램을 제어할 수 있습니다 Azure에 위치한 Vm에서 실행할 수 있는 Azure Security Center에서 솔루션입니다. 이 blueprint는 허용 된 응용 프로그램의 집합에 변경 내용을 모니터링 하는 Azure Policy 정의 할당 합니다. 소프트웨어 설치에 대 한 제한 소프트웨어 취약점이 가능성을 줄일 수 있습니다.

- [미리 보기]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 네트워크 제어

이 blueprint를 사용 하면 관리 하 고 할당 하 여 네트워크 제어를 [Azure 정책](../../../policy/overview.md) 관대 한 규칙을 사용 하 여 네트워크 보안 그룹을 모니터링 하는 정의 합니다. 규칙을 너무 높게 의도 하지 않은 네트워크 액세스를 허용 될 수 있습니다 하 고 검토 해야 합니다.

- [미리 보기]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 정보 전송 정책 및 절차

Blueprint를 사용 하면 두 개를 할당 하 여 Azure 서비스를 사용 하 여 정보 전송의 보안을 유지 [Azure 정책](../../../policy/overview.md) 감사 저장소 계정 및 Redis Cache에 대 한 안전 하지 않은 연결을 정의 합니다.

- Redis Cache에 대한 보안 연결의 사용만 감사
- 스토리지 계정에 대한 보안 전송 감사

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 보호 응용 프로그램 서비스 트랜잭션

이 blueprint를 통해 3 개를 할당 하 여 정보 시스템 자산을 보호할 수 [Azure 정책](../../../policy/overview.md) 보호 되지 않는 끝점, 응용 프로그램 및 저장소 계정을 모니터링 하는 정의 합니다. 끝점 및 방화벽 및 무제한 액세스를 사용 하 여 저장소 계정에서 보호 되지 않는 응용 프로그램 정보 시스템 내에 포함 된 정보에 의도 하지 않은 액세스를 허용할 수 있습니다.

- [미리 보기]: Monitor unprotected network endpoints in Azure Security Center
- [미리 보기]: Monitor unprotected web application in Azure Security Center
- 스토리지 계정에 대한 무제한 네트워크 액세스 감사

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 보고 정보 보안 약점

이 blueprint를 사용 하면 5 개를 할당 하 여 시스템 취약성에 대 한 인식을 유지 [Azure 정책](../../../policy/overview.md) 취약점, 패치 상태 및 Azure Security Center에서 맬웨어 경고를 모니터링 하는 정의 합니다. Azure Security Center는 배포 된 Azure 리소스의 보안 상태에 대 한 실시간 통찰력을 할 수 있도록 하는 보고 기능을 제공 합니다.

- [미리 보기]: Monitor missing Endpoint Protection in Azure Security Center
- [미리 보기]: Monitor missing system updates in Azure Security Center
- [미리 보기]: Monitor OS vulnerabilities in Azure Security Center
- [미리 보기]: Monitor SQL vulnerability assessment results in Azure Security Center
- [미리 보기]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>다음 단계

컨트롤 매핑의 ISO 27001 공유 서비스 blueprint 보신 했으므로 아키텍처 및이 샘플을 배포 하는 방법에 대해 알아보려면 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [ISO 27001 공유 서비스 blueprint-개요](./index.md)
> [ISO 27001 공유 서비스 blueprint-배포 단계](./deploy.md)

청사진 및 사용 하는 방법에 대 한 추가 문서:

- 에 대 한 자세한 합니다 [수명 주기 blueprint](../../concepts/lifecycle.md)합니다.
- 사용 하는 방법 이해 [정적 및 동적 매개 변수](../../concepts/parameters.md)합니다.
- 사용자 지정 하는 방법을 알아봅니다 합니다 [시퀀싱 순서 blueprint](../../concepts/sequencing-order.md)합니다.
- 확인 하는 방법을 알아봅니다 이용 [리소스 잠금 blueprint](../../concepts/resource-locking.md)합니다.
- 설명 하는 방법 [기존 할당을 업데이트할](../../how-to/update-existing-assignments.md)합니다.