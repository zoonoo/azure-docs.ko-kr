---
title: Azure Stack 1808 업데이트 | Microsoft Docs
description: 새로운 1808 업데이트의 알려진된 문제를 포함 하 여 Azure Stack 통합 시스템 및 업데이트를 다운로드 하는 위치에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 1ca305ab88e30c911bbded1e5ff97162e12f7652
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429068"
---
# <a name="azure-stack-1808-update"></a>Azure Stack 1808 업데이트

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 1808 업데이트 패키지의 내용을 설명 합니다. 향상 된 기능, 수정 및 알려진된 문제가이 버전의 Azure Stack에 대 한 업데이트 패키지에 포함 됩니다. 이 문서에서는 또한 업데이트를 다운로드할 수 있도록 링크가 포함 되어 있습니다. 알려진된 문제는 업데이트 프로세스에 직접 관련 된 문제 및 문제 (설치 후) 빌드를 사용 하 여으로 구분 됩니다.

> [!IMPORTANT]  
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조

Azure Stack 1808 업데이트 빌드 번호는 **1.1808.0.97**합니다.  

### <a name="new-features"></a>새로운 기능

이 업데이트는 Azure Stack에 대 한 다음과 같은 개선 사항이 포함 되어 있습니다.

<!--  2682594   | IS  --> 
- **모든 Azure Stack 환경에는 이제 utc (협정 세계시) 시간 영역 형식을 사용합니다.**  모든 로그 데이터 및 관련된 정보를 지금 UTC 형식으로 표시 됩니다. UTC를 사용 하 여 설치 되지 않은 이전 버전에서 업데이트 하는 경우 환경의 UTC를 사용 하도록 업데이트 됩니다. 

<!-- 2437250  | IS  ASDK --> 
- **Managed Disks는 지원 합니다.** 이제 Azure Stack virtual machines 및 가상 머신 확장 집합에서 Managed Disks를 사용할 수 있습니다. 자세한 내용은 [Azure Stack의 Managed Disks: 차이점 및 고려 사항](/azure/azure-stack/user/azure-stack-managed-disk-considerations)합니다.

<!-- 2563799  | IS  ASDK --> 
- **Azure Monitor** Azure에서 Azure Monitor와 같은 Azure Stack에서 Azure Monitor 대부분의 서비스에 대 한 기본 수준의 인프라 메트릭과 로그 제공합니다. 자세한 내용은 [Azure Stack에서 Azure Monitor](/azure/azure-stack/user/azure-stack-metrics-azure-data)합니다.

<!-- 2487932| IS --> 
- **확장 호스트에 대 한 준비**합니다. 확장 호스트 필요한 TCP/IP 포트의 수를 줄여 Azure Stack 보안 하는 데 사용할 수 있습니다. 1808 업데이트를 준비, Azure Stack에 확장 호스트에 대 한 준비 수 있습니다. 자세한 내용은 [Azure Stack에 대 한 확장 호스트에 대 한 준비](/azure/azure-stack/azure-stack-extension-host-prepare)합니다.

<!-- IS --> 
- **Virtual Machine Scale Sets에 대 한 갤러리 항목은 이제 기본 제공**합니다. Virtual Machine Scale Set 갤러리 항목의는 이제 가능 사용자 및 관리자 포털에서 다운로드 하지 않고도 합니다.  1808를 업그레이드할 경우 업그레이드 완료 시 제공 됩니다.  

<!-- IS, ASDK --> 
- **가상 머신 확장 집합 크기 조정**합니다. 포털을 사용할 수 있습니다 [가상 머신 확장 집합 확장](azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).    

<!-- 2489570 | IS ASDK--> 
- **사용자 지정 IPSec/IKE 정책 구성에 대 한 지원을** 에 대 한 [VPN 게이트웨이가 Azure Stack에서](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways)합니다.

<!-- | IS ASDK--> 
- **Kubernetes 마켓플레이스 항목**합니다. 이제 사용 하 여 Kubernetes 클러스터를 배포할 수 있습니다 합니다 [Kubernetes 마켓플레이스 항목](azure-stack-solution-template-kubernetes-cluster-add.md)합니다. 사용자는 Kubernetes 항목을 선택 하 고 Azure Stack에 Kubernetes 클러스터를 배포 하는 몇 가지 매개 변수를 채울 수 있습니다. 템플릿의 목적은 간단 하 게 몇 가지 단계에서 개발/테스트 Kubernetes 배포를 설정 하는 사용자에 게입니다.

