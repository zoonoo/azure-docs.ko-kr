---
title: 샘플-영국 공식 및 영국 NHS 청사진-컨트롤 매핑
description: 영국 공식 및 영국 NHS blueprint 샘플 컨트롤 매핑.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 01a8e104f6d590113784db28e4bfde849d78b15f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491923"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>영국 공식 및 영국 NHS blueprint 샘플 컨트롤 매핑

다음 문서에서는 영국 공식 및 영국 NHS 컨트롤에 영국 공식 및 영국 NHS blueprint 샘플을 매핑하는 방법을 자세히 설명 합니다. 컨트롤에 대 한 자세한 내용은 참조 하세요. [영국 공식](https://www.gov.uk/government/publications/government-security-classifications)합니다.

다음 매핑은에 **영국 공식** 하 고 **영국 NHS** 컨트롤입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음 찾아 선택 합니다  **[미리 보기] 영국 공식 감사 및 영국 NHS 제어 및 감사 요구 사항을 지원 하도록 특정 VM 확장을 배포** 기본 제공 정책 이니셔티브입니다.

## <a name="1-data-in-transit-protection"></a>1 전송 중인 데이터 보호

Blueprint 할당 하 여 Azure 서비스를 사용 하 여 정보 전송의 보안을 유지 하는 데 도움이 [Azure 정책](../../../policy/overview.md) 저장소 계정 및 Redis Cache에 대 한 안전 하지 않은 연결을 감사 하는 정의 합니다.

- Redis Cache에 대 한 보안 연결만 사용 하도록 설정 해야 합니다.
- 저장소 계정에 보안 전송을 사용 해야

## <a name="23-data-at-rest-protection"></a>2.3 미사용 데이터 보호

이 blueprint를 사용 하면 간단히 컨트롤의 사용에 정책을 할당 하 여 적용할 [Azure 정책](../../../policy/overview.md) 취약 한 암호화 설정을 사용 하 여 간단히 특정 제어 및 감사를 적용 하는 정의 합니다.
Azure 리소스의 암호화 구성이 최적이 아닐 수 있는 경우를 이해하면 리소스가 정보 보안 정책에 따라 구성되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다. 이 blueprint에 의해 할당 된 정책 데이터 레이크 저장소 계정에 대 한 암호화를 요구 하는 특히 SQL 데이터베이스에서 투명 한 데이터 암호화 필요 저장소 계정, SQL 데이터베이스, 가상 머신 디스크 및 automation 계정 변수;에서 누락 된 암호화 감사 저장소 계정 및 Redis Cache;에 대 한 감사 안전 하지 않은 연결 가상 머신 취약 한 암호 암호화 감사 및 암호화 되지 않은 Service Fabric 통신을 감사 합니다.

- Azure Security Center에서 암호화 되지 않은 SQL 데이터베이스 모니터링
- 가상 컴퓨터에서 디스크 암호화 적용
- Automation 계정 변수를 암호화 해야
- 저장소 계정에 보안 전송을 사용 해야
- Service Fabric 클러스터 ClusterProtectionLevel 속성 EncryptAndSign으로 설정 해야 합니다.
- SQL database에서 투명 한 데이터 암호화를 사용 해야
- SQL DB 투명 한 데이터 암호화를 배포 합니다.
- Data Lake Store 계정 암호화 필요
- 허용 되는 위치 (되었습니다 하드 코딩 "영국 남부" 및 "영국 서 부")
- 허용 되는 리소스 그룹에 대 한 위치 (되었습니다 하드 코딩 "영국 남부" 및 "영국 서 부")

## <a name="52-vulnerability-management"></a>5.2 취약성 관리

이 blueprint 할당 하 여 정보 시스템 취약성 관리 도움이 [Azure 정책](../../../policy/overview.md) 누락 된 시스템 업데이트, 운영 체제 취약점, SQL, 누락 된 endpoint protection을 모니터링 하는 정의 보안 문제 및 가상 머신 취약점으로 인 한 합니다. 이러한 인사이트는 배포된 리소스의 보안 상태에 대한 실시간 정보를 제공하며, 업데이트 관리 작업의 우선 순위를 지정하는 데 도움이 될 수 있습니다.

- Azure Security Center에서 누락된 Endpoint Protection 모니터링
- 컴퓨터에서 시스템 업데이트 설치
- 취약점으로 인 한 컴퓨터에서 보안 구성 수정 되어야 합니다.
- SQL 데이터베이스에서 취약성을 수정 되어야 합니다.
- 취약점으로 인 한 취약성 평가 솔루션에서 수정 되어야 합니다.

## <a name="53-protective-monitoring"></a>5.3 보호 모니터링

이 blueprint 할당 하 여 정보 시스템 자산을 보호 하는 데 도움이 [Azure 정책](../../../policy/overview.md) 무제한 액세스를 허용 목록에 추가 작업을 및 위협 보호 모니터링을 제공 하는 정의 합니다.

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- 가상 컴퓨터에서 적응 응용 프로그램 제어를 사용할 수 있어야
- SQL 서버에 위협 요소 탐지를 배포 합니다.
- Windows Server에 대 한 기본 Microsoft IaaS 맬웨어 방지 확장을 배포 합니다.

## <a name="9-secure-user-management--10-identity-and-authentication"></a>사용자 관리를 보호 하는 9 / 10 Id 및 인증

Azure 역할 기반 액세스 제어 (RBAC)를 관리할 수 있도록 Azure에서 리소스에 대 한 액세스 권한이 있는 사용자를 구현 합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 이 blueprint를 사용 하면 제한 하 고 할당 하 여 액세스 권한을 제어할 [Azure 정책](../../../policy/overview.md) 정의 소유자 및/또는 읽기/쓰기 권한이 있는 외부 계정 및 계정 소유자를 사용 하 여 감사를 읽거나 쓰기 수행 하는 권한 multi-factor authentication 인증을 사용할 수 없습니다.

- 구독 소유자 권한이 있는 계정에서 MFA는 사용할 수 있어야
- MFA 구독에 대 한 쓰기 권한 사용 하 여 활성화 된 계정이 있어야 합니다.
- 구독에 대 한 읽기 권한이 있는 계정에서 MFA는 사용할 수 있어야
- 소유자 권한이 있는 외부 계정은 구독에서 제거 해야
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 읽기 권한이 있는 외부 계정은 구독에서 제거 해야

이 blueprint는 SQL server 및 Service Fabric에 대 한 Azure Active Directory 인증의 사용을 감사 하려면 Azure Policy 정의 할당 합니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다.

- SQL server에 대 한 Azure Active Directory 관리자 프로 비전 되어야 합니다.
- Service Fabric 클러스터에만 클라이언트 인증을 위해 Azure Active Directory 사용 해야

이 blueprint는 Azure Policy 정의 감가 상각 계정과 외부 계정을 포함 하 여 검토를 위해 우선 하는 계정을 감사를 할당 합니다. 필요한 경우 계정을 로그인하지 못하게 차단하거나 제거하여 Azure 리소스에 대한 액세스 권한을 즉시 제거할 수 있습니다. 이 blueprint 상각액을 감사 계정 제거를 위해 고려해 야 하는 두 가지 Azure 정책 정의 할당 합니다.

- 사용 되지 않는 계정은 구독에서 제거 해야
- 소유자 권한이 있는 사용 되지 않는 계정은 구독에서 제거 해야
- 소유자 권한이 있는 외부 계정은 구독에서 제거 해야
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.

이 blueprint는 또한 Linux VM을 감사 하는 Azure Policy 정의 해당 하는 잘못 설정 하는 경우 경고를 발생 시 암호 파일 권한을 할당 합니다. 이 디자인을 사용 하면 인증자 손상 되지 않도록 조치를 취할 수 있습니다.

- [미리 보기]: Audit Linux VM /etc/passwd file permissions are set to 0644

이 blueprint를 사용 하면 최소 수준 및 기타 암호 요구 사항을 적용 하지 않는 Windows Vm을 감사 하는 Azure Policy 정의 할당 하 여 강력한 암호를 적용할 수 있습니다. 암호 강도 정책을 위반하는 VM을 인식하면 모든 VM 사용자 계정의 암호가 정책을 준수하도록 정정 작업을 수행하는 데 도움이 됩니다.

- [미리 보기]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [미리 보기]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [미리 보기]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [미리 보기]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [미리 보기]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [미리 보기]: Audit Windows VMs that do not have the password complexity setting enabled
- [미리 보기]: Audit Windows VMs that do not have a maximum password age of 70 days
- [미리 보기]: Audit Windows VMs that do not have a minimum password age of 1 day
- [미리 보기]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [미리 보기]: Audit Windows VMs that allow re-use of the previous 24 passwords

이 blueprint도 통해 Azure Policy 정의 할당 하 여 Azure 리소스에 대 한 액세스를 제어할 수 있습니다. 이 정책은 리소스에 더 많은 권한의 액세스를 허용할 수 있는 리소스 유형의 사용을 감사합니다. 이 정책을 위반하는 리소스를 이해하면 Azure 리소스 액세스가 권한 있는 사용자로 제한되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다.

- [미리 보기]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [미리 보기]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [미리 보기]: Audit Linux VMs that have accounts without passwords
- [미리 보기]: Audit Linux VMs that allow remote connections from accounts without passwords
- 새 Azure Resource Manager 리소스에 저장소 계정은 마이그레이션해야
- 새 Azure Resource Manager 리소스에 가상 컴퓨터를 마이그레이션해야 합니다.
- 관리 디스크를 사용하지 않는 VM 감사

## <a name="11-external-interface-protection"></a>11 외부 인터페이스 보호

이 blueprint는 서비스 인터페이스를 할당 하 여 무단된 액세스 로부터 보호 하는 데 25 개 이상의 정책을 사용 하 여 적절 한 보안 사용자 관리, 이외의 [Azure 정책](../../../policy/overview.md) 모니터 무제한 정의 저장소 계정입니다. 무제한 액세스를 사용 하 여 저장소 계정 정보 시스템 내에 포함 된 정보에 의도 하지 않은 액세스할을 수 있습니다. 이 blueprint는 virtual machines에서 적응 응용 프로그램 컨트롤을 사용 하는 정책을 할당 합니다.

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- 가상 컴퓨터에서 적응 응용 프로그램 제어를 사용할 수 있어야

## <a name="12-secure-service-administration"></a>12 보안 서비스 관리

Azure 역할 기반 액세스 제어 (RBAC)를 관리할 수 있도록 Azure에서 리소스에 대 한 액세스 권한이 있는 사용자를 구현 합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 이 blueprint를 사용 하면 제한 하 고 5 개를 할당 하 여 권한 있는 액세스 권한 제어 [Azure 정책](../../../policy/overview.md) 쓰기 권한 및/또는 쓰기 권한 및 계정 소유자를 사용 하 여 및/또는 외부 계정 소유자를 사용 하 여 감사 정의 multi-factor authentication 사용 없는 합니다.

클라우드 서비스 관리에 사용되는 시스템에는 해당 서비스에 대해 높은 권한의 액세스가 있습니다. 이러한 시스템이 손상되면 보안 제어를 우회하고 대용량의 데이터를 도용하거나 조작할 수단을 비롯하여 상당한 영향을 미칩니다. 작업 서비스를 관리 하는 서비스 공급자의 관리자가 사용 된 메서드는 서비스의 보안을 저해할 수 있는 악용 위험을 완화 하기 위해 설계 되어야 합니다. 이 원칙 구현 되지 않았습니다 하는 경우 공격자가 보안 제어를 우회 하 고 도용 하거나 많은 양의 데이터를 조작 하는 방법이 있을 수 있습니다.

- 구독 소유자 권한이 있는 계정에서 MFA는 사용할 수 있어야
- MFA 구독에 대 한 쓰기 권한 사용 하 여 활성화 된 계정이 있어야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거 해야
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.

이 blueprint는 SQL server 및 Service Fabric에 대 한 Azure Active Directory 인증의 사용을 감사 하려면 Azure Policy 정의 할당 합니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다.

- SQL server에 대 한 Azure Active Directory 관리자 프로 비전 되어야 합니다.
- Service Fabric 클러스터에만 클라이언트 인증을 위해 Azure Active Directory 사용 해야

이 blueprint는 또한 감사 검토, 감가 상각된 계정 및 권한 있는 외부 계정 등을 위해 우선 하는 계정에 Azure Policy 정의 할당 합니다. 필요한 경우 계정을 로그인하지 못하게 차단하거나 제거하여 Azure 리소스에 대한 액세스 권한을 즉시 제거할 수 있습니다. 이 blueprint 상각액을 감사 계정 제거를 위해 고려해 야 하는 두 가지 Azure 정책 정의 할당 합니다.

- 사용 되지 않는 계정은 구독에서 제거 해야
- 소유자 권한이 있는 사용 되지 않는 계정은 구독에서 제거 해야
- 소유자 권한이 있는 외부 계정은 구독에서 제거 해야
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.

이 blueprint는 또한 Linux VM을 감사 하는 Azure Policy 정의 해당 하는 잘못 설정 하는 경우 경고를 발생 시 암호 파일 권한을 할당 합니다. 이 디자인을 사용 하면 인증자 손상 되지 않도록 조치를 취할 수 있습니다.

- [미리 보기]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>사용자에 대 한 13 감사 정보

이 blueprint 할당 하 여 시스템 이벤트 기록 도움이 [Azure 정책](../../../policy/overview.md) Azure 리소스의 로그 설정을 감사 하는 정의 합니다. 또한 할당된 정책은 가상 머신이 로그를 지정된 로그 분석 작업 영역에 보내지 않는지도 감사합니다.

- Azure Security Center에서 SQL 서버에 감사를 모니터링 합니다.
- 진단 설정 감사
- SQL 감사 서버 수준 감사 설정
- [미리 보기]: Deploy Log Analytics Agent for Linux VMs
- [미리 보기]: Deploy Log Analytics Agent for Windows VMs
- 가상 네트워크를 만들 때 네트워크 감시자를 배포 합니다.

## <a name="next-steps"></a>다음 단계

영국 공식 및 영국 NHS 청사진의 컨트롤 매핑, 보신 했으므로 개요 및이 샘플을 배포 하는 방법에 대해 알아보려면 다음 문서를 방문 합니다.

> [!div class="nextstepaction"]
> [영국 공식 및 영국 NHS 청사진-개요](./index.md)
> [영국 공식 및 영국 NHS 청사진-배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
