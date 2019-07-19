---
title: 샘플-PCI DSS v 3.2.1 청사진-컨트롤 매핑
description: 지불 카드 업계 데이터 보안 표준 v 3.2.1 청사진 샘플을 Azure Policy 및 RBAC에 대 한 매핑을 제어 합니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 1e85cb5c06f36e0f8c105ece2c012cfe7cb77bf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226022"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>PCI DSS v 3.2.1 청사진 샘플의 매핑 제어

다음 문서에서는 Azure 청사진 PCI DSS v 3.2.1 청사진 샘플이 PCI DSS v 3.2.1 컨트롤에 매핑되는 방법에 대해 자세히 설명 합니다. 컨트롤에 대 한 자세한 내용은 [PCI DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)를 참조 하세요.

다음은 **PCI DSS v 3.2.1:2018** 컨트롤에 대 한 매핑입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음  **\[미리\] 보기 감사 PCI v 3.2.1:2018 컨트롤을 찾아 선택 하 고 특정 VM 확장을 배포 하 여 감사 요구 사항** 기본 제공 정책 이니셔티브를 지원 합니다.

## <a name="132-and-134-boundary-protection"></a>1.3.2 및 1.3.4 경계 보호

이 청사진을 통해 네트워크 보안 그룹을 허용 규칙으로 모니터링 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 하 여 네트워크를 관리 하 고 제어할 수 있습니다. 너무 관대한 규칙은 원치 않는 네트워크 액세스를 허용할 수 있으며, 검토를 거쳐야 합니다. 이 청사진은 보호 되지 않는 끝점, 응용 프로그램 및 저장소 계정을 모니터링 하는 Azure Policy 정의를 하나 할당 합니다. 방화벽으로 보호되지 않는 엔드포인트 및 애플리케이션과 액세스가 제한되지 않는 스토리지 계정은 정보 시스템 내에 포함된 정보에 대한 원치 않는 액세스를 허용할 수 있습니다.

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- 인터넷 연결 끝점을 통한 액세스는 제한 되어야 합니다.

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a, 4.1, 4.1. g, 4.1. h 및 6.5.3 암호화 보호

이 청사진은 특정 고 암호화 컨트롤을 적용 하 고 약한 암호화 설정의 사용을 감사 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 하 여 고 암호화 컨트롤을 사용할 때 정책을 적용 하는 데 도움이 됩니다. Azure 리소스의 암호화 구성이 최적이 아닐 수 있는 경우를 이해하면 리소스가 정보 보안 정책에 따라 구성되도록 정정 작업을 수행하는 데 도움이 될 수 있습니다. 특히이 청사진에서 할당 한 정책에는 SQL 데이터베이스에 대 한 투명 한 데이터 암호화가 필요 합니다. 저장소 계정 및 automation 계정 변수에 대 한 누락 된 암호화를 감사 합니다. 저장소 계정, 함수 앱, WebApp, API Apps 및 Redis Cache에 대 한 감사 안전 하지 않은 연결을 처리 하 고 암호화 되지 않은 Service Fabric 통신을 감사 하는 정책도 있습니다.

- 함수 앱에 HTTPS를 통해서만 액세스 가능
- 웹 애플리케이션에 HTTPS를 통해서만 액세스 가능
- API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.
- Azure Security Center에서 암호화되지 않은 SQL 데이터베이스 모니터링
- 가상 컴퓨터에 디스크 암호화를 적용 해야 합니다.
- Automation 계정 변수는 암호화 되어야 합니다.
- Redis Cache에 대 한 보안 연결만 사용 하도록 설정 해야 합니다.
- 저장소 계정에 대 한 보안 전송을 사용 하도록 설정 해야 합니다.
- Service Fabric 클러스터는 ClusterProtectionLevel 속성을 EncryptAndSign로 설정 해야 합니다.
- SQL 데이터베이스에 대 한 투명한 데이터 암호화를 사용 하도록 설정 해야 합니다.
- SQL DB 투명한 데이터 암호화 배포

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 및 11.2.1 취약성 검색 및 시스템 업데이트

