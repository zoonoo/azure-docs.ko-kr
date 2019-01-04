---
title: Microsoft Azure Stack 개발 키트 릴리스 정보 | Microsoft Docs
description: 향상 된 기능, 수정 및 Azure Stack 개발 키트에 대 한 알려진된 문제입니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: misainat
ms.openlocfilehash: d1ff154c42709f0c672b30f7ec51a436fb44ce13
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724745"
---
# <a name="asdk-release-notes"></a>ASDK 릴리스 정보 
 
이 문서에서는 향상 된 기능, 수정 및 알려진된 문제에는 Azure Stack 개발 키트 ASDK ()에 대 한 정보를 제공 합니다. 실행 중인 버전을 잘 모를 경우 [포털을 사용 하 여 확인](../azure-stack-updates.md#determine-the-current-version)합니다.

> 에서 새로운 기능을 ASDK 구독 하 여 최신 합니다 [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [피드](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)합니다.

## <a name="build-118110101"></a>1.1811.0.101 빌드

### <a name="new-features"></a>새로운 기능

Azure Stack에 대 한 다음과 같은 향상 된 기능 및 픽스를 포함 하는이 빌드:  

- 새 최소 및 권장 하드웨어 및 소프트웨어 요구 사항은 ASDK 집합이 있습니다. 에 설명 된 새 이러한 권장 사양을 [Azure Stack 배포의 계획 고려 사항](asdk-deploy-considerations.md)합니다. Azure Stack 플랫폼에 발전 함에 따라 더 많은 서비스 제공 됩니다.으로 더 많은 리소스가 필요할 수 있습니다. 향상 된 사양은 이러한 수정 된 권장 사항을 반영합니다.

### <a name="fixed-issues"></a>해결된 문제

<!-- TBD - IS ASDK --> 
- 공용 IP 주소 사용 측정기 데이터 알아보았습니다 동일한 문제를 해결 했습니다 **EventDateTime** 대신 각 레코드에 대 한 값을 **TimeDate** 레코드를 만든 경우를 보여 주는 스탬프입니다. 이제 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 있습니다.

<!-- 3099544 – IS, ASDK --> 
- Azure Stack 포털을 사용 하 여 새 가상 컴퓨터 (VM)를 만들 때 발생 한 문제를 해결 합니다. U s D/월 표시할 열을 발생 한 VM 크기를 선택 하는 **사용할 수 없습니다** 메시지입니다. 이 열이 더 이상 나타나지 않습니다. 표시 된 VM 가격 책정 열에서에서 지원 되지 않습니다 Azure Stack.

<!-- 2930718 - IS ASDK --> 
- 문제를 해결 했습니다. 사용자 구독 하는 경우의 세부 정보 블레이드를 닫고 클릭 한 후에 액세스할 때 관리자 포털 **최근**, 사용자 구독 이름이 표시 되지 않았습니다. 사용자 구독 이름에는 이제 다음이 표시 됩니다.

<!-- 3060156 - IS ASDK --> 
- 관리자와 사용자 포털에서 문제를 해결: 포털 설정을 클릭 하 고 선택 **모든 설정 및 개인 대시보드 삭제** 오류 알림이 표시 된 및 예상 대로 작동 하지 않았습니다. 

<!-- 2930799 - IS ASDK --> 
- 관리자와 사용자 포털에서 문제를 해결: 아래의 **모든 서비스**, 자산 **DDoS 보호 계획** 가 올바르게 나열 되지 되지만 Azure Stack에서 사용할 수 없습니다.
 
<!--2760466 – IS  ASDK --> 
- 새 Azure Stack 환경을 설치한 경우에 발생 한 문제를 해결 했습니다 나타내는 경고가 **활성화 필요** 표시 되지 않았습니다. 이제 올바르게 표시 됩니다.

<!--1236441 – IS  ASDK --> 
- ADFS를 사용 하는 경우 RBAC 정책을 사용자 그룹에 적용 되지 않는 문제를 해결 합니다.

<!--3463840 - IS, ASDK --> 
- 공용 VIP 네트워크에서 액세스할 수 없는 파일 서버 인해 실패 하는 인프라 백업 사용 하 여 문제를 해결 했습니다. 이 문제가 해결 공개 인프라 네트워크에 인프라 백업 서비스를 다시 이동합니다. 최신을 적용 하는 경우 [1809에 대 한 Azure Stack 핫픽스](#azure-stack-hotfixes) 이 문제를 해결 하는, 1811 업데이트가 모든 추가로 수정할 수 없습니다. 

<!-- 2967387 – IS, ASDK --> 
- Azure Stack 관리자 또는 사용자 포털에 로그인 할 때 사용한 계정으로 표시 되는 문제를 해결 했습니다 **알 수 없는 사용자**합니다. 계정이 하나를 갖고 있지 않은 경우이 메시지가 표시 된를 *첫 번째* 또는 *마지막* 이름을 지정 합니다.   

<!--  2873083 - IS ASDK --> 
- 가상 머신을 만들려면 포털을 사용 하는 확장 집합 (VMSS) 문제를 해결 합니다 *인스턴스 크기* Internet Explorer를 사용 하는 경우 드롭다운에서 제대로 로드 되지 않았습니다. 이 브라우저는 이제 올바르게 작동합니다.  

<!-- 3190553 - IS ASDK -->
- 인프라 역할 인스턴스를 사용할 수 없습니다 또는 배율 단위 노드가 오프 라인 상태 였는 나타내는 불필요 한 경고를 생성 하는 문제가 수정 되었습니다.

### <a name="known-issues"></a>알려진 문제

#### <a name="portal"></a>포털  

<!-- 2930820 - IS ASDK --> 
- 관리자와 사용자 포털에서 "Docker"를 검색 하는 경우 항목을 올바르게 반환 됩니다. Azure Stack에서 사용할 수 없는 합니다. 만들 하려고 하면 오류가 표시 된 블레이드가 표시 됩니다. 

<!-- 2931230 – IS  ASDK --> 
- 사용자 구독에서 해당 계획을 제거 하는 경우에는 추가 요금제는 사용자 구독에 추가 되는 계획을 삭제할 수 없습니다. 계획에는 추가 요금제를 참조 하는 구독도 삭제 될 때까지 상태로 유지 됩니다. 

<!-- TBD - IS ASDK --> 
- 1804 버전을 사용 하 여 도입 된 관리 구독 유형에 사용할 수 없습니다. 구독 유형은 **구독을 계량**, 및 **소비 구독**합니다. 이러한 구독 유형에 1804 버전부터 새 Azure Stack 환경에서 표시 되지만 아직 사용할 준비가 완료 됩니다. 계속 사용 해야 합니다 **기본 공급자** 구독 유형입니다.

<!-- TBD - IS ASDK --> 
- 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

<!-- TBD - IS ASDK --> 
- Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

#### <a name="compute"></a>컴퓨팅 

<!-- 3235634 – IS, ASDK -->
- 포함 된 크기를 사용 하 여 Vm을 배포 하는 **v2** 접미사; 예를 들어 **Standard_A2_v2**,으로 접미사를 지정 하세요 **Standard_A2_v2** (소문자 v). 사용 하지 마세요 **Standard_A2_V2** (V 대문자). 이 글로벌 Azure에서 작동 하며 Azure Stack에서 일치 하지 않습니다.

<!-- 2869209 – IS, ASDK --> 
- 사용 하는 경우는 [ **추가 AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0)를 사용 해야 합니다는 **-OsUri** storage 계정과 디스크 업로드 되는 URI 매개 변수입니다. 디스크의 로컬 경로 사용 하는 경우 cmdlet은 다음 오류로 인해 실패 합니다. *장기 실행 작업 상태 '실패'를 사용 하 여 실패 한*합니다. 

<!--  2795678 – IS, ASDK --> 
- 가상 머신 (VM)를 만들려면 포털 (DS, Ds_v2, FS, FSv2) 프리미엄 VM 크기에서를 사용 하면 표준 저장소 계정에 VM이 생성 됩니다. 표준 저장소 계정 만드는 영향을 주지 않습니다 기능적으로 IOPs 또는 대금 청구 합니다. 안전 하 게 되었다는 경고를 무시할 수 있습니다. **에서는 표준 디스크를 프리미엄 디스크를 지 원하는 크기를 사용 하기로 했습니다. 이 운영 체제 성능에 영향을 줄 수 있으며 권장 되지 않습니다. Premium storage (SSD)를 대신 사용 하는 것이 좋습니다.**

<!-- 2967447 - IS, ASDK --> 
- 가상 머신 확장 집합 (VMSS) 생성 환경에 대 한 배포 옵션으로 7.2 CentOS 기반을 제공합니다. 해당 이미지를 Azure Stack에서 사용할 수 없는 때문에 배포에 대 한 다른 OS를 선택 하거나 연산자가 marketplace에서 배포 하기 전에 다운로드 된 다른 CentOS 이미지를 지정 하는 Azure Resource Manager 템플릿을 사용 합니다.  

<!-- TBD - IS ASDK --> 
- VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

<!-- 1662991 IS ASDK --> 
- Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다.  

<!-- 2724961- IS ASDK --> 
- 등록 하는 경우는 **Microsoft.Insight** 리소스 공급자 구독 설정에서 Windows VM 만들기 및 VM 개요 페이지에서 CPU 백분율 차트 게스트 OS 진단 활성화를 사용 하 여 메트릭 데이터를 표시 하지 않습니다.

   VM에 대 한 CPU 백분율 차트와 같은 메트릭 데이터를 찾으려면 메트릭 창으로 이동 하 고 지원 되는 모든 Windows VM 게스트 메트릭을 보여줍니다.

<!-- 3507629 - IS, ASDK --> 
- Managed Disks에서는 두 개의 새 [계산 할당량 형식](../azure-stack-quota-types.md#compute-quota-types) 프로 비전 할 수 있는 관리 되는 디스크의 최대 용량을 제한 합니다. 기본적으로 각 관리 되는 디스크 할당량 형식에 대 한 2048 GiB 할당 됩니다. 그러나 다음과 같은 문제가 발생할 수 있습니다.

   - 할당량의 1808 업데이트 이전에 만든 경우 Managed Disks 할당량에에서 표시 됩니다 0 값 관리자 포털을 2048 GiB 할당 됩니다. 늘리거나 실제 요구 사항 및 새로 설정에 따라 값을 줄일 수 할당량 값 2048 GiB 기본값을 재정의 합니다.
   - 0으로 할당량 값을 업데이트 하는 경우 기본값인 2048 GiB 같습니다. 대 안으로 할당량 값을 1로 설정 합니다.

<!-- TBD - IS ASDK --> 
- 업데이트 된 1811를 적용 한 후, Managed Disks를 사용 하 여 Vm을 배포 하는 경우 다음과 같은 문제가 발생할 수 있습니다.

   1. Managed Disks를 사용 하 여 VM 배포 1808 업데이트 하기 전에 구독을 만든 경우 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면 각 구독에 대해 다음이 단계를 수행 합니다.
      1. 테 넌 트 포털에서로 이동 **구독** 구독을 찾습니다. 클릭 **리소스 공급자**, 클릭 **Microsoft.Compute**를 클릭 하 고 **re-register**합니다.
      2. 로 동일한 구독에 따라 **액세스 제어 (IAM)**, 되어 있는지 확인 하 고 **Azure Stack – Managed Disk** 나열 됩니다.
   2. 다중 테 넌 트 환경에 구성한 게스트 디렉터리와 연결 된 구독에서 Vm을 배포할 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면에서 다음이 단계를 수행 [이 문서에서는](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) 각 게스트 디렉터리를 다시 구성 합니다.

#### <a name="networking"></a>네트워킹

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

#### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

<!-- #### Usage -->  

<!-- #### Identity -->

## <a name="build-11809090"></a>1.1809.0.90 빌드

### <a name="new-features"></a>새로운 기능
이 빌드 Azure Stack에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.  

<!--  2712869   | IS  ASDK -->  
- **Azure Stack syslog 클라이언트 (일반 공급)** 이 클라이언트에는 감사, 경고 및 Azure Stack 인프라를 syslog 서버 또는 보안 정보 및 이벤트 관리 (SIEM) 소프트웨어와 관련 된 보안 로그를 전달할 수 있도록 Azure Stack에 외부입니다. Syslog 클라이언트는 이제 syslog 서버 수신 대기 하는 포트 지정을 지원 합니다.

이 릴리스에서 syslog 클라이언트는 일반 공급 하 고 프로덕션 환경에서 사용할 수 있습니다.

자세한 내용은 [Azure Stack syslog 전달을](../azure-stack-integrate-security.md)합니다.

### <a name="fixed-issues"></a>해결된 문제

<!-- TBD - IS ASDK -->
- 포털에서 무료/사용 된 용량을 보고 메모리 차트 정확 하 게 됩니다. 이제 얼마나 많은 Vm을 만들 수 있다면 더 안정적으로 예측할 수 있습니다.

<!-- TBD - IS ASDK --> 
- Azure Stack 사용자 포털에서 가상 컴퓨터를 만든이 및 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 하는 문제가 수정 되었습니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- <!-- 2702741 -  IS, ASDK --> 동적 할당을 사용 하 여 배포 된 공용 Ip는 메서드가 없습니다. 문제 해결된 실행 중지-할당 취소 후에 유지 되도록 보장 합니다. 이제 보존 됩니다.

- <!-- 3078022 - IS, ASDK --> VM 중지-할당 해제 하기 전에 되었던 1808 경우 수 없는 다시 할당 1808 업데이트 후 합니다.  이 문제는 1809에서 해결 됩니다. 이 문제가 해결 1809에서이 상태에 있으며 시작할 수 없습니다. 인스턴스를 시작할 수 있습니다. 또한 수정 반복에서이 문제를 방지합니다.

<!-- TBD -  IS ASDK --> 
- VM 이미지를 만들려는 못하면 VM 이미지 계산 블레이드로 삭제할 수 없습니다는 실패 한 항목을 추가할 수 있습니다.

  해결 방법으로 Hyper-v를 통해 만들 수 있는 더미 VHD를 사용 하 여 새 VM 이미지 만들기 (새 VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하는 것을 금지 하는 문제를 수정 해야 합니다. 그런 다음 더미 이미지를 만든 후 15 분 삭제할 수 있습니다 했습니다.

  그런 다음 이전에 실패 한 VM 이미지의 다운로드를 다시 시도할 수 있습니다.

- **다양 한 수정** 성능, 안정성, 보안 및 Azure Stack에서 사용 되는 운영 체제에 대 한

### <a name="changes"></a>변경 내용

### <a name="known-issues"></a>알려진 문제

#### <a name="portal"></a>포털  

<!-- 2515955   | IS ,ASDK--> 
- *모든 서비스* 바꿉니다 *더 많은 서비스* Azure Stack 관리자 및 사용자 포털에 있습니다. 이제 사용할 수 있습니다 *모든 서비스* Azure 포털에서 수행한 동일한 방식으로 Azure Stack 포털에 이동 하는 대신 합니다.

<!--  TBD – IS, ASDK --> 
- *기본 A* 가상 머신 크기에 대 한 현재 퇴직 [virtual machine scale sets 만들기](../azure-stack-compute-add-scalesets.md) (VMSS) 포털을 통해. 이 크기를 사용 하 여 VMSS를 만들려면, PowerShell 또는 템플릿을 사용 합니다.

#### <a name="health-and-monitoring"></a>상태 및 모니터링

<!-- 1264761 - IS ASDK -->  
- 에 대 한 경고를 표시 될 수 있습니다 합니다 *상태 컨트롤러* 다음 세부 정보는 구성 요소:  

   경고 # 1:
   - 이름:  인프라 역할 비정상
   - 심각도: Warning
   - 구성 요소입니다. 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 검색 프로그램을 사용할 수 없는 경우 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  # 2를 경고 합니다.
   - 이름:  인프라 역할 비정상
   - 심각도: Warning
   - 구성 요소입니다. 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너 제공 되지 않습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고가 모두 안전 하 게 무시 될 수 있으며 시간이 지남에 따라 자동으로 닫힙니다.  

<!-- 2368581 - IS. ASDK --> 
- 메모리 부족 경고를 받게 테 넌 트 가상 머신을 사용 하 여 배포 하는 데 실패 한 경우 Azure Stack 운영자를 *패브릭 VM 만들기 오류*, 사용 가능한 메모리가 부족 합니다. Azure Stack 스탬프는 불가능 합니다. 사용 된 [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 워크 로드에 대 한 사용 가능한 용량을 가장 잘 알아야 합니다.


#### <a name="compute"></a>컴퓨팅 

<!-- 3164607 – IS, ASDK -->
- 와 같은 오류로 인해 실패 때도 동일한 이름 및 LUN을 사용 하 여 동일한 가상 컴퓨터 (VM) 디스크를 분리 **datadisk' 데이터 디스크' 'vm1' VM에 연결할 수 없습니다**합니다. 디스크가 현재 분리 됨 또는 마지막 분리 작업 실패 오류가 발생 합니다. 하세요 디스크가 완전히 분리 될 때까지 대기 하 고 다시 시도 하거나 삭제/분리 디스크 명시적으로 다시 합니다. 이를 해결 하려면 다른 이름으로 또는 다른 LUN에 다시 연결 합니다. 

<!-- 3235634 – IS, ASDK -->
- 포함 된 크기를 사용 하 여 Vm을 배포 하는 **v2** 접미사; 예를 들어 **Standard_A2_v2**,으로 접미사를 지정 하세요 **Standard_A2_v2** (소문자 v). 사용 하지 마세요 **Standard_A2_V2** (V 대문자). 이 글로벌 Azure에서 작동 하며 Azure Stack에서 일치 하지 않습니다.

<!-- 3099544 – IS, ASDK --> 
- Azure Stack 포털을 사용 하 여 새 가상 컴퓨터 (VM)를 만든 경우 VM 크기를 선택 하면 u s D/월 열이 사용 하 여 표시 됩니다는 **사용할 수 없습니다** 메시지입니다. 이 열이 나타나지 않아야 합니다. 표시 된 VM 가격 책정 열에서에서 지원 되지 않습니다 Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- 사용 하는 경우는 [ **추가 AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0)를 사용 해야 합니다는 **-OsUri** storage 계정과 디스크 업로드 되는 URI 매개 변수입니다. 디스크의 로컬 경로 사용 하는 경우 cmdlet은 다음 오류로 인해 실패 합니다. *장기 실행 작업 상태 '실패'를 사용 하 여 실패 한*합니다. 

<!--  2966665 – IS, ASDK --> 
- 데이터 디스크를 프리미엄 크기 디스크 virtual machines (DS, DSv2, Fs, Fs_V2) 실패 오류가 발생 하 여 관리 되는 SSD에 연결:  *가상 머신 'vmname' 오류에 대 한 디스크를 업데이트 하지 못했습니다. 요청한 VM 크기에 대 한 저장소 계정 유형 'Premium_LRS' 지원 되지 않으므로 작업을 수행할 수 없습니다 ' Standard_DS/Ds_V2/FS/Fs_v2)*

   이 문제를 해결 하려면 사용 하 여 *Standard_LRS* 대신 데이터 디스크 *Premium_LRS 디스크*합니다. 이용 *Standard_LRS* 데이터 디스크 IOPs 또는 비용을 변경 하지 않습니다.  

<!--  2795678 – IS, ASDK --> 
- 가상 머신 (VM)를 만들려면 포털 (DS, Ds_v2, FS, FSv2) 프리미엄 VM 크기에서를 사용 하면 표준 저장소 계정에 VM이 생성 됩니다. 표준 저장소 계정 만드는 영향을 주지 않습니다 기능적으로 IOPs 또는 대금 청구 합니다. 

   안전 하 게 되었다는 경고를 무시할 수 있습니다. *에서는 표준 디스크를 프리미엄 디스크를 지 원하는 크기를 사용 하기로 했습니다. 이 운영 체제 성능에 영향을 줄 수 있으며 권장 되지 않습니다. Premium storage (SSD)를 대신 사용 하는 것이 좋습니다.*

<!-- 2967447 - IS, ASDK --> 
- 가상 머신 확장 집합 (VMSS) 환경을 만드는 7.2 CentOS 기반 배포에 대 한 옵션으로 제공 합니다. 해당 이미지를 Azure Stack에서 사용할 수 없는 때문에 배포에 대 한 다른 OS를 선택 하거나 연산자가 marketplace에서 배포 하기 전에 다운로드 된 다른 CentOS 이미지를 지정 하는 Azure Resource Manager 템플릿을 사용 합니다.

<!-- TBD -  IS ASDK --> 
- 가상 머신 확장 집합에 대 한 크기 조정 설정을 포털에서 사용할 수 없는 경우 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이로 인해 사용 해야 합니다 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.



<!-- TBD -  IS ASDK --> 
- VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

<!-- 1662991 - IS ASDK --> 
- Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다.

<!-- 2724961- IS ASDK --> 
- 등록 하는 경우는 **Microsoft.Insight** 리소스 공급자 구독 설정에서 게스트 OS 진단 사용 하도록 설정 된 Windows VM 만들기, VM 개요 페이지에서 CPU 백분율 차트 메트릭 데이터를 표시할 수 없습니다.
 
  VM에 대 한 CPU 백분율 차트를 찾으려면에서로 이동 합니다 **메트릭을** 블레이드에서 살펴보고, 지원 되는 모든 Windows VM 게스트 메트릭을 합니다.

 

#### <a name="networking"></a>네트워킹
<!-- 1766332 - IS, ASDK --> 
- 아래 **네트워킹**를 클릭 하면 **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택하지 마세요. 만 **경로 기반** 옵션은 Azure Stack에서 지원 됩니다.

<!-- 1902460 -  IS ASDK --> 
- Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

<!-- 16309153 -  IS ASDK --> 
- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.

<!-- 2702741 -  IS ASDK --> 
- 동적 할당 메서드를 사용 하 여 배포 되는 공용 Ip 실행 중지-할당 취소 후에 유지 되도록 보장 되지 않습니다.

<!-- 2529607 - IS ASDK --> 
- Azure Stack 하는 동안 *암호 회전*에는 공용 IP 주소에 연결할 수 없는 2 ~ 5 분에 대 한 간격이 있습니다.

<!-- 2664148 - IS ASDK --> 
- 여기서 테 넌 트에 액세스 하는 가상 컴퓨터는 S2S VPN 터널을 사용 하 여 시나리오에서는 온-프레미스 서브넷이 게이트웨이 이미 만든 후 로컬 네트워크 게이트웨이를 추가한 경우 연결 시도가 실패 하는 있는 경우를 발생할 수 있습니다. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

<!-- TBD - IS ASDK --> 
- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.  


#### <a name="usage"></a>사용 현황  
<!-- TBD -  IS ASDK --> 
- 사용량 공용 IP 주소 사용 측정기 데이터를 보여 줍니다 동일 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.

<!-- #### Identity -->

## <a name="build-11808097"></a>1.1808.0.97 빌드

### <a name="new-features"></a>새로운 기능
이 빌드 Azure Stack에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.  

<!-- 1658937 | ASDK, IS --> 
- **미리 정의 된 일정에 따라 백업을 시작** -는 어플라이언스로 Azure Stack 이제 자동으로 트리거할 수 사용자의 개입을 제거 하는 정기적으로 백업 인프라입니다. Azure Stack 정의 된 보존 기간 보다 오래 된 백업에 대 한 외부 공유를 자동으로 정리 합니다. 자세한 내용은 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](../azure-stack-backup-enable-backup-powershell.md)합니다.

<!-- 2496385 | ASDK, IS -->  
- **추가 데이터 전송 시간으로 전체 백업 시간입니다.** 자세한 내용은 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](../azure-stack-backup-enable-backup-powershell.md)합니다.

<!-- 1702130 | ASDK, IS --> 
- **이제 백업 외부 용량 외부 공유의 올바른 용량을 보여 줍니다.** (하드 코드 된이 이전에 10gb입니다.) 자세한 내용은 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](../azure-stack-backup-enable-backup-powershell.md)합니다.
 
<!-- 2753130 |  IS, ASDK   -->  
- **Azure Resource Manager 템플릿은 이제 condition 요소를 지원** -이제는 조건을 사용 하 여 Azure 리소스 관리자 템플릿에서 리소스를 배포할 수 있습니다. 매개 변수 값이 있는 경우 평가 같은 조건에 따라 리소스를 배포 하도록 템플릿을 디자인할 수 있습니다. 조건으로 서식 파일을 사용 하는 방법에 대 한 정보를 참조 하세요 [조건부 리소스 배포](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) 하 고 [Azure Resource Manager 템플릿의 변수 섹션](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) Azure 설명서에서. 

   템플릿을 사용할 수도 있습니다 [둘 이상의 구독 또는 리소스 그룹에 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment)합니다.  

<!--2753073 | IS, ASDK -->  
- **Microsoft.Network API 리소스 버전 지원 고쳤습니다** API 버전 2017-10-01 Azure Stack 네트워크 리소스에 대 한 15 개 2015-06-에 대 한 지원을 포함 하도록 합니다. 이 릴리스에서 리소스 버전 2015-06-15 2017-10-01 사이 대 한 지원이 포함 되지 않습니다. 참조 하십시오 [Azure Stack 네트워킹에 대 한 고려 사항](../user/azure-stack-network-differences.md) 기능 차이점에 대 한 합니다.

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack에는 외부 Azure Stack 인프라 끝점에 대 한 역방향 DNS 조회에 대 한 지원이 추가 되었습니다** (즉, adminportal, 관리 포털과 adminmanagement). 이렇게 하면 Azure Stack 외부 끝점 이름을 IP 주소에서 확인할 수 있습니다.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack은 이제 기존 VM에 추가 네트워크 인터페이스를 추가 지원 합니다.**  이 기능은 포털, PowerShell 및 CLI를 사용 하 여 사용할 수 있습니다. 자세한 내용은 [네트워크 인터페이스 추가 또는 제거](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) Azure 설명서에서. 

<!-- 2222444 | IS, ASDK   -->  
- **정확도 및 복원 력 향상 되었습니다 사용량 미터 네트워킹**합니다. 네트워크 사용량 미터 보다 정확 하 게 됩니다 및 일시 중단 하는 계정 구독, 작동 중단 기간 및 경합 상태를 고려 합니다.

<!-- 2297790 | IS, ASDK -->  
- **Azure Stack syslog 클라이언트 (미리 보기 기능)의 향상 된 기능**합니다. 이 클라이언트에 감사 및 Azure Stack 인프라를 syslog 서버 또는 보안 정보 및 이벤트 관리 (SIEM) 소프트웨어의 외부 Azure Stack에 관련 된 로그를 전달할 수 있습니다. Syslog 클라이언트는 이제 일반 텍스트 또는 TLS 1.2 암호화, 후자 되는 기본 구성을 사용 하 여 TCP 프로토콜을 지원 합니다. 서버 전용 또는 상호 인증을 사용 하 여 TLS 연결을 구성할 수 있습니다.

  Syslog 서버를 사용 하 여 syslog 클라이언트 (예: 프로토콜, 암호화 및 인증) 통신 하는 방법을 구성 하려면 집합 SyslogServer cmdlet을 사용 합니다. 이 cmdlet은 권한 있는 끝점 (PEP)에서 사용할 수 있습니다. 

- <!-- ASDK --> **Virtual Machine Scale Sets에 대 한 갤러리 항목은 기본 제공 이제**합니다.  Virtual Machine Scale Set 갤러리 항목은 이제 사용 가능 사용자 및 관리자 포털에서 다운로드 하지 않고도 합니다. 

- <!-- IS, ASDK --> **가상 머신 확장 집합 크기 조정**합니다.  포털을 사용할 수 있습니다 [가상 머신 확장 집합 확장](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).   

- <!-- 2489570 | IS ASDK--> **사용자 지정 IPSec/IKE 정책 구성에 대 한 지원을** 에 대 한 [VPN 게이트웨이가 Azure Stack에서](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways)합니다.

- <!-- | IS ASDK--> **Kubernetes 마켓플레이스 항목**합니다. 이제 사용 하 여 Kubernetes 클러스터를 배포할 수 있습니다 합니다 [Kubernetes 마켓플레이스 항목](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add)합니다. 사용자는 Kubernetes 항목을 선택 하 고 Azure Stack에 Kubernetes 클러스터를 배포 하는 몇 가지 매개 변수를 채울 수 있습니다. 템플릿의 목적은 간단 하 게 몇 가지 단계에서 개발/테스트 Kubernetes 배포의 설치는 사용자에 게입니다.

<!-- ####### | IS, ASDK -->  
- **Azure Resource Manager 지역 이름이 포함 됩니다.** 이 릴리스를 사용 하 여 Azure Resource Manager에서 검색 된 개체는 지역 이름 특성을 포함 이제 됩니다. 기존 PowerShell 스크립트를 다른 cmdlet에 개체를 직접 통과 하면 스크립트 오류가 발생 하 고 실패할 수 있습니다. Azure Resource Manager 규격 동작을 이며 region 특성 뺄 호출 클라이언트가 필요 합니다. 자세한 내용은 Azure Resource Manager를 참조 하십시오 [Azure Resource Manager 설명서](https://docs.microsoft.com/azure/azure-resource-manager/)합니다.

<!-- TBD | IS, ASDK -->  
- **위임 된 공급자 간에 구독을 이동 합니다.** 이제 동일한 디렉터리 테 넌 트에 속해 있는 기존 또는 새 위임 된 공급자 구독 간에 구독을 이동할 수 있습니다. 기본 공급자 구독에 속하는 구독을 동일한 디렉터리 테 넌 트 위임 된 공급자 구독에도 이동할 수 있습니다. 자세한 내용은 참조 [Azure Stack에서 제품 위임](../azure-stack-delegated-provider.md)합니다.
 
<!-- 2536808 IS ASDK --> 
- **VM을 만들 때 향상 된** Azure marketplace에서 다운로드 하는 이미지를 사용 하 여 만든 Vm에 대 한 합니다.

### <a name="fixed-issues"></a>해결된 문제
- <!-- IS ASDK--> 장애 도메인 및 업데이트 도메인 1의 집합에서 발생 하는 포털에서 가용성 집합을 만드는 문제를 해결 했습니다.

<!-- TBD | ASDK, IS --> 
- 다양 한 향상 되었습니다 업데이트 프로세스를 더 신뢰할 수 있도록 합니다. 또한 노드 드레이닝, 업데이트 하는 동안 워크 로드에 대 한 잠재적인 가동 중지 시간을 최소화 함으로써 향상 된 기본 인프라를 수정 했습니다.

<!--2292271 | ASDK, IS --> 
- 문제를 해결 했습니다 기존 구독에 수정 된 할당량 제한이 적용 되지 않았습니다.  이제 제품의 일부인 네트워크 리소스에 대 한 할당량 한도 시키고 사용자 구독과 연결 된 계획 하는 경우 새로운 제한 값 새 구독 뿐만 아니라 기존 구독에 적용 됩니다.

<!-- 2448955 | IS ASDK --> 
- 이제는 UTC + N 표준 시간대에 배포 된 시스템에 대 한 활동 로그를 성공적으로 쿼리할 수 있습니다.    

<!-- 2319627 |  ASDK, IS --> 
- 백업 구성 매개 변수 (경로/사용자 이름/암호/암호화 키)에 대 한 사전 검사는 더 이상 백업 구성 설정이 올바르지 않으면를 설정합니다. (이전에 백업 설정이 잘못 설정 된 것과 트리거될 때 백업 실패 후)

<!-- 2215948 |  ASDK, IS --> 
- 외부 공유에서 백업을 수동으로 삭제할 때에 이제 백업 목록을 새로 고칩니다.

<!-- 2360715 |  ASDK, IS -->  
- 데이터 센터 통합을 설정한 경우 더 이상 AD FS 메타 데이터 파일은 공유에서 액세스 합니다. 자세한 내용은 [페더레이션 메타 데이터 파일을 제공 하 여 AD FS 통합 설정](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file)합니다. 

<!-- 2388980 | ASDK, IS --> 
- 문제를 해결 했습니다 방지 없도록 할당 기존 공용 IP 주소는 이전에 할당 된 네트워크 인터페이스 또는 부하 분산 장치를 새 네트워크 인터페이스 또는 부하 분산 장치.  

<!-- 2551834 - IS, ASDK --> 
- 선택 하면 *개요* admin 또는 사용자 포털에서 저장소 계정에는 *Essentials* 창 이제 필요한 모든 정보가 올바르게 표시 합니다. 

<!-- 2551834 - IS, ASDK --> 
- 선택 하면 *태그* 관리자 또는 사용자 포털에서 저장소 계정에 대 한 정보를 이제 올바르게 표시 됩니다.

<!-- TBD - IS ASDK --> 
- 이 버전의 Azure Stack OEM 확장 패키지에서 드라이버 업데이트를 적용 하지 못하는 문제를 해결 합니다.

<!-- 2055809- IS ASDK --> 
- VM을 만들지 못한 경우 계산 블레이드에서 Vm을 삭제 하지 못하게 하는 문제를 해결 했습니다.  

<!--  2643962 IS ASDK -->  
- 에 대 한 경고 *메모리 용량을 낮은* 더 이상 잘못 표시 됩니다.

<!--  TBD ASDK --> 
- 가상 컴퓨터 (PEP) 권한 끝점을 호스트 하는 4GB로 증가 했습니다. ASDK,이 가상 머신에 AzS ERCS01 라고 했습니다.

- <!--  TBD – IS, ASDK --> *기본 A* 가상 머신 크기에 대 한 현재 퇴직 [virtual machine scale sets 만들기](../azure-stack-compute-add-scalesets.md) (VMSS) 포털을 통해. 이 크기를 사용 하 여 VMSS를 만들려면, PowerShell 또는 템플릿을 사용 합니다. 

### <a name="known-issues"></a>알려진 문제

#### <a name="portal"></a>포털  
- <!-- 2967387 – IS, ASDK --> Azure Stack 관리자 또는 사용자 포털에 로그인 하는 데 사용할 계정으로 표시 됩니다 **알 수 없는 사용자**합니다. 이런 계정에 없는 경우 하나는 *첫 번째* 또는 *마지막* 이름을 지정 합니다. 이 문제를 해결 하려면 첫 번째 또는 마지막 이름을 제공 하기 위해 사용자 계정을 편집 합니다. 그런 다음 로그 아웃 하 고 그런 다음 포털에 다시 로그인 해야 합니다. 

-  <!--  2873083 - IS ASDK --> 가상 머신 확장 집합을 만드는 포털을 사용할 때 (VMSS)를 설정 합니다 *인스턴스 크기* Internet Explorer를 사용 하는 경우 드롭다운에서 올바르게 로드 합니다. 이 문제를 해결 하려면 포털을 사용 하 여 VMSS를 만들려고 하는 동안 다른 브라우저를 사용 합니다.  

#### <a name="portal"></a>포털  
<!-- 2931230 – IS  ASDK --> 
- 사용자 구독에서 해당 계획을 제거 하는 경우에는 추가 요금제는 사용자 구독에 추가 되는 계획을 삭제할 수 없습니다. 계획에는 추가 요금제를 참조 하는 구독도 삭제 될 때까지 상태로 유지 됩니다. 

<!--2760466 – IS  ASDK --> 
- 이 버전을 실행 하는 새 Azure Stack 환경에 설치한 경우이 경고는 나타냅니다 *활성화 필요* 표시 되지 않을 수 있습니다. [활성화](../azure-stack-registration.md) 마켓플레이스 배포를 사용 하려면 먼저 필요 합니다. 

<!-- TBD - IS ASDK --> 
- 1804 버전을 사용 하 여 도입 된 관리 구독 유형에 사용할 수 없습니다. 구독 유형은 **구독을 계량**, 및 **소비 구독**합니다. 이러한 구독 유형은 **구독을 계량**, 및 **소비 구독**합니다. 이러한 구독 유형에 1804 버전부터 새 Azure Stack 환경에서 표시 되지만 아직 사용할 준비가 완료 됩니다. 계속 사용 해야 합니다 **기본 공급자 구독** 형식입니다.

<!-- 2403291 - IS ASDK --> 
- 관리자 및 사용자 포털의 아래쪽에 가로 스크롤 막대를 사용 하 여가 없을 수 있습니다. 가로 스크롤 막대에 액세스할 수 없는 경우 블레이드의 이름을 선택 하 여 포털에서 이전 블레이드로 이동 하려면 이동 경로 상단에 있는 이동 경로 탐색 목록에서 확인 하려는 사용 하 여 포털의 왼쪽입니다.
  ![이동 경로 탐색](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

<!-- TBD -  IS ASDK --> 
- Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

<!--  TBD | ASDK -->  
- 이제 Azure Stack 배포에 대 한 기본 표준 시간대는 UTC로 설정 가져오기 됩니다. 선택할 수 있습니다 시간대 Azure Stack을 설치할 때 자동으로 되돌아갑니다 UTC로 기본적으로 설치 하는 동안 있지만.

#### <a name="health-and-monitoring"></a>상태 및 모니터링
<!-- 1264761 - IS ASDK -->  
- 에 대 한 경고를 표시 될 수 있습니다 합니다 *상태 컨트롤러* 다음 세부 정보는 구성 요소:  

   경고 # 1:
   - 이름:  인프라 역할 비정상
   - 심각도: Warning
   - 구성 요소입니다. 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 검색 프로그램을 사용할 수 없는 경우 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  # 2를 경고 합니다.
   - 이름:  인프라 역할 비정상
   - 심각도: Warning
   - 구성 요소입니다. 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너 제공 되지 않습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고가 모두 안전 하 게 무시 될 수 있으며 시간이 지남에 따라 자동으로 닫힙니다.  

<!-- 2368581 - IS. ASDK --> 
- 메모리 부족 경고를 받게 테 넌 트 가상 머신을 사용 하 여 배포 하는 데 실패 한 경우 Azure Stack 운영자를 *패브릭 VM 만들기 오류*, 사용 가능한 메모리가 부족 합니다. Azure Stack 스탬프는 불가능 합니다. 사용 된 [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 워크 로드에 대 한 사용 가능한 용량을 가장 잘 알아야 합니다.

<!-- TBD - IS. ASDK --> 
- 실행 하는 경우는 **테스트 AzureStack** privileged 끝점 (PEP)에서 cmdlet을 **Azure Stack 인프라 역할 인스턴스 성능** 테스트 ERCS VM에 대 한 경고 메시지가 생성 됩니다. 안전 하 게 경고 메시지를 무시 하 고 계속 ASDK를 사용할 수 있습니다.

#### <a name="compute"></a>컴퓨팅
<!-- 2494144 - IS, ASDK --> 
- 일부 F 시리즈 VM 크기는 가상 머신 배포에 대 한 가상 머신 크기를 선택할 때 표시 되지 일부로 VM을 만들 때 크기 선택기입니다. VM 크기 선택기에 표시 되지 않습니다. *F8s_v2*, *F16s_v2*합니다 *F32s_v2*, 및 *F64s_v2*합니다.  
  해결 방법으로 VM을 배포 하려면 다음 방법 중 하나를 사용 합니다. 각 메서드를 사용 하려면 VM 크기를 지정 해야 합니다.

- <!-- 3099544 – IS, ASDK --> Azure Stack 포털을 사용 하 여 새 가상 컴퓨터 (VM)를 만든 경우 VM 크기를 선택 하면 u s D/월 열이 사용 하 여 표시 됩니다는 **사용할 수 없습니다** 메시지입니다. 이 열이 나타나지 않아야 합니다. 표시 된 VM 가격 책정 열에서에서 지원 되지 않습니다 Azure Stack.

- <!-- 2869209 – IS, ASDK --> 사용 하는 경우는 [ **추가 AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0)를 사용 해야 합니다는 **-OsUri** storage 계정과 디스크 업로드 되는 URI 매개 변수입니다. 디스크의 로컬 경로 사용 하는 경우 cmdlet은 다음 오류로 인해 실패 합니다. *장기 실행 작업 상태 '실패'를 사용 하 여 실패 한*합니다. 

- <!--  2966665 – IS, ASDK --> 데이터 디스크를 프리미엄 크기 디스크 virtual machines (DS, DSv2, Fs, Fs_V2) 실패 오류가 발생 하 여 관리 되는 SSD에 연결:  *가상 머신 'vmname' 오류에 대 한 디스크를 업데이트 하지 못했습니다. 요청한 VM 크기에 대 한 저장소 계정 유형 'Premium_LRS' 지원 되지 않으므로 작업을 수행할 수 없습니다 ' Standard_DS/Ds_V2/FS/Fs_v2)*

   이 문제를 해결 하려면 사용 하 여 *Standard_LRS* 대신 데이터 디스크 *Premium_LRS 디스크*합니다. 이용 *Standard_LRS* 데이터 디스크 IOPs 또는 비용을 변경 하지 않습니다.  

- <!--  2795678 – IS, ASDK --> 가상 머신 (VM)를 만들려면 포털 (DS, Ds_v2, FS, FSv2) 프리미엄 VM 크기에서를 사용 하면 표준 저장소 계정에 VM이 생성 됩니다. 표준 저장소 계정 만드는 영향을 주지 않습니다 기능적으로 IOPs 또는 대금 청구 합니다. 

   안전 하 게 되었다는 경고를 무시할 수 있습니다. *에서는 표준 디스크를 프리미엄 디스크를 지 원하는 크기를 사용 하기로 했습니다. 이 운영 체제 성능에 영향을 줄 수 있으며 권장 되지 않습니다. Premium storage (SSD)를 대신 사용 하는 것이 좋습니다.*

- <!-- 2967447 - IS, ASDK --> 가상 머신 확장 집합 (VMSS) 환경을 만드는 7.2 CentOS 기반 배포에 대 한 옵션으로 제공 합니다. 해당 이미지를 Azure Stack에서 사용할 수 없으므로 배포에 대 한 다른 OS를 선택 하거나 연산자가 marketplace에서 배포 하기 전에 다운로드 된 다른 CentOS 이미지를 지정 하는 ARM 템플릿을 사용 하 여 합니다.

<!-- TBD -  IS ASDK --> 
- 가상 머신 확장 집합에 대 한 크기 조정 설정을 포털에서 사용할 수 없는 경우 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이로 인해 사용 해야 합니다 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

<!-- TBD -  IS ASDK --> 
- Azure Stack 사용자 포털에서 가상 컴퓨터를 만들 때 포털 잘못 된 D 시리즈 VM을 연결할 수 있는 데이터 디스크 수를 표시 합니다. 모든 지원 되는 D 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

<!-- TBD -  IS ASDK --> 
- VM 이미지를 만들려는 못하면 VM 이미지 계산 블레이드로 삭제할 수 없습니다는 실패 한 항목을 추가할 수 있습니다.

  해결 방법으로 Hyper-v를 통해 만들 수 있는 더미 VHD를 사용 하 여 새 VM 이미지 만들기 (새 VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하는 것을 금지 하는 문제를 수정 해야 합니다. 그런 다음 더미 이미지를 만든 후 15 분 삭제할 수 있습니다 했습니다.

  그런 다음 이전에 실패 한 VM 이미지의 다운로드를 다시 시도할 수 있습니다.

<!-- TBD -  IS ASDK --> 
- VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

<!-- 1662991 - IS ASDK --> 
- Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다.

<!-- 2724961- IS ASDK --> 
- 등록할 때를 **Microsoft.Insight** 리소스 공급자 구독 설정에서 Windows VM 만들기 및 VM 개요 페이지 게스트 OS 진단 활성화를 사용 하 여 메트릭 데이터를 표시 하지 않습니다. 

 

#### <a name="networking"></a>네트워킹
<!-- 1766332 - IS, ASDK --> 
- 아래 **네트워킹**를 클릭 하면 **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택하지 마세요. 만 **경로 기반** 옵션은 Azure Stack에서 지원 됩니다.

<!-- 1902460 -  IS ASDK --> 
- Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

<!-- 16309153 -  IS ASDK --> 
- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.

<!-- 2702741 -  IS ASDK --> 
- 동적 할당 메서드를 사용 하 여 배포 되는 공용 Ip 실행 중지-할당 취소 후에 유지 되도록 보장 되지 않습니다.

<!-- 2529607 - IS ASDK --> 
- Azure Stack 하는 동안 *암호 회전*에는 공용 IP 주소에 연결할 수 없는 2 ~ 5 분에 대 한 간격이 있습니다.

<!-- 2664148 - IS ASDK --> 
- 여기서 테 넌 트에 액세스 하는 가상 컴퓨터는 S2S VPN 터널을 사용 하 여 시나리오에서는 온-프레미스 서브넷이 게이트웨이 이미 만든 후 로컬 네트워크 게이트웨이를 추가한 경우 연결 시도가 실패 하는 있는 경우를 발생할 수 있습니다. 


#### <a name="sql-and-mysql"></a>SQL 및 MySQL
<!-- TBD - ASDK --> 
- 리소스 공급자 및 사용자 작업에 사용할 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. App Services를 비롯 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

<!-- IS, ASDK --> 
- 공간 및 마침표를 포함 하 여 특수 문자를 사용할 수 없습니다는 **제품군** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름입니다.

#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

<!-- TBD - IS ASDK --> 
- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.  

<!-- TBD - IS ASDK --> 
- App Service에 배포할 수 있습니다 합니다 *기본 공급자 구독* 지금은 합니다.

#### <a name="usage"></a>사용 현황  
<!-- TBD -  IS ASDK --> 
- 사용량 공용 IP 주소 사용 측정기 데이터를 보여 줍니다 동일 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.

<!-- #### Identity -->

