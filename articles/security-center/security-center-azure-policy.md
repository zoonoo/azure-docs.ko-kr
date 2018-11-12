---
title: 개별적으로 설정하거나 Azure Policy의 일부분으로 설정할 수 있는 Azure Security Center 보안 정책 | Microsoft Docs
description: 이 문서에서는 Azure Policy 또는 Azure Security Center에서 정책을 설정하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/5/2018
ms.author: terrylan
ms.openlocfilehash: 0b38c6895421b43d6f80e0c34cc23b379a673559
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261947"
---
# <a name="setting-security-policies-in-security-center-or-in-azure-policy"></a>Security Center 또는 Azure Policy에서 보안 정책 설정

이 문서의 내용을 참조하여 Azure Security Center 보안 정책을 구성할 수 있습니다. Azure Scurity Center 정책은 Azure Policy와 통합되므로 특정 구독에 대해 Security Center에서 설정할 수도 있고 [Azure Policy](../azure-policy/azure-policy-introduction.md)에서 설정할 수도 있습니다. Azure Policy에서 정책을 설정하는 경우 여러 관리 그룹과 구독에 걸쳐 정책을 설정할 수 있습니다.

## <a name="what-are-security-policies"></a>보안 정책이란?
보안 정책은 워크로드에서 원하는 구성을 정의하고 회사 또는 규정 보안 요구 사항을 준수하는 데 도움이 됩니다. Azure Security Center에서 Azure 구독에 대한 정책을 정의하고 워크로드의 유형 또는 데이터의 민감도에 맞게 조정할 수 있습니다. 예를 들어 개인 식별 정보와 같이 규제된 데이터를 사용하는 응용 프로그램은 다른 작업보다 높은 수준의 보안이 필요할 수 있습니다. 여러 구독이나 관리 그룹에 걸쳐 정책을 설정하려면 [Azure Policy](../azure-policy/azure-policy-introduction.md)에서 정책을 설정합니다.

> [!NOTE]
> 정책이 여러 개 할당되어 있거나 관리 그룹의 일부인 구독에서 이전에 구성한 보안 정책은 Security Center에서는 흐리게 표시되므로 Azure Policy 페이지를 통해 관리 그룹 수준에서 정책을 관리할 수 있습니다. 

## <a name="how-security-policies-work"></a>보안 정책 작동 방법
Security Center는 각 Azure 구독에 대한 기본 보안 정책을 자동으로 만듭니다. Security Center에서 정책을 편집하거나 Azure Policy를 사용하여 다음을 수행할 수 있습니다.
- 새 정책 정의를 만듭니다.
- 전체 조직 또는 조직 내 비즈니스 단위를 나타낼 수 있는 관리 그룹 및 구독에 정책을 할당합니다.
- 정책 준수를 모니터링합니다.

Azure Policy에 대한 자세한 내용은 [규정 준수를 적용하는 정책 만들기 및 관리](../azure-policy/create-manage-policy.md)를 참조하세요.

Azure 정책은 다음 구성 요소로 구성됩니다.

- **정책**은 규칙
- **이니셔티브**는 정책 컬렉션
- **할당**은 특정 범위(관리 그룹, 구독 또는 리소스 그룹)에 이니셔티브 또는 정책의 적용

리소스는 할당되는 정책으로 계산되고 해당 리소스가 준수하는 정책 수에 따라 규정 준수 비율을 받습니다.

## <a name="who-can-edit-security-policies"></a>보안 정책은 누가 편집할 수 있나요?
Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공하는 RBAC(역할 기반 액세스 제어)를 사용합니다. 사용자가 Security Center를 열면 액세스 권한이 있는 리소스와 관련된 정보만 표시됩니다. 즉, 리소스가 속한 리소스 그룹이나 구독에 대한 소유자, 참가자 또는 읽기 권한자 역할이 사용자에게 할당됩니다. 이러한 역할 외에도 두 개의 특정한 Security Center 역할이 있습니다.

- 보안 읽기 권한자: 권장 사항, 경고, 정책 및 상태를 비롯한 Security Center에 대한 권한을 볼 수 있지만 변경할 수는 없습니다.
- 보안 관리자: 보안 읽기 권한자와 동일한 보기 권한을 갖지만 보안 정책을 업데이트하고 권장 사항 및 경고를 해제할 수 있습니다.

## <a name="edit-security-policies"></a>보안 정책 편집
Security Center에서 각 Azure 구독 및 관리 그룹에 대한 기본 보안 정책을 편집할 수 있습니다. 보안 정책을 수정하려면 구독 또는 포함된 관리 그룹의 소유자, 참가자 또는 보안 관리자여야 합니다. Security Center에서 보안 정책을 보려면