<!-- | IS ASDK--> 
- **블록 체인 템플릿**합니다. 이제 실행할 수 있습니다 [Ethereum 컨소시엄 배포](azure-stack-ethereum.md) Azure Stack에서. 세 개의 새로운 템플릿이 있습니다 합니다 [Azure Stack 빠른 시작 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates)합니다. 사용자가 배포를 한 지식이 별로 없더라도 Azure 및 Ethereum 다중 멤버 컨소시엄 Ethereum 네트워크를 구성할 수 있습니다. 템플릿의 목적은 간단 하 게 몇 가지 단계에서 개발/테스트 Blockchain 배포를 설정 하는 사용자에 게입니다.

<!-- | IS ASDK--> 
- **API 버전 프로필 2017-03-09-프로필 2018-03-01-하이브리드로 업데이트 되었습니다**합니다. 프로필 API는 Azure 리소스 공급자 및 Azure REST 끝점에 대 한 API 버전을 지정합니다. 프로필에 대 한 자세한 내용은 참조 하세요. [Azure Stack에서 관리 하는 API 버전 프로필](/azure/azure-stack/user/azure-stack-version-profiles)합니다.

 ### <a name="fixed-issues"></a>해결된 문제
<!-- IS ASDK--> 
- 장애 도메인 및 업데이트 도메인 1의 집합에서 발생 하는 포털에서 가용성 집합을 만드는 문제를 해결 했습니다. 

<!-- IS ASDK --> 
- 가상 머신 확장 집합의 크기를 조정 하는 설정을 포털에서 사용할 수 있습니다.  

<!-- 2494144- IS, ASDK --> 
- 배포에 대 한 VM 크기를 선택할 때 나타나는에서 일부 F 시리즈 가상 머신 크기를 막는 문제가 해결 되었습니다. 

<!-- IS, ASDK --> 
- Virtual machines 등 최적화를 만들 때 성능에 대 한 향상 된 기본 저장소를 사용 합니다.

- **다양 한 수정** 성능, 안정성, 보안 및 Azure Stack에서 사용 되는 운영 체제에 대 한 합니다.


### <a name="changes"></a>변경 내용
<!-- 1697698  | IS, ASDK --> 
- *빠른 시작 자습서* 온라인 Azure Stack 설명서에서 관련 문서에 사용자 포털 대시보드에 현재 연결에서.

<!-- 2515955   | IS ,ASDK--> 
- *모든 서비스* 바꿉니다 *더 많은 서비스* Azure Stack 관리자 및 사용자 포털에 있습니다. 이제 사용할 수 있습니다 *모든 서비스* Azure 포털에서 수행한 동일한 방식으로 Azure Stack 포털에 이동 하는 대신 합니다.

<!-- TBD | IS, ASDK --> 
- *+ 리소스 만들기* 바꿉니다 *+ 새로 만들기* Azure Stack 관리자 및 사용자 포털에 있습니다.  이제 사용할 수 있습니다 *+ 리소스 만들기* Azure 포털에서 수행한 동일한 방식으로 Azure Stack 포털에 이동 하는 대신 합니다.  

<!--  TBD – IS, ASDK --> 
- *기본 A* 가상 머신 크기에 대 한 현재 퇴직 [virtual machine scale sets 만들기](azure-stack-compute-add-scalesets.md) (VMSS) 포털을 통해. 이 크기를 사용 하 여 VMSS를 만들려면, PowerShell 또는 템플릿을 사용 합니다.  

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

이 업데이트에는 다음 업데이트를 설치합니다.  

