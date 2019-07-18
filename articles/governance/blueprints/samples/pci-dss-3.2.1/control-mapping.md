---
title: 샘플-PCI DSS v3.2.1 blueprint-컨트롤 매핑
description: Azure 정책 및 RBAC Payment Card Industry Data Security Standard v3.2.1 blueprint 샘플의 컨트롤 매핑.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540941"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>PCI DSS v3.2.1 blueprint 샘플의 컨트롤 매핑

다음 문서는 Azure 청사진 PCI-DSS v3.2.1 blueprint 샘플 PCI-DSS v3.2.1 컨트롤에 매핑되는 방법을 자세히 설명 합니다. 컨트롤에 대 한 자세한 내용은 참조 하세요. [PCI DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)합니다.

다음 매핑은 합니다 **PCI DSS v3.2.1:2018** 컨트롤입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음 찾아 선택 합니다  **[미리 보기] 감사 PCI v3.2.1:2018 제어 및 감사 요구 사항을 지원 하도록 특정 VM 확장을 배포** 기본 제공 정책 이니셔티브입니다.

## <a name="132-and-134-boundary-protection"></a>1.3.2 및 1.3.4 경계 보호

이 blueprint를 통해 관리 하 고 할당 하 여 네트워크를 제어할 [Azure 정책](../../../policy/overview.md) 관대 한 규칙을 사용 하 여 네트워크 보안 그룹을 모니터링 하는 정의 합니다. 너무 관대한 규칙은 원치 않는 네트워크 액세스를 허용할 수 있으며, 검토를 거쳐야 합니다. 이 blueprint 할당 보호 되지 않는 끝점, 응용 프로그램 및 저장소 계정을 모니터링 하는 Azure Policy 정의 합니다. 방화벽으로 보호되지 않는 엔드포인트 및 애플리케이션과 액세스가 제한되지 않는 스토리지 계정은 정보 시스템 내에 포함된 정보에 대한 원치 않는 액세스를 허용할 수 있습니다.

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- 인터넷 끝점을 통한 액세스 제한 해야 합니다.

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1 4.1.g, 4.1.h 및 6.5.3 암호화 보호

이 blueprint를 사용 하면 간단히 컨트롤 사용 하 여 정책을 할당 하 여 적용할 [Azure 정책](../../../policy/overview.md) 취약 한 암호화 설정을 사용 하 여 간단히 특정 제어 및 감사를 적용 하는 정의 합니다. Azure 리소스의 암호화 구성이 최적이 아닐 수 있는 경우를 이해하면 리소스가 정보 보안 정책에 따라 구성되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다. 이 blueprint에 의해 할당 된 정책에서 SQL 데이터베이스에서 투명 한 데이터 암호화를 요구 하는 특히 저장소 계정 및 automation 계정 변수에서 암호화 누락 감사 합니다. 주소를 저장소 계정, 함수 앱, 웹 앱, API 앱 및 Redis Cache에 대 한 안전 하지 않은 연결을 감사 하 고 암호화 되지 않은 Service Fabric 통신 감사 정책이 개입니다.

- 함수 앱에 HTTPS를 통해서만 액세스 가능
- 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능
- API 앱은 https만 액세스할 수 있어야
- Azure Security Center에서 암호화되지 않은 SQL 데이터베이스 모니터링
- 가상 컴퓨터에서 디스크 암호화 적용
- Automation 계정 변수를 암호화 해야
- Redis Cache에 대 한 보안 연결만 사용 하도록 설정 해야 합니다.
- 저장소 계정에 보안 전송을 사용 해야
- Service Fabric 클러스터 ClusterProtectionLevel 속성 EncryptAndSign으로 설정 해야 합니다.
- SQL database에서 투명 한 데이터 암호화를 사용 해야
- SQL DB 투명 한 데이터 암호화를 배포 합니다.

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 및 11.2.1 취약성 검색 및 시스템 업데이트