> [!NOTE]
> 여러 정책이 할당되어 있거나 관리 그룹의 일부인 구독에 대해 설정한 정책은 Security Center에서는 흐리게 표시됩니다. 이러한 정책은 [Azure Policy](../azure-policy/azure-policy-introduction.md)에서 편집할 수 있습니다. 

1. **Security Center** 대시보드의 **정책 및 준수** 아래에서 **보안 정책**을 선택합니다. **정책 관리**가 열립니다.

    ![정책 관리 창](./media/security-center-azure-policy/security-center-policies-fig10.png)

  정책 관리는 관리 그룹 구조 뿐만 아니라 관리 그룹, 구독 및 작업 영역 수를 표시합니다.

  > [!NOTE]
  > Security Center 대시보드는 **정책 관리** 아래에 표시된 구독 수보다 **구독 적용 범위** 아래에 더 많은 구독 수를 표시할 수 있습니다. 구독 적용 범위는 표준, 무료 및 "포함 안 된" 구독 수를 표시합니다. "포함 안 된" 구독은 Security Center를 사용하도록 설정되지 않고 **정책 관리** 아래에 표시되지도 않습니다.
  >
  >

  테이블의 열은 다음과 같이 표시됩니다.

 - 정책 이니셔티브 할당 – 구독 또는 관리 그룹에 할당되는 Security Center 기본 제공 정책 및 이니셔티브입니다.
 - 규정 준수 - 관리 그룹, 구독 또는 작업 영역에 대한 전반적인 준수 점수입니다. 점수는 할당의 가중치 평균입니다. 단일 할당의 정책 수 및 할당이 적용되는 리소스 수에서 가중치 평균 비율입니다.

 예를 들어 사용자 구독에 할당된 5개의 정책이 포함된 두 개의 VM 및 한 개의 이니셔티브가 있으면 구독에 10개의 평가가 있습니다. VM 중 하나가 두 개의 정책을 준수하지 않는 경우 구독 할당의 전반적인 준수 점수는 80%입니다.

 - 적용 범위 – 관리 그룹, 구독 또는 작업 영역이 실행되고 있는 무료 또는 평균의 가격 책정 계층을 식별합니다.  Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요.
 - 설정 - 구독에는 **설정 편집** 링크가 있습니다. **설정 편집**을 선택하면 데이터 수집, 가격 책정 계층 및 이메일 알림 같은 구독 설정을 업데이트할 수 있습니다.

2. 보안 정책을 사용하도록 설정하려는 구독 또는 관리 그룹을 선택합니다. **보안 정책**이 열립니다.

3.  **보안 정책** 아래에서 **On**을 선택하여 Security Center가 모니터링하고 권장 사항을 제공하게 하려는 컨트롤을 선택합니다.  Security Center가 해당 컨트롤을 모니터링 못하게 하려는 경우 **Off**를 선택합니다.

    ![정책 구성 요소](./media/security-center-azure-policy/security-policy.png)

4. **저장**을 선택합니다.

## <a name="available-security-policy-definitions"></a>사용 가능한 보안 정책 정의

기본 보안 정책에서 사용할 수 있는 정책 정의를 이해하려면 다음 표를 참조하세요.

