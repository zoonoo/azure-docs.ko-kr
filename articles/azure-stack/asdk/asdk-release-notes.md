---
title: Microsoft Azure Stack 개발 키트 릴리스 정보 | Microsoft Docs
description: 향상 된 기능, 수정 및 Azure Stack 개발 키트에 대 한 알려진된 문제입니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 86ac1f1b5433104faa89e1f107fa36fc1da5f70e
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989897"
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 개발 키트 릴리스 정보  
이러한 릴리스 정보에서는 향상 된 기능, 수정 및 Azure Stack 개발 키트의 알려진된 문제에 대 한 정보를 제공 합니다. 실행 중인 버전을 잘 모를 경우 [포털을 사용 하 여 확인](.\.\azure-stack-updates.md#determine-the-current-version)합니다.

> 에서 새로운 기능을 ASDK 구독 하 여 최신 합니다 [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [피드](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)합니다.

## <a name="build-11805147"></a>1.1805.1.47 빌드

> [!TIP]  
> 버전 스키마에 사용 하 여 Microsoft Azure Stack에 대 한 업데이트가 있는지 고객 피드백에 따라 합니다. 새 스키마 1805,이 업데이트로 시작 현재 클라우드 버전을 나타내는 더 잘 합니다.  
> 
> 버전 스키마는 이제 *Version.YearYearMonthMonth.MinorVersion.BuildNumber* 버전 및 릴리스는 두 번째 및 세 번째 집합 여기서 나타냅니다. 예를 들어 1805.1 나타냅니다 합니다 *제조 릴리스에서* 1805 (RTM) 버전.  


### <a name="new-features"></a>새로운 기능 
이 빌드 Azure Stack에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.  

- <!-- 2297790 - IS, ASDK --> **이제 azure Stack을 *Syslog* 클라이언트** 으로 *미리 보기 기능*합니다. 이 클라이언트를 Syslog 서버 또는 보안 정보 및 이벤트 관리 (SIEM) 소프트웨어의 외부 Azure Stack에는 Azure Stack 인프라와 관련 된 감사 및 보안 로그를 전달할 수 있습니다. 현재 Syslog 클라이언트는만 기본 포트 514 통해 인증 되지 않은 UDP 연결을 지원합니다. 각 Syslog 메시지의 페이로드에 공통 이벤트 형식 (CEF) 지정 됩니다. 

  Syslog 클라이언트를 구성 하려면 사용 합니다 **집합 SyslogServer** cmdlet은 권한 있는 끝점에 노출 합니다. 

  이 미리 보기에서는 다음 세 가지 경고를 볼 수 있습니다. 이러한 경고를 Azure Stack에서 제공 하는 경우 포함 *설명을* 하 고 *재구성* 지침입니다. 
  - 제목: 코드 무결성 해제  
  - 제목: 코드 무결성 감사 모드 
  - 제목: 사용자 계정 생성

  이 기능은 미리 보기 상태인 동안 해당 신뢰 해서는 안 됩니다 시 프로덕션 환경에서.   


### <a name="fixed-issues"></a>해결된 문제
- 차단 문제를 해결 했습니다 [드롭다운 목록에서 새 지원 요청 열기](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 포털 내에서. 이 옵션을 이제는 의도 한 대로 작동 합니다. 

- **다양 한 수정** 성능, 안정성, 보안 및 Azure Stack에서 사용 되는 운영 체제에 대 한


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- <!-- 2551834 - IS, ASDK --> 선택 하면 **개요** 관리자 또는 사용자의 정보를 포털에서 저장소 계정에 대 한 합니다 *Essentials* 창 표시 되지 않습니다.  권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 *권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링*합니다.  Azure Stack의 업데이트 관리 개요를 참조 하세요 *Azure Stack 개요에 대 한 업데이트 관리*합니다.  

  Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0)합니다. 

- <!-- 2551834 - IS, ASDK --> 선택 하면 **태그** 관리자 또는 사용자 포털에서 저장소 계정에 대 한 정보를 로드 하지 못하는 표시 하지 않습니다.  

  사용할 수 없는 정보를 보려면 다음을 사용 합니다 [Get-azurermtag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell cmdlet.

- <!-- TBD - IS ASDK --> 새로운 관리 구독 유형의 넣지 마십시오 *구독을 계량*, 및 *소비 구독*합니다. 이러한 새 구독 유형과 버전 1804 도입 되지만 아직 사용할 준비가 되지 않습니다. 계속 사용 해야 합니다 *기본 공급자* 구독 유형입니다.  

- <!-- 2403291 - IS ASDK --> 관리자 및 사용자 포털의 아래쪽에 가로 스크롤 막대를 사용 하 여가 없을 수 있습니다. 가로 스크롤 막대에 액세스할 수 없는 경우 블레이드의 이름을 선택 하 여 포털에서 이전 블레이드로 이동 하려면 이동 경로 상단에 있는 이동 경로 탐색 목록에서 확인 하려는 사용 하 여 포털의 왼쪽입니다.
  ![이동 경로 탐색](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- <!-- TBD -  IS ASDK --> Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.


#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  에 대 한 경고를 표시 될 수 있습니다 합니다 *상태 컨트롤러* 다음 세부 정보는 구성 요소:  

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

  # 1과 2 모두 경고를 안전 하 게 무시할 수 있습니다 하 고 시간이 지남에 따라 자동으로 닫을 수 있습니다. 

  에 대 한 다음과 같은 경고가 나타날 *용량*합니다. 이 경고에 대 한 설명에서 식별 하는 사용 가능한 메모리 비율로 달라질 수 있습니다.  

  # 3을 경고 합니다.
   - 이름: 메모리 용량
   - 심각도: 위험
   - 구성 요소: 용량
   - 설명: 지역 80.00% 이상의 사용 가능한 메모리를 사용 했습니다. 많은 양의 메모리를 사용 하 여 가상 컴퓨터를 만들기가 실패할 수 있습니다.  

  Azure Stack의이 버전에서는이 경고 발생 시킬 수 없습니다 올바르게 합니다. 테 넌 트 가상 컴퓨터를 성공적으로 배포를 계속 하는 경우이 경고를 안전 하 게 무시할 수 있습니다. 
  
  경고 3 자동으로 닫히지 않습니다. 이 경고를 종결 하는 경우 Azure Stack에서 15 분 이내 동일한 경고를 만듭니다.  

- <!-- 2368581 - IS ASDK --> 메모리 부족 경고를 받게 테 넌 트 가상 머신을 사용 하 여 배포 하는 데 실패 한 경우 Azure Stack 운영자를 *패브릭 VM 만들기 오류*, 사용 가능한 메모리가 부족 합니다. Azure Stack 스탬프는 불가능 합니다. 사용 된 [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 워크 로드에 대 한 사용 가능한 용량을 가장 잘 알아야 합니다. 


#### <a name="compute"></a>컴퓨팅
- <!-- TBD - IS, ASDK --> 일부 F 시리즈 VM 크기는 가상 머신 배포에 대 한 가상 머신 크기를 선택할 때 표시 되지 일부로 VM을 만들 때 크기 선택기입니다. 다음 VM 크기 선택기에 표시 되지 않습니다: *F8s_v2*, *F16s_v2*, *F32s_v2*, 및 *F64s_v2*합니다.  
  해결 방법으로 VM을 배포 하려면 다음 방법 중 하나를 사용 합니다. 각 메서드를 사용 하려면 VM 크기를 지정 해야 합니다.

  - **Azure Resource Manager 템플릿:** 템플릿을 사용 하는 경우 설정 된 *vmSize* 사용 하려는 VM 크기와 같아야 하는 템플릿에 합니다. 다음 항목을 사용 하는 VM을 배포 하는 예를 들어 합니다 *F32s_v2* 크기:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** 사용할 수는 [az vm 만들기](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 명령 및 유사한 매개 변수로 VM 크기를 지정할 `--size "Standard_F32s_v2"`합니다.

  - **PowerShell:** Powershell을 사용할 수 있습니다 [New-azurermvmconfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) 비슷합니다 VM 크기를 지정 하는 매개 변수를 사용 하 여 `-VMSize "Standard_F32s_v2"`입니다.


- <!-- TBD -  IS ASDK --> 가상 머신 확장 집합에 대 한 크기 조정 설정을 포털에서 사용할 수 없는 경우 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이로 인해 사용 해야 합니다 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- <!-- TBD -  IS ASDK --> Azure Stack 사용자 포털에서 가상 컴퓨터를 만들 때 포털 잘못 된 D 시리즈 VM을 연결할 수 있는 데이터 디스크 수를 표시 합니다. 모든 지원 되는 D 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- <!-- TBD -  IS ASDK --> VM 이미지를 만들려는 못하면 VM 이미지 계산 블레이드로 삭제할 수 없습니다는 실패 한 항목을 추가할 수 있습니다.

  해결 방법으로 Hyper-v를 통해 만들 수 있는 더미 VHD를 사용 하 여 새 VM 이미지 만들기 (새 VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하는 것을 금지 하는 문제를 수정 해야 합니다. 그런 다음 더미 이미지를 만든 후 15 분 삭제할 수 있습니다 했습니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드를 시도할 수 있습니다.

- <!-- TBD -  IS ASDK --> VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

- <!-- 1662991 - IS ASDK --> Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다. 

#### <a name="networking"></a>네트워킹
- <!-- TBD - IS ASDK --> 관리자 또는 사용자 포털 중 하나에서 사용자 정의 경로 만들 수입니다. 대 안으로 사용 하 여 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell)합니다.

- <!-- 1766332 - IS, ASDK --> 아래 **네트워킹**를 클릭 하면 **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 만 **경로 기반** 옵션은 Azure Stack에서 지원 됩니다.

- <!-- 2388980 -  IS ASDK --> VM을 만들고 공용 IP 주소와 연결 된 후 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 연결 해제 하려면 나타나지만 이전에 할당된 된 공용 IP 주소 유지 원래 VM과 연결 됩니다.

  현재, 만든 새 Vm에 대 한 새 공용 IP 주소만 사용 해야 합니다.

  Azure Stack의 업데이트 관리 개요를 참조 하세요 *Azure Stack 개요에 대 한 업데이트 관리*합니다. 앞으로의 모든 새 아닌 원본 VM에 연결에서이 IP 주소 결과 통해 연결 하려고 시도 합니다.


- <!-- 2292271 - IS ASDK --> 제품 및 테 넌 트 구독을 사용 하 여 연결 된 계획의 일부인 네트워크 리소스에 대 한 할당량 한도 늘릴 경우 해당 구독에 새 제한이 적용 되지 않습니다. 그러나 할당량 증가 이후에 만든 새 구독에 새 제한이 적용지 않습니다. 

  이 문제를 해결 하려면 계획을 이미 구독과 연결 된 경우 네트워크 할당량을 늘리려면는 추가 요금제를 사용 합니다. 자세한 내용은 참조 하는 방법 [는 추가 요금제를 사용할 수 있도록](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)입니다.

- <!-- 2304134 IS ASDK --> DNS 영역 리소스 또는 연결 된 경로 테이블 리소스는 구독을 삭제할 수 없습니다. 구독을 성공적으로 삭제 하려면 먼저 테 넌 트 구독에서 DNS 영역 및 경로 테이블 리소스를 삭제 해야 합니다. 


- <!-- 1902460 -  IS ASDK --> Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

- <!-- 16309153 -  IS ASDK --> DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.
 
- <!-- TBD -  IS ASDK --> Azure Stack VM을 배포한 후 추가 네트워크 인터페이스 VM 인스턴스를 추가 하는 것을 지원 하지 않습니다. VM이 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.


#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- <!-- TBD - ASDK --> 리소스 공급자 및 사용자 작업에 사용할 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. App Services를 비롯 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

- <!-- IS, ASDK --> 공간 및 마침표를 포함 하 여 특수 문자를 사용할 수 없습니다는 **제품군** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름입니다. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- <!-- TBD - IS ASDK --> (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.  

- <!-- TBD - IS ASDK --> App Service에 배포할 수 있습니다 합니다 *기본 공급자 구독* 지금은 합니다. 향후 업데이트를 새 App Service 배포 *구독을 계량* 는 Azure Stack 1804에서 도입 되었습니다. 계량 사용에 대 한 지원 되는 경우이 새 구독 형식으로 기존의 모든 배포를 마이그레이션합니다.

#### <a name="usage"></a>사용 현황  
- <!-- TBD -  IS ASDK --> 사용량 공용 IP 주소 사용 측정기 데이터를 보여 줍니다 동일 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.

<!-- #### Identity -->



## <a name="build-201805131"></a>20180513.1 빌드

### <a name="new-features"></a>새로운 기능 
이 빌드 Azure Stack에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.  

- <!-- 1759172 - IS, ASDK --> **새 관리 구독**합니다. 1804를 사용 하 여 두 가지가 새 구독 포털에서 사용할 수 있습니다. 이러한 새 구독 유형과 되며 기본 공급자 구독 외에도 표시 1804 버전부터 새 Azure Stack 설치 합니다. *이 버전의 Azure Stack을 사용 하 여 이러한 새 구독 유형과 넣지 마십시오*합니다. 향후 업데이트를 사용 하 여 이러한 구독 형식을 사용 하 여 가용성을 발표할 예정입니다. 

  이러한 새 구독 유형은 표시 되지만 기본 공급자 구독을 보호 하 고 SQL 호스팅 서버와 같은 공유 리소스를 배포할 수 있도록 더 큰 변경의 일부입니다. 

  이제 사용할 수 있는 세 가지 구독 형식은 다음과 같습니다.  
  - 공급자 구독을 기본:이 구독 유형을 사용 하 여 계속 합니다. 
  - 구독을 계량: *이 구독 유형은 사용 하지 않습니다.*
  - 소비 구독: *이 구독 형식을 사용 하지 마세요*

### <a name="fixed-issues"></a>해결된 문제
- <!-- IS, ASDK -->  관리 포털에서 더 이상 정보를 표시 하기 전에 업데이트 타일 새로 고침 해야 합니다. 

- <!-- 2050709 - IS, ASDK -->  이제 Blob service, Table service 및 큐 서비스에 대 한 저장소 메트릭을 편집 하려면 관리 포털을 사용할 수 있습니다.

- <!-- IS, ASDK --> 아래 **네트워킹**클릭 **연결** VPN 연결을 설정 하려면 **사이트-사이트 간 (IPsec)** 경우만 사용할 수 있는 옵션이 있습니다. 

- **다양 한 수정** 성능, 안정성, 보안 및 Azure Stack에서 사용 되는 운영 체제에 대 한

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>이 업데이트를 사용 하 여 시간이 추가 릴리스  
다음 이제 사용할 수 있지만 Azure Stack 업데이트 1804 필요 하지 않습니다.
- **모니터링 팩은 Microsoft Azure Stack System Center Operations Manager에 업데이트**합니다. Microsoft System Center Operations Manager 모니터링 팩 Azure Stack 용의 새 버전 (1.0.3.0)을 사용할 수 있습니다 [다운로드](https://www.microsoft.com/download/details.aspx?id=55184)합니다. 이 버전을 사용 하 여 연결 된 Azure Stack 배포를 추가 하면 서비스 주체를 사용할 수 있습니다. 이 버전에는 또한 Operations Manager 내에서 직접 수정 작업을 수행 할 수 있는 업데이트 관리 환경을 갖추고 있습니다. 리소스 공급자를 표시, 배율 단위를 및 단위 노드의 크기를 조정 하는 새 대시보드도 있습니다.

- **새 Azure Stack 관리자 PowerShell 버전 1.3.0**합니다.  Azure Stack PowerShell 1.3.0 설치에 대 한 출시 되었습니다. 이 버전에는 Azure Stack을 관리 하는 모든 관리자 리소스 공급자에 대 한 명령을 제공 합니다.  이 릴리스를 사용 하 여 일부 콘텐츠가 Azure Stack 도구 GitHub에서 중단 예정 [리포지토리](https://github.com/Azure/AzureStack-Tools)합니다. 

   설치 세부 정보를 수행 합니다 [지침](.\.\azure-stack-powershell-install.md) 또는 [도움말](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) 1.3.0 Azure Stack 모듈에 대 한 콘텐츠. 

- **Azure Stack API Rest 참조 릴리스의 초기**합니다. 합니다 [모든 Azure Stack 관리자 리소스 공급자에 대 한 API 참조](https://docs.microsoft.com/rest/api/azure-stack/) 이제 게시 되었습니다. 

### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- <!-- TBD - IS ASDK --> 기능 [드롭다운 목록에서 새 지원 요청을 열려면](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털을 사용할 수 없습니다. 다음 링크를 대신 사용 합니다.     
    - Azure Stack Development Kit를 사용 하 여 https://aka.ms/azurestackforum입니다.    

- <!-- 2403291 - IS ASDK --> 관리자 및 사용자 포털의 아래쪽에 가로 스크롤 막대를 사용 하 여가 없을 수 있습니다. 가로 스크롤 막대에 액세스할 수 없는 경우 블레이드의 이름을 선택 하 여 포털에서 이전 블레이드로 이동 하려면 이동 경로 상단에 있는 이동 경로 탐색 목록에서 확인 하려는 사용 하 여 포털의 왼쪽입니다.
  ![이동 경로 탐색](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- <!-- TBD -  IS ASDK --> Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

-   <!-- TBD -  IS ASDK --> 관리 포털에서 Microsoft.Update.Admin 구성 요소에 대 한 중요 한 경고를 볼 수 있습니다. 경고 이름, 설명 및 수정으로 모두 표시 합니다.  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

    이 경고는 무시 해도 됩니다. 

#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  에 대 한 경고를 표시 될 수 있습니다 합니다 *상태 컨트롤러* 다음 세부 정보는 구성 요소:  

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

  두 가지 경고는 무시 해도 됩니다. 이러한 작업은 시간이 지남에 따라 자동으로 종료 됩니다.  

#### <a name="marketplace"></a>Marketplace
- 사용자를 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 및 계획 및 제품은 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.
 
#### <a name="compute"></a>컴퓨팅
- <!-- TBD -  IS ASDK --> 가상 머신 확장 집합에 대 한 크기 조정 설정을 포털에서 사용할 수 없는 경우 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이로 인해 사용 해야 합니다 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- <!-- TBD -  IS ASDK --> Azure Stack 사용자 포털에서 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- <!-- TBD -  IS ASDK --> VM 이미지를 만들려는 못하면 VM 이미지 계산 블레이드로 삭제할 수 없습니다는 실패 한 항목을 추가할 수 있습니다.

  해결 방법으로 Hyper-v를 통해 만들 수 있는 더미 VHD를 사용 하 여 새 VM 이미지 만들기 (새 VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하는 것을 금지 하는 문제를 수정 해야 합니다. 그런 다음 더미 이미지를 만든 후 15 분 삭제할 수 있습니다 했습니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드를 시도할 수 있습니다.

- <!-- TBD -  IS ASDK --> VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

- <!-- 1662991 - IS ASDK --> Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다. 

#### <a name="networking"></a>네트워킹
- <!-- 1766332 - IS, ASDK --> 아래 **네트워킹**를 클릭 하면 **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 만 **경로 기반** 옵션은 Azure Stack에서 지원 됩니다.

- <!-- 2388980 -  IS ASDK --> VM을 만들고 공용 IP 주소와 연결 된 후 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 연결 해제 하려면 나타나지만 이전에 할당된 된 공용 IP 주소 유지 원래 VM과 연결 됩니다.

  현재, 만든 새 Vm에 대 한 새 공용 IP 주소만 사용 해야 합니다.

  Azure Stack의 업데이트 관리 개요를 참조 하세요 *Azure Stack 개요에 대 한 업데이트 관리*합니다. 앞으로의 모든 새 아닌 원본 VM에 연결에서이 IP 주소 결과 통해 연결 하려고 시도 합니다.

- <!-- 2292271 - IS ASDK --> 제품 및 테 넌 트 구독을 사용 하 여 연결 된 계획의 일부인 네트워크 리소스에 대 한 할당량 한도 늘릴 경우 해당 구독에 새 제한이 적용 되지 않습니다. 그러나 할당량 증가 이후에 만든 새 구독에 새 제한이 적용지 않습니다. 

  이 문제를 해결 하려면 계획을 이미 구독과 연결 된 경우 네트워크 할당량을 늘리려면는 추가 요금제를 사용 합니다. 자세한 내용은 참조 하는 방법 [는 추가 요금제를 사용할 수 있도록](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)입니다.

- <!-- 2304134 IS ASDK --> DNS 영역 리소스 또는 연결 된 경로 테이블 리소스는 구독을 삭제할 수 없습니다. 구독을 성공적으로 삭제 하려면 먼저 테 넌 트 구독에서 DNS 영역 및 경로 테이블 리소스를 삭제 해야 합니다. 


- <!-- 1902460 -  IS ASDK --> Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

- <!-- 16309153 -  IS ASDK --> DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.
 
- <!-- TBD -  IS ASDK --> Azure Stack VM을 배포한 후 추가 네트워크 인터페이스 VM 인스턴스를 추가 하는 것을 지원 하지 않습니다. VM이 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.


#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- <!-- TBD - ASDK --> 리소스 공급자 및 사용자 작업에 사용할 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. App Services를 비롯 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

- <!-- IS, ASDK --> 공간 및 마침표를 포함 하 여 특수 문자를 사용할 수 없습니다는 **제품군** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름입니다. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- <!-- TBD -  IS ASDK --> (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.
 
#### <a name="usage"></a>사용 현황  
- <!-- TBD -  IS ASDK --> 사용량 공용 IP 주소 사용 측정기 데이터를 보여 줍니다 동일 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure Stack 도구를 다운로드합니다.
- 사용 하는 경우는 *호출할 webrequest* Github에서 Azure Stack을 다운로드 하기 위한 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)로 인해 발생합니다. 자세한 내용은 [취약 한 암호화 표준 제거 공지](https://githubengineering.com/crypto-removal-notice/)합니다.

<!-- #### Identity -->



## <a name="build-201803021"></a>20180302.1 빌드

### <a name="new-features-and-fixes"></a>새 기능 및 수정
새로운 기능 및 Azure Stack 통합된 시스템 버전 1803 Azure Stack 개발 키트에 적용에 대 한 릴리스 수정 합니다. 참조를 [새로운 기능](.\.\azure-stack-update-1803.md#new-features) 및 [문제를 해결](.\.\azure-stack-update-1803.md#fixed-issues) 섹션에서는 Azure Stack 1803의 세부 정보에 대 한 릴리스 정보를 업데이트 합니다.  
> [!IMPORTANT]    
> 에 나열 된 항목 중 일부는 **새로운 기능** 하 고 **문제를 해결** 섹션에서는 Azure Stack 통합 시스템에만 적용 됩니다.

### <a name="changes"></a>변경 내용
- 새로 만든된 제품의 상태를 변경 하는 방법은 *사설* 하 *공용* 또는 *해제* 변경 되었습니다. 자세한 내용은 [제품을 만드는](.\.\azure-stack-create-offer.md)합니다. 


### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- 기능 [드롭다운 목록에서 새 지원 요청을 열려면](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털을 사용할 수 없습니다. 다음 링크를 대신 사용 합니다.     
    - Azure Stack Development Kit를 사용 하 여 https://aka.ms/azurestackforum입니다.    

- <!-- 2050709 --> 관리 포털에서 Blob service, Table service 또는 Queue 서비스에 대 한 저장소 메트릭을 편집할 수 없는 합니다. 저장소로 이동 하 고 blob, 테이블 또는 큐 서비스 타일을 선택 하는 경우 해당 서비스에 대 한 메트릭 차트를 표시 하는 새 블레이드가 열립니다. 메트릭 차트 타일의 맨 위에서 다음 편집을 선택 하는 경우 차트 편집 블레이드의 열리지만 메트릭을 편집 하는 옵션을 표시 하지 않습니다.  

- 표시는 **활성화 필요** Azure Stack 개발 키트를 등록 해야 한다는 경고 합니다. 이 동작은 예상 됩니다.

- 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- 관리 포털의 대시보드에서 타일 업데이트는 업데이트에 대 한 정보를 표시 하려면 실패 합니다. 이 문제를 해결 하려면 타일을 새로 고치려면 클릭 합니다.

-   관리 포털에서 Microsoft.Update.Admin 구성 요소에 대 한 중요 한 경고를 볼 수 있습니다. 경고 이름, 설명 및 수정으로 모두 표시 합니다.  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

    이 경고는 무시 해도 됩니다. 

- 관리 포털 및 사용자 포털 모두에서 개요 블레이드에서 이전 API 버전을 사용 하 여 생성 된 저장소 계정에 대 한 개요 블레이드를 선택 하면 로드에 실패 (예: 2015-06-15). 

  이 문제를 해결 하려면 PowerShell을 사용 합니다 **시작 ResourceSynchronization.ps1** 저장소 계정 세부 정보에 대 한 액세스를 복원 하는 스크립트입니다. [이 스크립트는 GitHub에서 사용할 수 있는]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), ASDK를 사용 하는 경우 개발 키트 호스트에서 서비스 관리자 자격 증명을 사용 하 여 실행 해야 합니다.  

- 합니다 **서비스 상태** 블레이드가 로드 하지 못했습니다. 관리자 또는 사용자 포털, Azure Stack에서에서 서비스 상태 블레이드를 열 때 오류를 표시 하 고 정보를 로드 하지 않습니다. 이는 정상적인 동작입니다. 선택 하 고 서비스 상태를 열 수 있지만이 기능은 아직 제공 되지 되지만 Azure Stack의 이후 버전에서 구현 됩니다.


#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  에 대 한 경고를 표시 될 수 있습니다 합니다 *상태 컨트롤러* 다음 세부 정보는 구성 요소:  

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

  두 가지 경고는 무시 해도 됩니다. 이러한 작업은 시간이 지남에 따라 자동으로 종료 됩니다.  

- Azure Stack 관리 포털에서 이름 사용 하 여 중요 한 경고가 표시 될 수 있습니다 **외부 인증서 만료 보류 중인**합니다.  이 경고는 무시 해도 됩니다 및 Azure Stack 개발 키트의 작업에 영향 을지 않습니다. 


#### <a name="marketplace"></a>Marketplace
- 사용자를 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 및 계획 및 제품은 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.
 
#### <a name="compute"></a>컴퓨팅
- 가상 머신 확장 집합에 대 한 크기 조정 설정을 포털에서 사용할 수 없는 경우 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이로 인해 사용 해야 합니다 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- Azure Stack 사용자 포털에서 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- VM 이미지를 만들려는 못하면 VM 이미지 계산 블레이드로 삭제할 수 없습니다는 실패 한 항목을 추가할 수 있습니다.

  해결 방법으로 Hyper-v를 통해 만들 수 있는 더미 VHD를 사용 하 여 새 VM 이미지 만들기 (새 VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하는 것을 금지 하는 문제를 수정 해야 합니다. 그런 다음 더미 이미지를 만든 후 15 분 삭제할 수 있습니다 했습니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드를 시도할 수 있습니다.

-  VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

- <!-- 1662991 --> Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다. 


#### <a name="networking"></a>네트워킹
- 아래 **네트워킹**를 클릭 하면 **연결** VPN 연결을 설정 하려면 **VNet 대 VNet** 가능한 연결 유형으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 현재만 합니다 **사이트-사이트 간 (IPsec)** 옵션은 지원 됩니다.

- VM을 만들고 공용 IP 주소와 연결 된 후 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 연결 해제 하려면 나타나지만 이전에 할당된 된 공용 IP 주소 유지 원래 VM과 연결 됩니다.

  현재, 만든 새 Vm에 대 한 새 공용 IP 주소만 사용 해야 합니다.

  Azure Stack의 업데이트 관리 개요를 참조 하세요 *Azure Stack 개요에 대 한 업데이트 관리*합니다. 앞으로의 모든 새 아닌 원본 VM에 연결에서이 IP 주소 결과 통해 연결 하려고 시도 합니다.



- Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.
 
- Azure Stack VM을 배포한 후 추가 네트워크 인터페이스 VM 인스턴스를 추가 하는 것을 지원 하지 않습니다. VM이 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.



#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- 새 SQL 또는 MySQL SKU에서 사용자 데이터베이스를 만들 수 전에 최대 1 시간이 걸릴 수 있습니다.

- 리소스 공급자 및 사용자 작업에 사용할 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. App Services를 비롯 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

- <!-- IS, ASDK --> 공간 및 마침표를 포함 하 여 특수 문자를 사용할 수 없습니다는 **제품군** 또는 **계층** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름을 지정 합니다.

#### <a name="app-service"></a>App Service
- 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.
 
#### <a name="usage"></a>사용 현황  
- 사용량 공용 IP 주소 사용 측정기 데이터를 보여 줍니다 동일 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure Stack 도구를 다운로드합니다.
- 사용 하는 경우는 *호출할 webrequest* Github에서 Azure Stack을 다운로드 하기 위한 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)로 인해 발생합니다. 자세한 내용은 [취약 한 암호화 표준 제거 공지](https://githubengineering.com/crypto-removal-notice/)합니다.

  이 문제를 해결 하려면 추가 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` GitHub 리포지토리에서 다운로드 시 TLSv1.2를 사용 하도록 PowerShell 콘솔을 강제 적용 하는 스크립트의 맨 위로 이동 합니다.