이 blueprint 할당 하 여 정보 시스템 취약성 관리 도움이 [Azure 정책](../../../policy/overview.md) 누락 된 시스템 업데이트, 운영 체제 취약점, SQL 취약성 및 가상 컴퓨터를 모니터링 하는 정의 Azure Security Center의 취약점으로 인 한 합니다. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 가질 수 있도록 하는 보고 기능을 제공합니다.

- Azure Security Center에서 누락된 Endpoint Protection 모니터링
- Windows Server에 대 한 기본 Microsoft IaaSAntimalware 확장 배포
- SQL Server에서 위협 감지를 배포 합니다.
- 컴퓨터에서 시스템 업데이트 설치
- 취약점으로 인 한 컴퓨터에서 보안 구성 수정 되어야 합니다.
- SQL 데이터베이스에서 취약성을 수정 되어야 합니다.
- 취약점으로 인 한 취약성 평가 솔루션에서 수정 되어야 합니다.

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 및 7.1.3 의무 분리

Azure 구독 소유자가 한 명만 있으면 관리 중복이 허용되지 않습니다. 반대로, Azure 구독 소유자가 너무 많으면 손상된 소유자 계정을 통한 위반 가능성이 증가할 수 있습니다. 이 blueprint 할당 하 여 Azure 구독 소유자의 적절 한 수를 유지 하는 데 도움이 [Azure 정책](../../../policy/overview.md) 감사 Azure 구독에 대 한 소유자의 수를 정의 합니다. 구독 소유자 권한을 관리하면 적절한 임무 분리를 구현하는 데 도움이 될 수 있습니다.

- 구독에 할당 하는 둘 이상의 소유자 해야
- 최대 3 소유자는 구독에 대해 지정 되어야 합니다. 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 권한 있는 액세스 권한의 8.3.1.a 및 8.3.1.b 관리

이 blueprint를 사용 하면 제한 하 고 할당 하 여 권한 있는 액세스 권한 제어 [Azure 정책](../../../policy/overview.md) 정의 외부 계정 소유자를 사용 하 여 감사를 작성 및/또는 사용 권한 및 직원 계정 소유자 및/또는 쓰기를 사용 하 여 읽기 multi-factor authentication 사용 되지 않은 권한입니다. Azure는 Azure 리소스에 대한 액세스 권한이 있는 사용자를 관리하기 위해 RBAC(역할 기반 액세스 제어)를 구현합니다. 사용자 지정 RBAC 규칙은 오류가 발생할 가능성이 높기 때문에 사용자 지정 RBAC 규칙이 구현되는 경우를 이해하면 어떤 구현이 필요하고 적절한지 확인하는 데 도움이 될 수 있습니다. 이 blueprint 할당 수도 [Azure 정책](../../../policy/overview.md) SQL 서버에 대 한 Azure Active Directory 인증 사용 하 여 감사를 정의 합니다. Azure Active Directory를 사용 하 여 인증 권한 관리를 간소화 및 데이터베이스 사용자 및 다른 Microsoft의 id 관리를 중앙 집중화  
서비스입니다.
 
- 소유자 권한이 있는 외부 계정은 구독에서 제거 해야
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 읽기 권한이 있는 외부 계정은 구독에서 제거 해야
- 구독 소유자 권한이 있는 계정에서 MFA는 사용할 수 있어야
- MFA 구독에 대 한 쓰기 권한 사용 하 여 활성화 된 계정이 있어야 합니다.
- 구독에 대 한 읽기 권한이 있는 계정에서 MFA는 사용할 수 있어야
- SQL server에 대 한 Azure Active Directory 관리자 프로 비전 되어야 합니다.
- 사용자 지정 RBAC 규칙의 사용 감사

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 및 8.1.5 최소 권한 사용자 액세스 권한 검토

Azure 역할 기반 액세스 제어 (RBAC)를 관리할 수 있도록 Azure에서 리소스에 대 한 액세스 권한이 있는 사용자를 구현 합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 이 blueprint 할당 [Azure 정책](../../../policy/overview.md) 감가 상각된 계정 및 권한 있는 외부 계정을 포함 하 여 검토를 위해 우선 하는 계정을 감사를 정의 합니다.

