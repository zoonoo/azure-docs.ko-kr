---
title: Azure Stack 1809 업데이트 | Microsoft Docs
description: 새로운 1809 업데이트의 알려진된 문제를 포함 하 여 Azure Stack 통합 시스템 및 업데이트를 다운로드 하는 위치에 알아봅니다.
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
ms.date: 11/01/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 00ce57dbff749d4ee906e0e7dae1d828ef85326f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962064"
---
# <a name="azure-stack-1809-update"></a>Azure Stack 1809 업데이트

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 1809 업데이트 패키지의 내용을 설명 합니다. 향상 된 기능, 수정 및 알려진된 문제가이 버전의 Azure Stack에 대 한 업데이트 패키지에 포함 됩니다. 이 문서에서는 또한 업데이트를 다운로드할 수 있도록 링크가 포함 되어 있습니다. 알려진된 문제는 업데이트 프로세스에 직접 관련 된 문제 및 문제 (설치 후) 빌드를 사용 하 여으로 구분 됩니다.

> [!IMPORTANT]  
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조

Azure Stack 1809 업데이트 빌드 번호는 **1.1809.0.90**합니다.  

### <a name="new-features"></a>새로운 기능

이 업데이트는 Azure Stack에 대 한 다음과 같은 향상 된 기능을 포함합니다.