이 청사진은 Azure의 누락 된 시스템 업데이트, 운영 체제 취약점, SQL 취약성 및 가상 머신 취약점을 모니터링 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 하 여 정보 시스템 취약성을 관리 하는 데 도움이 됩니다. Security Center. Azure Security Center는 배포된 Azure 리소스의 보안 상태에 대한 실시간 인사이트를 가질 수 있도록 하는 보고 기능을 제공합니다.

- Azure Security Center에서 누락된 Endpoint Protection 모니터링
- Windows Server용 기본 Microsoft IaaSAntimalware 확장 배포
- SQL Server에서 위협 감지 배포
- 시스템 업데이트를 컴퓨터에 설치 해야 합니다.
- 컴퓨터에서 보안 구성의 취약성을 재구성 해야 함
- SQL 데이터베이스에 대 한 취약성을 재구성 해야 합니다.
- 취약성 평가 솔루션에서 취약성을 재구성 해야 함

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 및 7.1.3의 의무 분리

Azure 구독 소유자가 한 명만 있으면 관리 중복이 허용되지 않습니다. 반대로, Azure 구독 소유자가 너무 많으면 손상된 소유자 계정을 통한 위반 가능성이 증가할 수 있습니다. 이 청사진을 통해 Azure 구독에 대 한 소유자 수를 감사 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 하 여 적절 한 수의 azure 구독 소유자를 유지 관리할 수 있습니다. 구독 소유자 권한을 관리하면 적절한 임무 분리를 구현하는 데 도움이 될 수 있습니다.

- 구독에 둘 이상의 소유자가 할당 되어 있어야 합니다.
- 구독에 대해 최대 3 개의 소유자를 지정 해야 합니다. 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1 및 8.3.1 권한 있는 액세스 권한 관리

이 청사진은 소유자, 쓰기 및/또는 읽기 권한이 있는 외부 계정을 감사 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 하 고 소유자 및/또는 쓰기 권한이 없는 직원 계정을 감사 하는 권한 있는 액세스 권한을 제한 하 고 제어 하는 데 도움이 됩니다. multi-factor authentication을 사용 하도록 설정 했습니다. Azure는 Azure 리소스에 대한 액세스 권한이 있는 사용자를 관리하기 위해 RBAC(역할 기반 액세스 제어)를 구현합니다. 사용자 지정 RBAC 규칙은 오류가 발생할 가능성이 높기 때문에 사용자 지정 RBAC 규칙이 구현되는 경우를 이해하면 어떤 구현이 필요하고 적절한지 확인하는 데 도움이 될 수 있습니다. 이 청사진에는 SQL Server에 대 한 Azure Active Directory 인증 사용을 감사 하는 [Azure Policy](../../../policy/overview.md) 정의도 할당 됩니다. Azure Active Directory 인증을 사용 하면 권한 관리가 간소화 되 고 데이터베이스 사용자 및 기타 Microsoft의 id 관리가 중앙 집중화 됩니다.  
서비스.
 
- 소유자 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 읽기 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.
- 구독에 대 한 소유자 권한이 있는 계정에서 MFA를 사용 하도록 설정 해야 합니다.
- 구독에 대 한 쓰기 권한이 있는 계정으로 MFA를 사용 하도록 설정 해야 합니다.
- 구독에 대 한 읽기 권한이 있는 계정에서 MFA를 사용 하도록 설정 해야 합니다.
- SQL server에 대 한 Azure Active Directory 관리자를 프로 비전 해야 합니다.
- 사용자 지정 RBAC 규칙의 사용 감사

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 및 8.1.5 최소 권한 및 사용자 액세스 권한 검토

Azure는 Azure의 리소스에 대 한 액세스 권한이 있는 사용자를 관리 하는 데 도움이 되는 RBAC (역할 기반 액세스 제어)를 구현 합니다. Azure Portal을 사용하여 Azure 리소스에 대한 액세스 권한이 있는 사용자 및 해당 사용자의 권한을 검토할 수 있습니다. 이 청사진은 사용 계정 및 승격 된 권한이 있는 외부 계정을 포함 하 여 검토를 위해 우선 순위를 지정 해야 하는 계정을 감사 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 합니다.