- 사용 되지 않는 계정은 구독에서 제거 해야
- 소유자 권한이 있는 사용 되지 않는 계정은 구독에서 제거 해야
- 소유자 권한이 있는 외부 계정은 구독에서 제거 해야
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 읽기 권한이 있는 외부 계정은 구독에서 제거 해야

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 제거 또는 액세스 권한 조정

Azure는 Azure의 리소스에 역할 기반 액세스 제어 (RBAC) 액세스 권한이 있는 사용자를 관리할 수 있도록를 구현 합니다. Azure Active Directory 및 RBAC를 사용 하 여, 조직 구성 변경 내용을 반영 하도록 사용자 역할을 업데이트할 수 있습니다. 필요한 경우 계정을 로그인하지 못하게 차단하거나 제거하여 Azure 리소스에 대한 액세스 권한을 즉시 제거할 수 있습니다. 이 blueprint 할당 [Azure 정책](../../../policy/overview.md) 감가 상각된 계정 제거를 위해 고려해 야 하는 감사를 정의 합니다.

- 사용 되지 않는 계정은 구독에서 제거 해야
- 소유자 권한이 있는 사용 되지 않는 계정은 구독에서 제거 해야

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b 및 8.2.5 암호 기반 인증

이 blueprint 할당 하 여 강력한 암호를 적용할 수 있습니다 [Azure 정책](../../../policy/overview.md) 최소 수준 및 기타 암호 요구 사항을 적용 하지 않는 Windows Vm을 감사 하는 정의 합니다. 암호 강도 정책을 위반하는 VM을 인식하면 모든 VM 사용자 계정의 암호가 정책을 준수하도록 정정 작업을 수행하는 데 도움이 됩니다.

- [미리 보기]: Audit Windows VMs that do not have a maximum password age of 70 days
- [미리 보기]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [미리 보기]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [미리 보기]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [미리 보기]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [미리 보기]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 및 10.5.4 감사 생성

이 blueprint 할당 하 여 시스템 이벤트 기록 도움이 [Azure 정책](../../../policy/overview.md) Azure 리소스의 로그 설정을 감사 하는 정의 합니다.
진단 로그는 Azure 리소스 내에서 수행된 작업에 대한 인사이트를 제공합니다. Azure 로그는 동기화된 내부 클록을 사용하여 리소스 간 이벤트의 시간 관련 레코드를 만듭니다.

- Azure Security Center에서 SQL 서버에 감사를 모니터링 합니다.
- 진단 설정 감사
- SQL 감사 서버 수준 감사 설정
- SQL 서버에 감사를 배포 합니다.
- 새 Azure Resource Manager 리소스에 저장소 계정은 마이그레이션해야
- 새 Azure Resource Manager 리소스에 가상 컴퓨터를 마이그레이션해야 합니다.

## <a name="1236-and-1237-information-security"></a>12.3.6 및 12.3.7 정보 보안

이 blueprint를 사용 하면 관리 하 고 할당 하 여 네트워크를 제어 [Azure 정책](../../../policy/overview.md) 환경에 대 한 네트워크 위치 및 승인 된 회사 제품을 감사 하는 정의 허용 합니다. 이들은 이러한 정책의 각 정책 매개 변수를 통해 각 회사에서 사용자 지정할 수 있습니다.

- 허용되는 위치
- 허용 되는 리소스 그룹에 대 한 위치

## <a name="next-steps"></a>다음 단계

컨트롤 매핑의 PCI-DSS v3.2.1 blueprint 보신 했으므로 개요 및이 샘플을 배포 하는 방법에 대해 알아보려면 다음 문서를 방문 합니다.

> [!div class="nextstepaction"]
> [PCI DSS v3.2.1 blueprint-개요](./index.md)
> [PCI DSS v3.2.1 blueprint-배포 단계](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