| 정책 | 활성화된 정책에서 수행하는 작업 |
| --- | --- |
| 시스템 업데이트 |Windows 업데이트 또는 Windows Server Update Services에서 사용 가능한 보안 및 중요 업데이트의 일일 목록을 검색합니다. 검색된 목록은 가상 머신에 구성된 서비스에 따라 다르며, 누락된 업데이트를 적용하도록 권장합니다. Linux 시스템의 경우 정책은 배포판에서 제공하는 패키지 관리 시스템을 사용하여 사용 가능한 업데이트가 있는 패키지를 확인합니다. 또한 [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md) 가상 머신에서 보안 및 중요 업데이트를 확인합니다. |
| 보안 구성 |운영 체제 구성을 매일 분석하여 가상 머신을 공격에 취약하게 만들 수 있는 문제를 확인합니다. 또한 정책은 이러한 취약점을 해결하는 구성 변경 내용을 권장합니다. 모니터링되는 특정 구성에 대한 자세한 내용은 [권장되는 기준 목록](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)을 참조하세요. (현재는 Windows Server 2016이 완전히 지원되지 않습니다.) |
| Endpoint Protection |바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하도록 모든 Windows VM(가상 머신)에 엔드포인트 보호를 설정하는 것이 좋습니다. |
| 디스크 암호화 |모든 가상 머신에서 디스크 암호화를 사용하여 휴지 상태의 데이터 보호 기능을 향상시키는 것이 좋습니다. |
| 네트워크 보안 그룹 |공용 엔드포인트를 사용하여 VM에 대한 인바운드 및 아웃바운드 트래픽을 제어할 수 있도록 [네트워크 보안 그룹](../virtual-network/security-overview.md) 을 구성하는 것이 좋습니다. 서브넷에 대해 구성된 네트워크 보안 그룹은 별도로 지정하지 않는 한 모든 가상 머신 네트워크 인터페이스에 상속됩니다. 이 정책은 네트워크 보안 그룹이 구성되었는지 확인하는 것 외에도 인바운드 보안 규칙을 평가하여 들어오는 트래픽을 허용하는 규칙을 식별합니다. |
| 웹 응용 프로그램 방화벽 |다음 항목 중 하나가 true인 경우 가상 머신에 웹 응용 프로그램 방화벽을 설정하는 것이 좋습니다. <ul><li>[인스턴스 수준 공용 IP](../virtual-network/virtual-networks-instance-level-public-ip.md)를 사용하고, 연결된 네트워크 보안 그룹에 대한 인바운드 보안 규칙에서 80/443 포트에 대한 액세스를 허용하도록 구성합니다.</li><li>부하 분산된 IP를 사용하고, 연결된 부하 분산 및 인바운드 NAT(Network Address Translation) 규칙에서 80/443 포트에 대한 액세스를 허용하도록 구성합니다. 자세한 내용은 [부하 분산 장치에 대한 Azure Resource Manager 지원](../load-balancer/load-balancer-arm.md)을 참조하세요.</li> |
| 차세대 방화벽 |Azure에 기본 제공되는 네트워크 보안 그룹 외에도 네트워크 보호 기능을 확장합니다. Security Center에서 차세대 방화벽이 권장되는 배포를 검색한 다음 가상 어플라이언스를 설정할 수 있습니다. |
| SQL 감사 및 위협 검색 |조사를 위해 Azure 데이터베이스에 대한 액세스 감사를 규정 준수 및 고급 위협 검색에 사용하도록 설정하는 것이 좋습니다. |
| SQL 암호화 |미사용 데이터 암호화를 Azure SQL 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일에 사용하도록 설정하는 것이 좋습니다. 데이터가 위반되는 경우에도 해당 데이터를 읽을 수 없습니다. |
| 취약점 평가 |VM에 취약점 평가 솔루션을 설치하는 것이 좋습니다. |
| 저장소 암호화 |이 기능은 현재 Azure Blob 저장소 및 Azure Files에서 사용할 수 있습니다. 저장소 서비스 암호화를 사용하도록 설정하면 새 데이터만 암호화되고 이 저장소 계정의 기존 파일은 암호화되지 않은 상태로 유지됩니다. |
| JIT 네트워크 액세스 |JIT(Just-In-Time)를 사용하도록 설정하면 Security Center에서 네트워크 보안 그룹 규칙을 만들어 Azure VM에 대한 인바운드 트래픽을 잠급니다. VM에서 인바운드 트래픽을 잠글 포트를 선택합니다. 자세한 내용은 [Just-In-Time를 사용하여 가상 머신 액세스 관리](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)를 참조하세요. |

## <a name="management-groups"></a>관리 그룹
조직에 구독이 많으면 구독에 대한 액세스, 정책 및 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. Azure 관리 그룹은 구독 상위 수준의 범위를 제공합니다. "관리 그룹"이라는 컨테이너에 구독을 구성하고 거버넌스 정책을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 정책을 자동으로 상속합니다. 각 디렉터리에는 "루트" 관리 그룹이라고 하는 단일 최상위 관리 그룹이 부여됩니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 이 루트 관리 그룹은 전역 정책 및 RBAC 할당을 디렉터리 수준에서 적용하는 것을 허용합니다. Azure Security Center용 관리 그룹을 설정하려면 [Azure Security Center에 대한 테넌트 수준 표시 유형 얻기](security-center-management-groups.md) 문서의 지침을 따릅니다. 

> [!NOTE]
> 관리 그룹 및 구독의 계층 구조를 이해하는 것은 중요합니다. 관리 그룹, 루트 관리 및 관리 그룹 액세스에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/index.md#root-management-group-for-each-directory)을 참조하세요.
>
>


## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 보안 정책을 구성하는 방법을 알아보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md): Azure Security Center에 대한 디자인 고려 사항을 계획하고 이해하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에 대한 테넌트 수준 표시 유형 얻기](security-center-management-groups.md): Azure Security Center에 대한 관리 그룹을 설정하는 방법을 알아봅니다. 
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답을 가져옵니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

Azure Policy에 대해 자세히 알아보려면 [Azure Policy란?](../azure-policy/azure-policy-introduction.md)을 참조하세요.