- 사용 되지 않는 계정은 구독에서 제거 해야 합니다.
- 소유자 권한이 있는 사용 되지 않는 계정은 구독에서 제거 해야 합니다.
- 소유자 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.
- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 읽기 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 제거 또는 액세스 권한 조정

Azure는 Azure의 리소스에 대 한 액세스 권한이 있는 사용자를 관리 하는 데 도움이 되는 RBAC (역할 기반 액세스 제어)를 구현 합니다. Azure Active Directory 및 RBAC를 사용 하 여 조직 변경 내용을 반영 하도록 사용자 역할을 업데이트할 수 있습니다. 필요한 경우 계정을 로그인하지 못하게 차단하거나 제거하여 Azure 리소스에 대한 액세스 권한을 즉시 제거할 수 있습니다. 이 청사진은 제거를 고려해 야 하는 사용 계정을 감사 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 합니다.

- 사용 되지 않는 계정은 구독에서 제거 해야 합니다.
- 소유자 권한이 있는 사용 되지 않는 계정은 구독에서 제거 해야 합니다.

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3, b, 8.2.4, b 및 8.2.5 암호 기반 인증

이 청사진은 최소 강도 및 기타 암호 요구 사항을 적용 하지 않는 Windows Vm을 감사 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 하 여 강력한 암호를 적용 하는 데 도움이 됩니다. 암호 강도 정책을 위반하는 VM을 인식하면 모든 VM 사용자 계정의 암호가 정책을 준수하도록 정정 작업을 수행하는 데 도움이 됩니다.

- \[미리 보기\]: 최대 암호 사용 기간이 70 일인 Windows Vm 감사
- \[미리 보기\]: 70 일의 최대 암호 사용 기간을 포함 하지 않는 Windows Vm을 감사 하기 위한 요구 사항 배포
- \[미리 보기\]: 최소 암호 길이를 14 자로 제한 하지 않는 Windows Vm 감사
- \[미리 보기\]: 최소 암호 길이를 14 자로 제한 하지 않는 Windows Vm을 감사 하기 위한 요구 사항 배포
- \[미리 보기\]: 이전 24 개 암호를 다시 사용 하도록 허용 하는 Windows Vm 감사
- \[미리 보기\]: 이전 24 개 암호를 다시 사용 하도록 허용 하는 Windows Vm을 감사 하기 위한 요구 사항 배포

## <a name="103-and-1054-audit-generation"></a>10.3 및 10.5.4 감사 생성

이 청사진은 Azure 리소스에 대 한 로그 설정을 감사 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 하 여 시스템 이벤트를 기록 하도록 도와줍니다.
진단 로그는 Azure 리소스 내에서 수행된 작업에 대한 인사이트를 제공합니다. Azure 로그는 동기화된 내부 클록을 사용하여 리소스 간 이벤트의 시간 관련 레코드를 만듭니다.

- Azure Security Center에서 감사 되지 않은 SQL server 모니터링
- 진단 설정 감사
- SQL 감사 서버 수준 감사 설정
- SQL Server에 감사 배포
- 저장소 계정은 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- 가상 컴퓨터를 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.

## <a name="1236-and-1237-information-security"></a>12.3.6 및 12.3.7 정보 보안

이 청사진은 허용 가능한 네트워크 위치와 해당 환경에 대해 허용 되는 승인 된 회사 제품을 감사 하는 [Azure Policy](../../../policy/overview.md) 정의를 할당 하 여 네트워크를 관리 하 고 제어 하는 데 도움이 됩니다. 이러한 각 정책 내에서 정책 매개 변수를 통해 각 회사에서 사용자 지정할 수 있습니다.

- 허용되는 위치
- 리소스 그룹에 허용 되는 위치

## <a name="next-steps"></a>다음 단계

이제 PCI DSS v 3.2.1 청사진의 제어 매핑을 검토 했으므로 다음 문서를 방문 하 여이 샘플을 배포 하는 방법 및 개요에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Pci dss v 3.2.1 청사진-개요](./index.md)
> [PCI-dss v 3.2.1 청사진-배포 단계](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