- [CVE-2018-0952 지역](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

이러한 취약점에 대 한 자세한 내용은 위의 링크를 클릭 하거나 Microsoft 기술 자료 문서를 참조 하세요 [4343887](https://support.microsoft.com/help/4343887)합니다.

이 업데이트에 설명 된 L1 터미널 오류 (L1TF)로 알려진 투기적 실행 쪽 채널 취약성에 대 한 완화 포함 된 [Microsoft 보안 공지 ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018)합니다.  

### <a name="prerequisites"></a>필수 조건

- Azure Stack을 설치 [1807 업데이트](azure-stack-update-1807.md) Azure Stack 1808 업데이트를 적용 하기 전에 합니다. 

- 사용 가능한 최신 설치 [업데이트 또는 핫픽스 1807 버전용](azure-stack-update-1807.md#post-update-steps)합니다.  
  > [!TIP]  
  > 다음을 구독할 *RRS* 또는 *Atom* Azure Stack 핫픽스를 피드 합니다.
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- 이 업데이트의 설치를 시작 하기 전에 실행할 [테스트 AzureStack](azure-stack-diagnostic-test.md) 에 Azure Stack의 상태를 확인 하 고 발견 된 작동 문제를 해결 하려면 다음 매개 변수를 사용 하 여 모든 경고 및 오류를 포함 합니다. 또한 활성 경고를 검토 하 고 작업을 필요로 하는 해결 합니다.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```   

### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스를 사용 하 여 알려진된 문제

- 실행할 때 [테스트 AzureStack](azure-stack-diagnostic-test.md) 1808 업데이트 후 베이스 보드 관리 컨트롤러 (BMC)에서 경고 메시지가 표시 됩니다. 이 경고를 안전 하 게 무시할 수 있습니다.

<!-- 2468613 - IS --> 
- 이 업데이트를 설치 하는 동안 경고 제목으로 표시 될 수 있습니다 *오류 – FaultType UserAccounts.New 템플릿을 누락 되었습니다.*  이러한 경고를 안전 하 게 무시할 수 있습니다. 이러한 경고는이 업데이트의 설치가 완료 된 후 자동으로 종료 됩니다.

<!-- 2489559 - IS --> 
- 이 업데이트를 설치 하는 동안 가상 컴퓨터를 만들 하려고 하지 마십시오. 업데이트를 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md#plan-for-updates)합니다.

<!-- 2830461 - IS --> 
- 특정 상황에서 업데이트에 주의가 필요한 경우 해당 경고 하지 생성 될 수 있습니다. 정확한 상태를 포털에 여전히 반영 됩니다 및 영향을 받지 않습니다.

### <a name="post-update-steps"></a>업데이트 후 단계
이 업데이트의 설치가 끝나면 모든 적용 가능한 핫픽스를 설치 합니다. 자세한 내용은 다음 기술 자료 문서를 볼 뿐만 아니라 우리 [서비스 정책](azure-stack-servicing-policy.md)합니다. 
- [KB 4468920 – Azure Stack 핫픽스 Azure Stack 핫픽스 1.1808.5.110](https://support.microsoft.com/help/4468920/)


## <a name="known-issues-post-installation"></a>알려진된 문제 (사후 설치)

이 빌드 버전에 대 한 설치 후 알려진된 문제는 다음과 같습니다.

### <a name="portal"></a>포털

- Azure Stack 기술 설명서는 최신 릴리스의 Azure Stack에 중점을 둡니다. 릴리스 간의 포털 변경으로 인해 Azure Stack 포털을 사용 하는 경우 표시 되는 내용 설명서에 표시 되는 내용에서 달라질 수 있습니다. 

<!-- TBD - IS ASDK --> 
- 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 클릭 **대시보드 편집**, 한 다음 마우스 오른쪽 단추로 **기본 상태로 재설정**합니다.

<!-- 2930718 - IS ASDK --> 
- 관리자 포털에서 사용자 구독 하는 경우의 세부 정보 블레이드를 닫고 클릭 한 후에 액세스할 때 **최근**, 사용자 구독 이름이 표시 되지 않습니다.

<!-- 3060156 - IS ASDK --> 
- 포털 설정을 클릭 하 고 선택 하면 관리자와 사용자 포털 **모든 설정 및 개인 대시보드 삭제** 예상 대로 작동 하지 않습니다. 오류 알림이 표시 됩니다. 

<!-- 2930799 - IS ASDK --> 
- 관리자와 사용자 포털에서 아래 **모든 서비스**, 자산 **DDoS 보호 계획** 올바르게 나열 됩니다. Azure Stack에서 실제로 사용할 수 없는 합니다. 만들 하려고 하면 포털 마켓플레이스 항목을 만들 수 있는지를 나타내는 오류가 표시 됩니다. 

<!-- 2930820 - IS ASDK --> 
- 관리자와 사용자 포털에서 "Docker"를 검색 하는 경우 항목을 올바르게 반환 됩니다. Azure Stack에서 실제로 사용할 수 없는 합니다. 만들 하려고 하면 오류가 표시 된 블레이드가 표시 됩니다. 

<!-- 2967387 – IS, ASDK --> 
- Azure Stack 관리자 또는 사용자 포털에 로그인 하는 데 사용할 계정으로 표시 됩니다 **알 수 없는 사용자**합니다. 이런 계정에 없는 경우 하나는 *첫 번째* 또는 *마지막* 이름을 지정 합니다. 이 문제를 해결 하려면 첫 번째 또는 마지막 이름을 제공 하기 위해 사용자 계정을 편집 합니다. 그런 다음 로그 아웃 하 고 그런 다음 포털에 다시 로그인 해야 합니다. 

<!--  2873083 - IS ASDK --> 
-  가상 머신 확장 집합을 만드는 포털을 사용할 때 (VMSS)를 설정 합니다 *인스턴스 크기* Internet Explorer를 사용 하는 경우 드롭다운에서 올바르게 로드 합니다. 이 문제를 해결 하려면 포털을 사용 하 여 VMSS를 만들려고 하는 동안 다른 브라우저를 사용 합니다.  

<!-- 2931230 – IS  ASDK --> 
- 사용자 구독에서 해당 계획을 제거 하는 경우에는 추가 요금제는 사용자 구독에 추가 되는 계획을 삭제할 수 없습니다. 계획에는 추가 요금제를 참조 하는 구독도 삭제 될 때까지 상태로 유지 됩니다. 

<!--2760466 – IS  ASDK --> 
- 이 버전을 실행 하는 새 Azure Stack 환경에 설치한 경우이 경고는 나타냅니다 *활성화 필요* 표시 되지 않을 수 있습니다. [활성화](azure-stack-registration.md) 마켓플레이스 배포를 사용 하려면 먼저 필요 합니다.  

<!-- TBD - IS ASDK --> 
- 1804 버전을 사용 하 여 도입 된 관리 구독 유형에 사용할 수 없습니다. 구독 유형은 **구독을 계량**, 및 **소비 구독**합니다. 이러한 구독 유형에 1804 버전부터 새 Azure Stack 환경에서 표시 되지만 아직 사용할 준비가 완료 됩니다. 계속 사용 해야 합니다 **기본 공급자** 구독 유형입니다.

<!-- TBD - IS ASDK --> 
- 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

<!-- TBD - IS ASDK --> 
- Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.


### <a name="health-and-monitoring"></a>상태 및 모니터링

<!-- TBD - IS -->
- 반복적으로 나타나고 나타났다가 사라집니다 Azure Stack 시스템에 다음 경고가 표시 될 수 있습니다.
   - *인프라 역할 인스턴스를 사용할 수 없음*
   - *확장 단위 노드에 오프 라인 상태입니다.*
   
  실행 하십시오 합니다 [테스트 AzureStack](azure-stack-diagnostic-test.md) 인프라 역할 인스턴스의 상태를 확인 하 고 단위 노드의 크기를 조정 하는 cmdlet입니다. 문제가 감지 되 면 [테스트 AzureStack](azure-stack-diagnostic-test.md), 이러한 경고를 무시할 수 있습니다. 문제가 감지 될 경우에 인프라 역할 인스턴스 또는 관리 포털 또는 PowerShell을 사용 하 여 노드를 시작 하려면 시작할 수 있습니다.

<!-- 1264761 - IS ASDK --> 
- 에 대 한 경고를 표시 될 수 있습니다 합니다 **상태 컨트롤러** 다음 세부 정보는 구성 요소:  

   경고 # 1:
   - 비정상 인프라 역할 이름:
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 검색 프로그램 사용할 수 없는 경우 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  # 2를 경고 합니다.
   - 비정상 인프라 역할 이름:
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  모두 경고를 안전 하 게 무시할 수 있습니다 하 고 시간이 지남에 따라 자동으로 닫을 수 있습니다.  


<!-- 2812138 | IS --> 
- 에 대 한 경고가 표시 될 수 있습니다 **저장소** 다음 세부 정보를 포함 하는 구성 요소:

   - 이름: 저장소 서비스의 내부 통신 오류  
   - 심각도: 위험  
   - 구성 요소: 저장소  
   - 설명: 다음 노드에 요청을 보낼 때 저장소 서비스의 내부 통신 오류가 발생 했습니다.  

    경고를 안전 하 게 무시할 수 있지만 경고를 수동으로 종결 해야 합니다.

<!-- 2368581 - IS. ASDK --> 
- 메모리 부족 경고를 받게 테 넌 트 가상 머신을 사용 하 여 배포 하는 데 실패 한 경우 Azure Stack 운영자를 **패브릭 VM 만들기 오류**, 사용 가능한 메모리가 부족 합니다. Azure Stack 스탬프는 불가능 합니다. 사용 된 [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 워크 로드에 대 한 사용 가능한 용량을 가장 잘 알아야 합니다.


### <a name="compute"></a>컴퓨팅

<!-- 3099544 – IS, ASDK --> 
- Azure Stack 포털을 사용 하 여 새 가상 컴퓨터 (VM)를 만든 경우 VM 크기를 선택 하면 u s D/월 열이 사용 하 여 표시 됩니다는 **사용할 수 없습니다** 메시지입니다. 이 열이 나타나지 않아야 합니다. 표시 된 VM 가격 책정 열에서에서 지원 되지 않습니다 Azure Stack.

<!-- 3090289 – IS, ASDK --> 
- 업데이트 된 1808를 적용 한 후, Managed Disks를 사용 하 여 Vm을 배포 하는 경우 다음과 같은 문제가 발생할 수 있습니다.

   1. Managed Disks를 사용 하 여 VM 배포 1808 업데이트 하기 전에 구독을 만든 경우 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면 각 구독에 대해 다음이 단계를 수행 합니다.
      1. 테 넌 트 포털에서로 이동 **구독** 구독을 찾습니다. 클릭 **리소스 공급자**, 클릭 **Microsoft.Compute**를 클릭 하 고 **re-register**합니다.
      2. 로 동일한 구독에 따라 **액세스 제어 (IAM)**, 되어 있는지 확인 하 고 **Azure Stack – Managed Disk** 나열 됩니다.
   2. 다중 테 넌 트 환경에 구성한 게스트 디렉터리와 연결 된 구독에서 Vm을 배포할 내부 오류 메시지와 함께 실패할 수 있습니다. 이 오류를 해결 하려면 다음이 단계를 수행 합니다.
      1. 적용 된 [1808 Azure Stack 핫픽스](https://support.microsoft.com/help/4468920/)합니다.
      2. 단계를 따릅니다 [이 문서에서는](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) 각 게스트 디렉터리를 다시 구성 합니다.
      
<!-- 3179561 - IS --> 
- 에 설명 된 대로 관리 되는 디스크 사용량이 시간에 보고 합니다 [Azure Stack 사용 FAQ](azure-stack-usage-related-faq.md#managed-disks)합니다. 그러나 Azure Stack 청구의 월별 가격 대신 사용 될 수 있습니다 가져오기 올바르게 비용이 청구 되지 않습니다 또는 그 27 년 9 월 이전에 Managed Disks 사용량에 대 한 합니다. 에서는 일시적으로 일시 중지 했 요금 Managed Disks에 대 한 청구 문제가 해결 될 때까지 27를 년 9 월 후. 하면 요금이 청구 되지 올바르게 Managed Disks 사용량에 대 한, Microsoft 대금 청구 지원에 문의 하세요.
Azure Stack 사용량 Api에서에서 생성 된 사용 현황 보고서 올바른 수량을 표시 하 고 사용할 수 있습니다.

<!-- 2869209 – IS, ASDK --> 
- 사용 하는 경우는 [ **추가 AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0)를 사용 해야 합니다는 **-OsUri** storage 계정과 디스크 업로드 되는 URI 매개 변수입니다. 다음 오류로 인해 cmdlet이 실패 하면 디스크의 로컬 경로 사용 하는 경우: *장기 실행 작업 상태 '실패'를 사용 하 여 실패 한*합니다. 

<!--  2966665 – IS, ASDK --> 
- 디스크 virtual machines (DS, DSv2, Fs, Fs_V2) 실패 오류가 발생 하 여 관리 되는 프리미엄 크기에 SSD 데이터 디스크를 연결: *가상 머신 'vmname' 오류에 대 한 디스크를 업데이트 하지 못했습니다: 요청한 저장소 계정 유형 때문에 작업을 수행할 수 없습니다 ' VM 크기에 대 한 Premium_LRS'를 사용할 수 없습니다 ' Standard_DS/Ds_V2/FS/Fs_v2)*

   이 문제를 해결 하려면 사용 하 여 *Standard_LRS* 대신 데이터 디스크 *Premium_LRS 디스크*합니다. 이용 *Standard_LRS* 데이터 디스크 IOPs 또는 비용을 변경 하지 않습니다. 

<!--  2795678 – IS, ASDK --> 
- 가상 머신 (VM)를 만들려면 포털 (DS, Ds_v2, FS, FSv2) 프리미엄 VM 크기에서를 사용 하면 표준 저장소 계정에 VM이 생성 됩니다. 표준 저장소 계정 만드는 영향을 주지 않습니다 기능적으로 IOPs 또는 대금 청구 합니다. 

   라는 경고를 무시 해도: *에 프리미엄 디스크를 지 원하는 크기로 표준 디스크를 사용 하기로 선택 하셨습니다. 이 운영 체제 성능에 영향을 줄 수 있으며 권장 되지 않습니다. Premium storage (SSD)를 대신 사용 하는 것이 좋습니다.*

<!-- 2967447 - IS, ASDK --> 
- 가상 머신 확장 집합 (VMSS) 환경을 만드는 7.2 CentOS 기반 배포에 대 한 옵션으로 제공 합니다. 해당 이미지를 Azure Stack에서 사용할 수 없는 때문에 배포에 대 한 다른 OS를 선택 하거나 연산자가 marketplace에서 배포 하기 전에 다운로드 된 다른 CentOS 이미지를 지정 하는 Azure Resource Manager 템플릿을 사용 합니다.  

<!-- 2724873 - IS --> 
- PowerShell cmdlet을 사용 하는 경우 **시작 AzsScaleUnitNode** 하거나 **중지 AzsScaleunitNode** 배율 단위를 관리 하려면 시작 또는 중지 확장 단위를 첫 번째 시도가 실패할 수 있습니다. 첫 번째 실행에 실패 하면 cmdlet은 cmdlet을 두 번 실행 합니다. 작업을 완료 하 고 두 번째 실행 성공 해야 합니다. 

<!-- TBD - IS ASDK --> 
- Azure Stack 사용자 포털에서 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

<!-- TBD - IS ASDK --> 
- VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

<!-- 1662991 IS ASDK --> 
- Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다.  

<!-- 2724961- IS ASDK --> 
- 등록 하는 경우는 **Microsoft.Insight** 리소스 공급자 구독 설정에서 게스트 OS 진단 사용 하도록 설정 된 Windows VM 만들기, VM 개요 페이지에서 CPU 백분율 차트 메트릭 데이터를 표시할 수 없습니다.

   VM에 대 한 CPU 백분율 차트를 찾으려면에서로 이동 합니다 **메트릭을** 블레이드에서 살펴보고, 지원 되는 모든 Windows VM 게스트 메트릭을 합니다.



### <a name="networking"></a>네트워킹  

<!-- 1766332 - IS ASDK --> 
- 아래 **네트워킹**를 클릭 하면 **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 만 **경로 기반** 옵션은 Azure Stack에서 지원 됩니다.

<!-- 1902460 - IS ASDK --> 
- Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

<!-- 16309153 - IS ASDK --> 
- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.

<!-- 2702741 -  IS ASDK --> 
- 동적 할당 메서드를 사용 하 여 배포 되는 공용 Ip 실행 중지-할당 취소 후에 유지 되도록 보장 되지 않습니다.

<!-- 2529607 - IS ASDK --> 
- Azure Stack 하는 동안 *암호 회전*에는 공용 IP 주소에 연결할 수 없는 2 ~ 5 분에 대 한 간격이 있습니다.

<!-- 2664148 - IS ASDK --> 
- 여기서 테 넌 트에 액세스 하는 가상 컴퓨터는 S2S VPN 터널을 사용 하 여 시나리오에서는 온-프레미스 서브넷이 게이트웨이 이미 만든 후 로컬 네트워크 게이트웨이를 추가한 경우 연결 시도가 실패 하는 있는 경우를 발생할 수 있습니다. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

<!-- 2489178 - IS ASDK --> 
- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.



### <a name="usage"></a>사용 현황  
<!-- TBD - IS ASDK --> 
- 사용량 공용 IP 주소 사용 측정기 데이터를 보여 줍니다 동일 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>업데이트 다운로드
Azure Stack 1808 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.
  

## <a name="next-steps"></a>다음 단계
- Azure Stack 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 해야 할 새로운 서비스의 정책을 참조 하세요 [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)합니다.  
- 권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.  
- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.  
- Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.  
