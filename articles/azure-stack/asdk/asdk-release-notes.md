---
title: Microsoft Azure 스택 개발 키트 릴리스 정보 | Microsoft Docs
description: 향상 된 기능, 수정 및 Azure 스택 개발 키트에 대 한 알려진된 문제입니다.
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
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: bbd9bb0d56dd61fd0a32531ac425a1dbc1aa8923
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295790"
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure 스택 개발 키트 릴리스 정보  
이러한 릴리스 정보에서는 향상 된 기능, 수정 및 Azure 스택 개발 키트의 알려진된 문제에 대 한 정보를 제공 합니다. 실행 중인 버전을 잘 모르는 경우 다음을 할 수 있습니다 [포털을 사용 하 여](.\.\azure-stack-updates.md#determine-the-current-version)합니다.

> 구독 하 여 ASDK는의 새로운 기능을 통해 최신 정보는 [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [피드](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)합니다.

## <a name="build-11805147"></a>빌드 1.1805.1.47

> [!TIP]  
> Microsoft Azure 스택에 사용 중인 버전 스키마에는 업데이트는 고객의 의견에 따라. 새 스키마 1805,이 업데이트를 시작 합니다. 현재 클라우드 버전을 나타내는 더 잘 합니다.  
> 
> 버전 스키마는 이제 *Version.YearYearMonthMonth.MinorVersion.BuildNumber* 두 번째 및 세 번째 집합의 버전 및 릴리스 여기서 나타냅니다. 예를 들어 1805.1 나타냅니다는 *제조 하도록 릴리스* 1805 (RTM) 버전입니다.  


### <a name="new-features"></a>새로운 기능 
이 빌드 Azure 스택에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.  

- <!-- 2297790 - IS, ASDK --> **Azure 스택에 포함 되어 이제는 *Syslog* 클라이언트** 로 *미리 보기 기능*합니다. 이 클라이언트를 Syslog 서버 또는 보안 정보 및 이벤트 (SIEM) 관리 소프트웨어 외부 Azure 스택에 있는 Azure 스택 인프라와 관련 된 감사 및 보안 로그를 전달할 수 있습니다. 현재 Syslog 클라이언트는만 514 기본 포트를 통해 인증 되지 않은 UDP 연결을 지원합니다. 각 Syslog 메시지의 페이로드에 공통 이벤트 형식 (CEF) 지정 됩니다. 

  Syslog 클라이언트를 구성 하려면 사용 하 여는 **집합 SyslogServer** cmdlet은 권한 있는 끝점에 노출 합니다. 

  이 미리 보기에서는 다음 세 가지 경고를 볼 수 있습니다. 이러한 경고를 포함 하는 Azure 스택에서 나타나면 *설명* 및 *재구성* 지침. 
  - 해제 코드 무결성 제목:  
  - 감사 모드에서 코드 무결성 제목: 
  - 제목: 사용자 계정 생성

  이 기능은 미리 보기 상태인 동안 것 신뢰 해서는 시 프로덕션 환경에서 합니다.   


### <a name="fixed-issues"></a>해결된 문제
- 차단 문제 해결 [드롭다운 목록에서 새 지원 요청을 열](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 포털 내에서. 이 옵션을 지금 의도 한 대로 작동 합니다. 

- **다양 한 수정 프로그램이** 성능, 안정성, 보안 및 Azure 스택에서 사용 되는 운영 체제에 대 한


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- <!-- 2551834 - IS, ASDK --> 선택 하는 경우 **개요** 관리자 또는 사용자의 정보를 포털의 저장소 계정에 대 한는 *Essentials* 창 표시 되지 않습니다.  Essentials 창 같은 계정에 대 한 정보가 표시 됩니다는 *리소스 그룹*, *위치*, 및 *구독 ID*합니다.  다른 옵션에 대 한 개요와 같은 액세스할 수 있는 *서비스* 및 *모니터링*에 옵션으로 *탐색기에에서 열려 있는* 또는 *저장소 계정 삭제* .  

  사용할 수 없는 정보를 보려면 사용 하 여는 [Get azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell cmdlet. 

- <!-- 2551834 - IS, ASDK --> 선택 하는 경우 **태그** 관리자 또는 사용자 포털의 저장소 계정에 대 한 정보를 로드 실패 하 고 표시 되지 않습니다.  

  사용할 수 없는 정보를 보려면 사용 하 여는 [Get AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell cmdlet.

- <!-- TBD - IS ASDK --> 새 관리 구독 유형으로 사용 하지 마십시오 *구독 계량*, 및 *소비 구독*합니다. 이러한 새 구독 유형이 1804 버전에 도입 된 되지만 아직 사용할 준비가 되지 않습니다. 계속 사용 해야는 *기본 공급자* 구독 유형입니다.  

- <!-- 2403291 - IS ASDK --> 관리자 및 사용자 포털의 아래쪽에 가로 스크롤 막대를 사용 하 여가 없을 수도 있습니다. 포털의 상단에 있는 이동 경로 목록에서 표시할 블레이드 이름을 선택 하 여 포털에서 이전 블레이드로 이동 하려면 이동 경로 사용 하 여 남아 있는 가로 스크롤 막대에 액세스할 수 없는 경우.
  ![이동 경로 탐색](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- <!-- TBD -  IS ASDK --> Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.


#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  에 대 한 경고가 표시 될 수 있습니다는 *상태 컨트롤러* 다음 정보를 포함 하는 구성 요소:  

   #1 경고:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  경고 # 2:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다 하 고 시간이 지남에 따라 자동으로 닫힙니다.  

- <!-- 2392907 – ASDK -->   표시 될 수 있습니다는 *중요* 에 대 한 경고 **메모리 용량 부족**합니다. 이 경고는 다음 설명과: *지역 95.00% 이상의 사용 가능한 메모리를 사용 했습니다. 많은 양의 메모리를 가상 컴퓨터를 만들기가 실패할 수 있습니다.*

  Azure 스택 올바르게 계정 하지 메모리 사용에 대 한 Azure 스택 개발 키트에서 하는 경우이 경고를 생성할 수 있습니다.  

  이 경고는 무시할 수 있으며 문제 가상 컴퓨터의 배치에 영향을 주지 않습니다. 

- <!-- 2368581 - IS. ASDK --> 메모리 부족 경고를 표시 하 고 테 넌 트 가상 컴퓨터와 함께 배포할 실패 하는 경우에 Azure 스택 연산자는 *패브릭 VM 만들기 오류*, Azure 스택 스탬프 사용 가능한 메모리가 부족 수도 있습니다. 사용 하 여는 [Azure 스택 용량 Planne](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 이해 하기 위해서는 작업에 대 한 사용 가능한 용량입니다. 


#### <a name="compute"></a>컴퓨팅
- <!-- TBD - IS, ASDK --> 일부 F 시리즈 VM 크기는 가상 컴퓨터 배포에 대 한 가상 컴퓨터 크기를 선택할 때는 표시 되지 않습니다는 VM을 만들 때 크기 선택기의 일환으로 합니다. 다음 VM 크기 선택기에 표시 되지 않습니다: *F8s_v2*, *F16s_v2*, *F32s_v2*, 및 *F64s_v2*합니다.  
  문제를 해결 VM을 배포 하는 다음 방법 중 하나를 사용 합니다. 각 방법에 사용할 VM 크기를 지정 해야 합니다.

  - **Azure 리소스 관리자 템플릿:** 서식 파일을 사용 하면 설정는 *vmSize* 템플릿의 사용 하려는 VM 크기와 같아야 합니다. 사용 하는 VM을 배포 하려면 다음 항목이 사용 되는 예를 들어는 *F32s_v2* 크기:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** 사용할 수 있습니다는 [az vm 만들기](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 명령 및 VM 크기 비슷합니다 매개 변수로 지정 `--size "Standard_F32s_v2"`합니다.

  - **PowerShell:** Powershell을 사용할 수 있습니다 [새로 AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) 비슷합니다 v M 크기를 지정 하는 매개 변수와 함께 `-VMSize "Standard_F32s_v2"`합니다.


- <!-- TBD -  IS ASDK --> 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- <!-- TBD -  IS ASDK --> Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 D 시리즈 VM을 연결할 수 있는 데이터 디스크 수를 표시 합니다. 모든 지원 되는 D 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- <!-- TBD -  IS ASDK --> VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

- <!-- TBD -  IS ASDK --> VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 - IS ASDK --> Linux VM 진단 Azure 스택에서 지원 되지 않습니다. VM 진단을 사용 하는 Linux VM을 배포 하는 경우 배포에 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정 하는 경우에 배포 실패 합니다. 

#### <a name="networking"></a>네트워킹
- <!-- TBD - IS ASDK --> 관리자 또는 사용자 포털 중 하나에서 사용자 정의 경로 만들 수입니다. 사용 하 여 문제를 해결 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell)합니다.

- <!-- 1766332 - IS, ASDK --> 아래 **네트워킹**누르면, **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 만 **경로 기반** 옵션은 Azure 스택에서 지원 합니다.

- <!-- 2388980 -  IS ASDK --> VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.


- <!-- 2292271 - IS ASDK --> 제안 및 연결 된 테 넌 트 구독 계획의 일부인 네트워크 리소스에 대 한 할당량 한도 늘릴 경우 해당 구독에 새 제한이 적용 되지 않습니다. 그러나 새 한도 할당량을 늘릴 후 생성 된 새 구독에 적용지 않습니다. 

  이 문제를 해결 하려면 계획 이미 구독과 연결 된 경우 네트워크 할당량을 늘려야 하는 추가 기능 계획을 사용 합니다. 자세한 내용은 참조 하는 방법 [플랜을 통해 추가 기능을 사용할 수 있도록](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)합니다.

- <!-- 2304134 IS ASDK --> DNS 영역 리소스 또는 연결 된 경로 테이블 리소스가 있는 구독을 삭제할 수 없습니다. 구독을 삭제 하려면 먼저 테 넌 트 구독에서 DNS 영역 및 경로 테이블 리소스를 삭제 해야 합니다. 


- <!-- 1902460 -  IS ASDK --> 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- <!-- 16309153 -  IS ASDK --> DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.
 
- <!-- TBD -  IS ASDK --> Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.


#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- <!-- TBD - ASDK --> 리소스 공급자 및 사용자 작업에 사용 하기 위해 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. 응용 프로그램 서비스를 포함 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

- <!-- IS, ASDK --> 특수 문자, 공백 및 마침표를 포함 하 여에서 지원 되지 않습니다는 **제품군** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름을 지정 합니다. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> 사용자가 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- <!-- TBD - IS ASDK --> (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.  

- <!-- TBD - IS ASDK --> 앱 서비스에만 배포할 수는 *공급자 기본 구독* 이 이번에 있습니다. 새 배포 응용 프로그램 서비스는 향후 업데이트에서 *구독 계량* Azure 스택 1804에 도입 된입니다. 계량 사용 하기 위해 지원 되는 경우이 새 구독 형식에 기존의 모든 배포를 마이그레이션합니다.

#### <a name="usage"></a>사용 현황  
- <!-- TBD -  IS ASDK --> 사용 현황 공용 IP 주소 사용 계량 데이터를 보여 줍니다 동일한 *EventDateTime* 대신 각 레코드에 대 한 값은 *TimeDate* 레코드를 만들 때 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용 정확한 계정 작업을 수행 하려면이 데이터를 사용할 수 없습니다.

<!-- #### Identity -->



## <a name="build-201805131"></a>빌드 20180513.1

### <a name="new-features"></a>새로운 기능 
이 빌드 Azure 스택에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.  

- <!-- 1759172 - IS, ASDK --> **새로운 관리 구독**합니다. 1804로 두 가지가 새 구독 포털에서 사용할 수 있습니다. 이러한 새 구독 유형은 공급자 기본 구독 뿐 아니라 및 표시 1804 버전부터 새 Azure 스택 설치입니다. *이 버전의 Azure 스택 이러한 새 구독 유형을 사용 하지 마십시오*합니다. 향후 업데이트를 사용 하 여 이러한 구독 유형을 사용 하도록 가용성을 알려 드리겠습니다. 

  이러한 새 구독 유형은 볼 수 있지만 기본 공급자 구독을 보호 하 고 쉽게 SQL 호스팅 서버와 마찬가지로 공유 리소스를 배포 하는 큰 변경의 일부입니다. 

  현재 사용할 수 있는 세 개의 구독 유형은 다음과 같습니다.  
  - 기본 공급자 구독:이 구독 유형을 사용 하 여 계속 합니다. 
  - 구독을 계량: *이 구독의 유형은 사용 하지 마십시오.*
  - 소비 구독: *이 구독의 유형은 사용 하지 마십시오*

### <a name="fixed-issues"></a>해결된 문제
- <!-- IS, ASDK -->  관리 포털에서 더 이상 정보를 표시 하기 전에 업데이트 타일 새로 고침 해야 합니다. 

- <!-- 2050709 - IS, ASDK -->  이제 Blob 서비스, 테이블 서비스 및 큐 서비스에 대 한 저장소 메트릭을 편집 하는 관리 포털을 사용할 수 있습니다.

- <!-- IS, ASDK --> 아래 **네트워킹**클릭 **연결** VPN 연결을 설정 하려면 **사이트 (IPsec)** 경우만 사용할 수 있는 옵션이 있습니다. 

- **다양 한 수정 프로그램이** 성능, 안정성, 보안 및 Azure 스택에서 사용 되는 운영 체제에 대 한

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>추가 릴리스가이 업데이트 시간이 초과 되었습니다.  
다음 사용할 수 있지만 Azure 스택 업데이트 1804 필요 하지 않습니다.
- **Microsoft Azure 스택 System Center Operations Manager 모니터링 팩으로 업데이트**합니다. 새 버전 (1.0.3.0)는 Microsoft System Center Operations Manager 모니터링 팩에 대 한 Azure 스택의를 사용할 수 있는 [다운로드](https://www.microsoft.com/download/details.aspx?id=55184)합니다. 이 버전에서는 연결 된 Azure 스택 배포를 추가 하면 서비스 사용자를 사용할 수 있습니다. 또한이 버전의 Operations Manager 내에서 직접 수정 조치를 취할 수 있는 업데이트 관리 경험을 기능 합니다. 리소스 공급자를 표시, 단위, 확장 및 단위 노드를 확장 하는 새 대시보드도 있습니다.

- **새로운 Azure 스택 관리자 PowerShell 버전 1.3.0**합니다.  Azure 스택 PowerShell 1.3.0 설치용으로 제공 되었습니다. 이 버전에는 Azure 스택을 관리 하는 모든 관리자 리소스 공급자에 대 한 명령을 제공 합니다.  이 릴리스에서 일부 콘텐츠를 Azure 스택 도구 GitHub에서 중단 예정 [리포지토리](https://github.com/Azure/AzureStack-Tools)합니다. 

   설치 세부 정보에 따라는 [지침](.\.\azure-stack-powershell-install.md) 또는 [도움말](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) 1.3.0 Azure 스택 모듈에 대 한 콘텐츠입니다. 

- **초기 버전의 Azure 스택 API Rest 참조**합니다. [모든 Azure 스택 관리자 리소스 공급자에 대 한 API 참조](https://docs.microsoft.com/rest/api/azure-stack/) 이제 게시 되었습니다. 

### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- <!-- TBD - IS ASDK --> 기능 [새 지원 요청을 드롭다운 목록에서 열려는](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털 사용할 수 없습니다. 대신, 다음 링크를 사용 합니다.     
    - Azure 스택 개발 키트를 사용 하 여 https://aka.ms/azurestackforum합니다.    

- <!-- 2403291 - IS ASDK --> 관리자 및 사용자 포털의 아래쪽에 가로 스크롤 막대를 사용 하 여가 없을 수도 있습니다. 포털의 상단에 있는 이동 경로 목록에서 표시할 블레이드 이름을 선택 하 여 포털에서 이전 블레이드로 이동 하려면 이동 경로 사용 하 여 남아 있는 가로 스크롤 막대에 액세스할 수 없는 경우.
  ![이동 경로 탐색](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- <!-- TBD -  IS ASDK --> Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

-   <!-- TBD -  IS ASDK --> 관리 포털에서 Microsoft.Update.Admin 구성 요소에 대 한 중요 한 알림이 표시 될 수 있습니다. 경고 이름, 설명 및으로 표시 하는 모든 업데이트 관리:  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

    이 경고는 무시 해도 됩니다. 

#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  에 대 한 경고가 표시 될 수 있습니다는 *상태 컨트롤러* 다음 정보를 포함 하는 구성 요소:  

   #1 경고:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  경고 # 2:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다. 시간에 따라 자동으로 종료 됩니다.  

#### <a name="marketplace"></a>Marketplace
- 사용자 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.
 
#### <a name="compute"></a>컴퓨팅
- <!-- TBD -  IS ASDK --> 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- <!-- TBD -  IS ASDK --> Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- <!-- TBD -  IS ASDK --> VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

- <!-- TBD -  IS ASDK --> VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 - IS ASDK --> Linux VM 진단 Azure 스택에서 지원 되지 않습니다. VM 진단을 사용 하는 Linux VM을 배포 하는 경우 배포에 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정 하는 경우에 배포 실패 합니다. 

#### <a name="networking"></a>네트워킹
- <!-- 1766332 - IS, ASDK --> 아래 **네트워킹**누르면, **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 만 **경로 기반** 옵션은 Azure 스택에서 지원 합니다.

- <!-- 2388980 -  IS ASDK --> VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.

- <!-- 2292271 - IS ASDK --> 제안 및 연결 된 테 넌 트 구독 계획의 일부인 네트워크 리소스에 대 한 할당량 한도 늘릴 경우 해당 구독에 새 제한이 적용 되지 않습니다. 그러나 새 한도 할당량을 늘릴 후 생성 된 새 구독에 적용지 않습니다. 

  이 문제를 해결 하려면 계획 이미 구독과 연결 된 경우 네트워크 할당량을 늘려야 하는 추가 기능 계획을 사용 합니다. 자세한 내용은 참조 하는 방법 [플랜을 통해 추가 기능을 사용할 수 있도록](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)합니다.

- <!-- 2304134 IS ASDK --> DNS 영역 리소스 또는 연결 된 경로 테이블 리소스가 있는 구독을 삭제할 수 없습니다. 구독을 삭제 하려면 먼저 테 넌 트 구독에서 DNS 영역 및 경로 테이블 리소스를 삭제 해야 합니다. 


- <!-- 1902460 -  IS ASDK --> 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- <!-- 16309153 -  IS ASDK --> DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.
 
- <!-- TBD -  IS ASDK --> Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.


#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- <!-- TBD - ASDK --> 리소스 공급자 및 사용자 작업에 사용 하기 위해 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. 응용 프로그램 서비스를 포함 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

- <!-- IS, ASDK --> 특수 문자, 공백 및 마침표를 포함 하 여에서 지원 되지 않습니다는 **제품군** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름을 지정 합니다. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> 사용자가 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- <!-- TBD -  IS ASDK --> (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.
 
#### <a name="usage"></a>사용 현황  
- <!-- TBD -  IS ASDK --> 사용 현황 공용 IP 주소 사용 계량 데이터를 보여 줍니다 동일한 *EventDateTime* 대신 각 레코드에 대 한 값은 *TimeDate* 레코드를 만들 때 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용 정확한 계정 작업을 수행 하려면이 데이터를 사용할 수 없습니다.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure 스택 도구를 다운로드합니다.
- 사용 하는 경우는 *호출 webrequest* Github에서 Azure 스택을 다운로드 하려면 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원의 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)으로 인해 발생합니다. 자세한 내용은 참조 [약한 암호화 표준 제거 통지](https://githubengineering.com/crypto-removal-notice/)합니다.

<!-- #### Identity -->



## <a name="build-201803021"></a>빌드 20180302.1

### <a name="new-features-and-fixes"></a>새 기능 및 수정
새로운 기능 및 수정 사항에 대 한 통합 된 시스템 버전 1803 Azure 스택 개발 키트에 적용 하는 Azure 스택 해제 합니다. 참조는 [새로운 기능](.\.\azure-stack-update-1803.md#new-features) 및 [해결 된 문제](.\.\azure-stack-update-1803.md#fixed-issues) Azure 스택 1803의 섹션 자세한 내용은 릴리스 정보를 업데이트 합니다.  
> [!IMPORTANT]    
> 에 나열 된 항목 중 일부는 **새로운 기능** 및 **해결 된 문제** 섹션에서는 Azure 스택 통합 시스템에만 적용 됩니다.

### <a name="changes"></a>변경 내용
- 새로 만든된 제품의 상태를 변경 하는 방법은 *개인* 를 *공용* 또는 *폐기 된* 변경 되었습니다. 자세한 내용은 참조 [제안을 만들](.\.\azure-stack-create-offer.md)합니다. 


### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- 기능 [새 지원 요청을 드롭다운 목록에서 열려는](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털 사용할 수 없습니다. 대신, 다음 링크를 사용 합니다.     
    - Azure 스택 개발 키트를 사용 하 여 https://aka.ms/azurestackforum합니다.    

- <!-- 2050709 --> 관리 포털에서 Blob 서비스, 테이블 서비스 또는 큐 서비스에 대 한 저장소 메트릭을 편집할 수 없으면입니다. 저장소를 이동 해야 하 고 다음 blob, 테이블 또는 큐 서비스 타일을 선택 하는 경우 해당 서비스에 대 한 메트릭 차트를 표시 하는 새 블레이드가 열립니다. 메트릭 차트 타일의 맨 위에서 다음 편집을 선택 하는 경우 차트 편집 블레이드 열리지만 메트릭을 편집 하는 옵션이 표시 되지 않습니다.  

- 표시는 **활성화 필요** Azure 스택 개발 키트를 등록 해야 한다는 경고 성 알림입니다. 이 동작은 사용할 수 있습니다.

- 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- 관리 포털의 대시보드에서 업데이트 타일 업데이트에 대 한 정보를 표시 하는 실패 합니다. 이 문제를 해결 하려면 타일 새로 고침을 클릭 합니다.

-   관리 포털에서 Microsoft.Update.Admin 구성 요소에 대 한 중요 한 알림이 표시 될 수 있습니다. 경고 이름, 설명 및으로 표시 하는 모든 업데이트 관리:  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

    이 경고는 무시 해도 됩니다. 

- 관리 포털 및 사용자 포털 모두에서 개요 블레이드에 이전 API 버전을 사용 하 여 만든 저장소 계정에 대 한 개요 블레이드를 선택 하면 로드에 실패 (예: 2015-06-15). 

  이 문제를 해결 PowerShell 실행을 사용 하는 **시작 ResourceSynchronization.ps1** 저장소 계정 세부 정보에 대 한 액세스를 복원 하는 스크립트입니다. [이 스크립트는 GitHub에서 사용할 수 있는]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)는 ASDK를 사용 하는 경우 개발 키트 호스트에서 서비스 관리자 자격 증명으로 실행 해야 합니다.  

- **서비스 상태** 블레이드 로드 되지 않습니다. 관리자 또는 사용자 포털에서 Azure 스택 서비스 상태 블레이드를 열 때 오류가 표시 되 고 정보를 로드 하지 않습니다. 이는 정상적인 동작입니다. 선택 하 고 서비스 상태를 열 수 있지만이 기능은 아직 제공 되지 않습니다 되지만 Azure 스택의 이후 버전에서 구현 됩니다.


#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  에 대 한 경고가 표시 될 수 있습니다는 *상태 컨트롤러* 다음 정보를 포함 하는 구성 요소:  

   #1 경고:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  경고 # 2:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다. 시간에 따라 자동으로 종료 됩니다.  

- Azure 스택 관리자 포털에서 이름으로 중요 한 알림이 표시 될 수 있습니다 **외부 인증서 만료 보류 중인**합니다.  이 경고는 무시 해도 됩니다 및 Azure 스택 개발 키트의 작업에 영향을 줍니다. 


#### <a name="marketplace"></a>Marketplace
- 사용자 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.
 
#### <a name="compute"></a>컴퓨팅
- 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

-  VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 --> Linux VM 진단 Azure 스택에서 지원 되지 않습니다. VM 진단을 사용 하는 Linux VM을 배포 하는 경우 배포에 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정 하는 경우에 배포 실패 합니다. 


#### <a name="networking"></a>네트워킹
- 아래 **네트워킹**누르면, **연결** VPN 연결을 설정 하려면 **VNet 대 VNet** 가능한 연결 유형으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 현재만 **사이트 (IPsec)** 옵션은 지원 됩니다.

- VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.



- 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.
 
- Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.



#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- 사용자가 새 SQL 또는 MySQL SKU에 데이터베이스를 만들 수 최대 1 시간이 걸릴 수 있습니다.

- 리소스 공급자 및 사용자 작업에 사용 하기 위해 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. 응용 프로그램 서비스를 포함 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

- <!-- IS, ASDK --> 특수 문자, 공백 및 마침표를 포함 하 여에서 지원 되지 않습니다는 **제품군** 또는 **계층** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름을 지정 합니다.

#### <a name="app-service"></a>App Service
- 사용자가 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.
 
#### <a name="usage"></a>사용 현황  
- 사용 현황 공용 IP 주소 사용 계량 데이터를 보여 줍니다 동일한 *EventDateTime* 대신 각 레코드에 대 한 값은 *TimeDate* 레코드를 만들 때 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용 정확한 계정 작업을 수행 하려면이 데이터를 사용할 수 없습니다.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure 스택 도구를 다운로드합니다.
- 사용 하는 경우는 *호출 webrequest* Github에서 Azure 스택을 다운로드 하려면 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원의 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)으로 인해 발생합니다. 자세한 내용은 참조 [약한 암호화 표준 제거 통지](https://githubengineering.com/crypto-removal-notice/)합니다.

  이 문제를 해결 하려면 추가 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` TLSv1.2 GitHub 리포지토리에에서 다운로드할 때 사용할 PowerShell 콘솔을 강제 적용 하는 스크립트의 맨 위로 이동 합니다.