- 이 릴리스에서 Azure Stack 통합 4-16 노드의 시스템 지원 구성 합니다. 사용할 수 있습니다는 [Azure Stack Capacity Planner](http://aka.ms/azstackcapacityplanner) Azure Stack 용량 및 구성에 대 한 계획 수립에 있도록 합니다.

- <!--  2712869   | IS  ASDK -->  **Azure Stack syslog 클라이언트 (일반 공급)** 이 클라이언트에는 감사, 경고 및 Azure Stack 인프라를 syslog 서버 또는 보안 정보 및 이벤트 관리 (SIEM) 소프트웨어와 관련 된 보안 로그를 전달할 수 있도록 Azure Stack에 외부입니다. Syslog 클라이언트는 이제 syslog 서버 수신 대기 하는 포트 지정을 지원 합니다.

   이 릴리스에서 syslog 클라이언트는 일반 공급 하 고 프로덕션 환경에서 사용할 수 있습니다.

   자세한 내용은 [Azure Stack syslog 전달을](azure-stack-integrate-security.md)합니다.

- 이제 [등록 리소스 이동](azure-stack-registration.md#move-a-registration-resource) 다시 등록 하지 않고 리소스 그룹 간에 Azure에서. 클라우드 솔루션 공급자 (Csp) 간에 이동할 수도 등록 리소스 구독으로 새 일정과 이전 구독을 모두 같은 CSP 파트너 ID에 매핑됩니다. 기존 고객 테 넌 트 매핑을 영향을 주지 않습니다. 

### <a name="fixed-issues"></a>해결된 문제

<!-- TBD - IS ASDK -->
- 포털에서 무료/사용 된 용량을 보고 메모리 차트 정확 하 게 됩니다. 이제 얼마나 많은 Vm을 만들 수 있다면 더 안정적으로 예측할 수 있습니다.

<!-- TBD - IS ASDK --> 
- Azure Stack 사용자 포털에서 가상 컴퓨터를 만든이 및 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 하는 문제가 수정 되었습니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- 다음과 같은 관리 디스크 문제가 1809에서 고정 되어 있으며는 1808에도 수정 되어 [Azure Stack 핫픽스 1.1808.5.110](https://support.microsoft.com/help/4468920/): 

   <!--  2966665 – IS, ASDK --> 
   - 관리 디스크 virtual machines (DS, DSv2, Fs, Fs_V2) 오류로 인해 실패 하는 프리미엄 크기는 연결 SSD 데이터 디스크에서 문제를 해결 했습니다.: *가상 머신 'vmname' 오류에 대 한 디스크를 업데이트 하지 못했습니다: 요청 때문에 작업을 수행할 수 없습니다 저장소 계정 유형 VM 크기에 대 한 'Premium_LRS'를 사용할 수 없습니다 ' Standard_DS/Ds_V2/FS/Fs_v2)* 합니다. 
   
   - 사용 하 여 관리 디스크 VM을 만드는 **createOption**: **Attach** 다음 오류로 인해 실패: *장기 실행 작업 상태 '실패'를 사용 하 여 실패 했습니다. 추가 정보: '내부 실행 오류가 발생 했습니다.'*
   ErrorCode: InternalExecutionError ErrorMessage: 내부 실행 오류가 발생 했습니다.
   
   이제이 문제가 해결 되었습니다.

- <!-- 2702741 -  IS, ASDK --> 동적 할당을 사용 하 여 배포 된 공용 Ip는 메서드가 없습니다. 문제 해결된 실행 중지-할당 취소 후에 유지 되도록 보장 합니다. 이제 보존 됩니다.

- <!-- 3078022 - IS, ASDK --> VM 중지-할당 해제 하기 전에 되었던 1808 경우 수 없는 다시 할당 1808 업데이트 후 합니다.  이 문제는 1809에서 해결 됩니다. 이 문제가 해결 1809에서이 상태에 있으며 시작할 수 없습니다. 인스턴스를 시작할 수 있습니다. 또한 수정 반복에서이 문제를 방지합니다.

### <a name="changes"></a>변경 내용

없음.

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

이 업데이트에는 다음 보안 업데이트를 설치합니다.  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

이러한 취약점에 대 한 자세한 내용은 위의 링크를 클릭 하거나 Microsoft 기술 자료 문서를 참조 하세요 [4457131](https://support.microsoft.com/help/4457131) 하 고 [4462917](https://support.microsoft.com/help/4462917)합니다.

### <a name="prerequisites"></a>필수 조건

- 1809 적용 하기 전에 1808에 대 한 최신 Azure Stack 핫픽스를 설치 합니다. 자세한 내용은 [KB 4468920 – Azure Stack 핫픽스 Azure Stack 핫픽스 1.1808.5.110](https://support.microsoft.com/en-us/help/4468920)합니다.

  > [!TIP]  
  > 다음을 구독할 *RRS* 또는 *Atom* Azure Stack 핫픽스를 피드 합니다.
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- 이 업데이트의 설치를 시작 하기 전에 실행할 [테스트 AzureStack](azure-stack-diagnostic-test.md) 에 Azure Stack의 상태를 확인 하 고 발견 된 작동 문제를 해결 하려면 다음 매개 변수를 사용 하 여 모든 경고 및 오류를 포함 합니다. 또한 활성 경고를 검토 하 고 작업을 필요로 하는 해결 합니다.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스를 사용 하 여 알려진된 문제

- 실행할 때 [테스트 AzureStack](azure-stack-diagnostic-test.md) 1809 업데이트 후 베이스 보드 관리 컨트롤러 (BMC)에서 경고 메시지가 표시 됩니다. 이 경고를 안전 하 게 무시할 수 있습니다.

- <!-- 2468613 - IS --> 이 업데이트를 설치 하는 동안 경고 제목으로 표시 될 수 있습니다 *오류 – FaultType UserAccounts.New 템플릿을 누락 되었습니다.*  이러한 경고를 안전 하 게 무시할 수 있습니다. 이러한 경고는이 업데이트의 설치가 완료 된 후 자동으로 종료 됩니다.

- <!-- 2489559 - IS --> 이 업데이트를 설치 하는 동안 가상 컴퓨터를 만들 하려고 하지 마십시오. 업데이트를 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md#plan-for-updates)합니다.

- <!-- 3139614 | IS --> Azure Stack에 OEM에 게에서 업데이트를 적용 한 경우는 **업데이트 사용 가능** 알림 Azure Stack 관리 포털에 나타나지 않을 수 있습니다. Microsoft 업데이트를 설치 하려면 다운로드 하 고 여기에 있는 지침을 사용 하 여 수동으로 가져와서 [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.

### <a name="post-update-steps"></a>업데이트 후 단계

> [!Important]  
> 다음 업데이트 패키지에서 사용할 수 있는 확장 호스트에 대 한 준비 된 Azure Stack 배포를 가져옵니다. 다음 지침을 사용 하 여 시스템을 준비 [Azure Stack에 대 한 확장 호스트에 대 한 준비](azure-stack-extension-host-prepare.md)합니다.

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - [Link to KB]()  
 -->

## <a name="known-issues-post-installation"></a>알려진된 문제 (사후 설치)

이 빌드 버전에 대 한 설치 후 알려진된 문제는 다음과 같습니다.

### <a name="portal"></a>포털

- Azure Stack 기술 설명서는 최신 버전에 중점을 둡니다. 릴리스 간의 포털 변경으로 인해 Azure Stack 포털을 사용 하는 경우 표시 되는 내용 설명서에 표시 되는 내용에서 달라질 수 있습니다.

<!-- 2930718 - IS ASDK --> 
- 관리자 포털에서 사용자 구독 하는 경우의 세부 정보 블레이드를 닫고 클릭 한 후에 액세스할 때 **최근**, 사용자 구독 이름이 표시 되지 않습니다.

<!-- 3060156 - IS ASDK --> 
- 포털 설정을 클릭 하 고 선택 하면 관리자와 사용자 포털 **모든 설정 및 개인 대시보드 삭제** 예상 대로 작동 하지 않습니다. 오류 알림이 표시 됩니다. 

<!-- 2930799 - IS ASDK --> 
- 관리자와 사용자 포털에서 아래 **모든 서비스**, 자산 **DDoS 보호 계획** 올바르게 나열 됩니다. Azure Stack에서 사용할 수 없는 합니다. 만들 하려고 하면 포털 마켓플레이스 항목을 만들 수 있는지를 나타내는 오류가 표시 됩니다. 

<!-- 2930820 - IS ASDK --> 
- 관리자와 사용자 포털에서 "Docker"를 검색 하는 경우 항목을 올바르게 반환 됩니다. Azure Stack에서 사용할 수 없는 합니다. 만들 하려고 하면 오류가 표시 된 블레이드가 표시 됩니다. 

<!-- 2967387 – IS, ASDK --> 
- Azure Stack 관리자 또는 사용자 포털에 로그인 하는 데 사용할 계정으로 표시 됩니다 **알 수 없는 사용자**합니다. 계정이 없는 경우 중 하나에이 메시지가 표시 됩니다는 *첫 번째* 또는 *마지막* 이름을 지정 합니다. 이 문제를 해결 하려면 첫 번째 또는 마지막 이름을 제공 하기 위해 사용자 계정을 편집 합니다. 그런 다음 로그 아웃 하 고 그런 다음 포털에 다시 로그인 해야 합니다.  

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
   
  실행 합니다 [테스트 AzureStack](azure-stack-diagnostic-test.md) 인프라 역할 인스턴스의 상태를 확인 하 고 단위 노드의 크기를 조정 하는 cmdlet입니다. 문제가 감지 되 면 [테스트 AzureStack](azure-stack-diagnostic-test.md), 이러한 경고를 무시할 수 있습니다. 문제가 감지 될 경우에 인프라 역할 인스턴스 또는 관리 포털 또는 PowerShell을 사용 하 여 노드를 시작 하려면 시작할 수 있습니다.

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
- 에 대 한 경고가 표시 될 수 있습니다 합니다 **저장소** 다음 세부 정보를 포함 하는 구성 요소:

   - 이름: 저장소 서비스의 내부 통신 오류  
   - 심각도: 위험  
   - 구성 요소: 저장소  
   - 설명: 다음 노드에 요청을 보낼 때 저장소 서비스의 내부 통신 오류가 발생 했습니다.  

    경고를 안전 하 게 무시할 수 있지만 경고를 수동으로 종결 해야 합니다.

<!-- 2368581 - IS, ASDK --> 
- 메모리 부족 경고를 받게 테 넌 트 가상 머신을 사용 하 여 배포 하는 데 실패 한 경우 Azure Stack 운영자를 **패브릭 VM 만들기 오류**, 사용 가능한 메모리가 부족 합니다. Azure Stack 스탬프는 불가능 합니다. 사용 된 [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 워크 로드에 대 한 사용 가능한 용량을 가장 잘 알아야 합니다.

### <a name="compute"></a>컴퓨팅

<!-- 3235634 – IS, ASDK -->
- 포함 된 크기를 사용 하 여 Vm을 배포 하는 **v2** 접미사; 예를 들어 **Standard_A2_v2**,으로 접미사를 지정 하세요 **Standard_A2_v2** (소문자 v). 사용 하지 마세요 **Standard_A2_V2** (V 대문자). 이 글로벌 Azure에서 작동 하며 Azure Stack에서 일치 하지 않습니다.

<!-- 3099544 – IS, ASDK --> 
- Azure Stack 포털을 사용 하 여 새 가상 컴퓨터 (VM)를 만든 경우 VM 크기를 선택 하면 u s D/월 열이 사용 하 여 표시 됩니다는 **사용할 수 없습니다** 메시지입니다. 이 열이 나타나지 않아야 합니다. 표시 된 VM 가격 책정 열에서에서 지원 되지 않습니다 Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- 사용 하는 경우는 [ **추가 AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0)를 사용 해야 합니다는 **-OsUri** storage 계정과 디스크 업로드 되는 URI 매개 변수입니다. 다음 오류로 인해 cmdlet이 실패 하면 디스크의 로컬 경로 사용 하는 경우: *장기 실행 작업 상태 '실패'를 사용 하 여 실패 한*합니다. 

<!--  2795678 – IS, ASDK --> 
- 가상 머신 (VM)를 만들려면 포털 (DS, Ds_v2, FS, FSv2) 프리미엄 VM 크기에서를 사용 하면 표준 저장소 계정에 VM이 생성 됩니다. 표준 저장소 계정 만드는 영향을 주지 않습니다 기능적으로 IOPs 또는 대금 청구 합니다. 

   라는 경고를 무시 해도: *에 프리미엄 디스크를 지 원하는 크기로 표준 디스크를 사용 하기로 선택 하셨습니다. 이 운영 체제 성능에 영향을 줄 수 있으며 권장 되지 않습니다. Premium storage (SSD)를 대신 사용 하는 것이 좋습니다.*

<!-- 2967447 - IS, ASDK --> 
- 가상 머신 확장 집합 (VMSS) 생성 환경에 대 한 배포 옵션으로 7.2 CentOS 기반을 제공합니다. 해당 이미지를 Azure Stack에서 사용할 수 없는 때문에 배포에 대 한 다른 OS를 선택 하거나 연산자가 marketplace에서 배포 하기 전에 다운로드 된 다른 CentOS 이미지를 지정 하는 Azure Resource Manager 템플릿을 사용 합니다.  

<!-- 2724873 - IS --> 
- PowerShell cmdlet을 사용 하는 경우 **시작 AzsScaleUnitNode** 하거나 **중지 AzsScaleunitNode** 배율 단위를 관리 하려면 시작 또는 중지 확장 단위를 첫 번째 시도가 실패할 수 있습니다. 첫 번째 실행에 실패 하면 cmdlet은 cmdlet을 두 번 실행 합니다. 작업을 완료 하 고 두 번째 실행 성공 해야 합니다. 

<!-- TBD - IS ASDK --> 
- VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

<!-- 1662991 IS ASDK --> 
- Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다.  

<!-- 2724961- IS ASDK --> 
- 등록 하는 경우는 **Microsoft.Insight** 리소스 공급자 구독 설정에서 Windows VM 만들기 및 VM 개요 페이지에서 CPU 백분율 차트 게스트 OS 진단 활성화를 사용 하 여 메트릭 데이터를 표시 하지 않습니다.

   VM에 대 한 CPU 백분율 차트와 같은 메트릭 데이터를 찾으려면 메트릭 창으로 이동 하 고 지원 되는 모든 Windows VM 게스트 메트릭을 보여줍니다.



### <a name="networking"></a>네트워킹  

<!-- 1766332 - IS ASDK --> 
- 아래 **네트워킹**를 클릭 하면 **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택하지 마세요. 만 **경로 기반** 옵션은 Azure Stack에서 지원 됩니다.

<!-- 1902460 - IS ASDK --> 
- Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

<!-- 16309153 - IS ASDK --> 
- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.

<!-- 2529607 - IS ASDK --> 
- Azure Stack 하는 동안 *암호 회전*에는 공용 IP 주소에 연결할 수 없는 2 ~ 5 분에 대 한 간격이 있습니다.

<!-- 2664148 - IS ASDK --> 
-   여기서 테 넌 트에 액세스 하는 가상 컴퓨터는 S2S VPN 터널을 사용 하 여 시나리오에서는 온-프레미스 서브넷이 게이트웨이 이미 만든 후 로컬 네트워크 게이트웨이를 추가한 경우 연결 시도가 실패 하는 있는 경우를 발생할 수 있습니다. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.


### <a name="usage"></a>사용 현황  

<!-- TBD - IS ASDK --> 
- 동일한 공용 IP 주소 사용 측정기 데이터 표시 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>업데이트 다운로드

Azure Stack 1809 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.
  

## <a name="next-steps"></a>다음 단계

- Azure Stack 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 해야 할 새로운 서비스의 정책을 참조 하세요 [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)합니다.  
- 권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.  
- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.  
- Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.  
