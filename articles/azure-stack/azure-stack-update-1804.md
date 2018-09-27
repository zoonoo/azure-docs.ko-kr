---
title: Azure Stack 1804 업데이트 | Microsoft Docs
description: Azure Stack 용 1804 업데이트의 새로운 기능 알아보기 시스템의 알려진된 문제 및 업데이트를 다운로드 하는 위치를 통합 합니다.
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
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 16f12d8119a14e668a7502d99fa2d9c976d23833
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393522"
---
# <a name="azure-stack-1804-update"></a>Azure Stack 1804 업데이트

*적용 대상: Azure Stack 통합 시스템*

이 문서는 향상 된 기능에 설명 하 고이 릴리스 및 업데이트를 다운로드 하는 위치에 대 한 문제를 알려진 1804 업데이트 패키지의 수정. 알려진된 문제는 업데이트 프로세스에 직접 관련 된 문제 및 문제 (설치 후) 빌드를 사용 하 여으로 구분 됩니다.

> [!IMPORTANT]        
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조    
Azure Stack 1804 업데이트 빌드 번호는 **20180513.1**합니다.   

### <a name="new-features"></a>새로운 기능
이 업데이트는 Azure Stack에 대 한 다음과 같은 개선 사항이 포함 되어 있습니다.

- <!-- 15028744 - IS -->  **AD FS를 사용 하 여 연결이 끊긴된 Azure Stack 배포에 대 한 visual Studio 지원**합니다. 이제 구독 추가 인증 하는 Visual Studio 내에서 AD FS를 사용 하 여 사용자 자격 증명을 페더레이션 합니다. 
 
- <!-- 1779474, 1779458 - IS --> **Av2 및 F 시리즈 virtual machines를 사용 하 여**입니다. Azure Stack의 Av2 시리즈 및 F 시리즈 가상 머신 크기에 따라 가상 컴퓨터를 이제 사용할 수 있습니다. 자세한 내용은 참조 [Azure Stack에서 지원 되는 가상 머신 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)합니다. 

- <!-- 1759172 - IS, ASDK --> **새 관리 구독**합니다. 1804를 사용 하 여 두 가지가 새 구독 포털에서 사용할 수 있습니다. 이러한 새 구독 유형과 되며 기본 공급자 구독 외에도 표시 1804 버전부터 새 Azure Stack 설치 합니다. *이 버전의 Azure Stack을 사용 하 여 이러한 새 구독 유형과 넣지 마십시오*합니다. 향후 업데이트를 사용 하 여 이러한 구독 형식을 사용 하 여 가용성을 발표할 예정입니다. 

  Azure Stack 1804 버전으로 업데이트 하는 경우에 두 개의 새 구독 유형과 표시 되지 않습니다. 그러나 새 배포의 Azure Stack 통합 시스템 및 Azure Stack 개발 키트 버전 1804 이상이 설치 된 모든 세 가지 구독 형식에 액세스할 수 합니다.  

  이러한 새 구독 유형은 기본 공급자 구독을 보호 하 고 쉽게 SQL 호스팅 서버와 같은 공유 리소스를 배포 하는 큰 변경의 일환입니다. Azure Stack에 향후 업데이트를 사용 하 여이 크게 변경의 자세한 부분을 추가 했습니다 이러한 새 구독 유형과 배포 된 리소스가 손실 될 수 있습니다. 

  이제 표시 된 세 가지 구독 형식은 다음과 같습니다.  
  - 공급자 구독을 기본:이 구독 유형을 사용 하 여 계속 합니다. 
  - 구독을 계량: *이 구독 유형은 사용 하지 않습니다.*
  - 소비 구독: *이 구독 형식을 사용 하지 마세요*

  



## <a name="fixed-issues"></a>해결된 문제

- <!-- IS, ASDK -->  관리 포털에서 더 이상 정보를 표시 하기 전에 업데이트 타일 새로 고침 해야 합니다.
 
- <!-- 2050709  -->  이제 Blob service, Table service 및 큐 서비스에 대 한 저장소 메트릭을 편집 하려면 관리 포털을 사용할 수 있습니다.
 
- <!-- IS, ASDK --> 아래 **네트워킹**클릭 **연결** VPN 연결을 설정 하려면 **사이트-사이트 간 (IPsec)** 경우만 사용할 수 있는 옵션이 있습니다.

- **다양 한 수정** 성능, 안정성, 보안 및 Azure Stack에서 사용 되는 운영 체제에 대 한 합니다.

## <a name="additional-releases-timed-with-this-update"></a>이 업데이트를 사용 하 여 시간이 추가 릴리스  
다음 이제 사용할 수 있지만 Azure Stack 업데이트 1804 필요 하지 않습니다.
- **모니터링 팩은 Microsoft Azure Stack System Center Operations Manager에 업데이트**합니다. Microsoft System Center Operations Manager 모니터링 팩 Azure Stack 용의 새 버전 (1.0.3.0)을 사용할 수 있습니다 [다운로드](https://www.microsoft.com/download/details.aspx?id=55184)합니다. 이 버전을 사용 하 여 연결 된 Azure Stack 배포를 추가 하면 서비스 주체를 사용할 수 있습니다. 이 버전에는 또한 Operations Manager 내에서 직접 수정 작업을 수행 할 수 있는 업데이트 관리 환경을 갖추고 있습니다. 리소스 공급자를 표시, 배율 단위를 및 단위 노드의 크기를 조정 하는 새 대시보드도 있습니다.

- **새 Azure Stack 관리자 PowerShell 버전 1.3.0**합니다.  Azure Stack PowerShell 1.3.0 설치에 대 한 출시 되었습니다. 이 버전에는 Azure Stack을 관리 하는 모든 관리자 리소스 공급자에 대 한 명령을 제공 합니다.  이 릴리스를 사용 하 여 일부 콘텐츠가 Azure Stack 도구 GitHub에서 중단 예정 [리포지토리](https://github.com/Azure/AzureStack-Tools)합니다. 

   설치 세부 정보를 수행 합니다 [지침](azure-stack-powershell-install.md) 또는 [도움말](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) 1.3.0 Azure Stack 모듈에 대 한 콘텐츠. 

- **Azure Stack API Rest 참조 릴리스의 초기**합니다. 합니다 [모든 Azure Stack 관리자 리소스 공급자에 대 한 API 참조](https://docs.microsoft.com/rest/api/azure-stack/) 이제 게시 되었습니다. 


## <a name="before-you-begin"></a>시작하기 전에    

### <a name="prerequisites"></a>필수 조건
- Azure Stack을 설치 [1803 업데이트](azure-stack-update-1803.md) Azure Stack 1804 업데이트를 적용 하기 전에 합니다.  
  
- 사용 가능한 최신 설치 [업데이트 또는 핫픽스 버전 1803](azure-stack-update-1803.md#post-update-steps)합니다. 


### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스를 사용 하 여 알려진된 문제   
- 1804 업데이트를 설치 하는 동안 경고 제목으로 표시 될 수 있습니다 *오류 – FaultType UserAccounts.New 템플릿을 누락 되었습니다.*  이러한 경고를 안전 하 게 무시할 수 있습니다. 이러한 경고 1804 업데이트가 완료 된 후 자동으로 닫힙니다.   
 
- <!-- TBD - IS --> 이 업데이트를 설치 하는 동안 가상 컴퓨터를 만들 하려고 하지 마십시오. 업데이트를 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md#plan-for-updates)합니다.


### <a name="post-update-steps"></a>업데이트 후 단계
1804의 설치가 끝나면 모든 적용 가능한 핫픽스를 설치 합니다. 자세한 내용은 다음 기술 자료 문서를 볼 뿐만 아니라 우리 [서비스 정책](azure-stack-servicing-policy.md)합니다.  
 - [KB 4344114-Azure Stack 핫픽스 1.0.180527.15](https://support.microsoft.com/help/4344114)합니다.




### <a name="known-issues-post-installation"></a>알려진된 문제 (사후 설치)
다음은 빌드에 대 한 설치 후 알려진된 문제 **20180513.1**합니다.

#### <a name="portal"></a>포털

- Azure Stack 기술 설명서는 최신 버전에 중점을 둡니다. 릴리스 간의 포털 변경으로 인해 Azure Stack 포털을 사용 하는 경우 표시 되는 내용 설명서에 표시 되는 내용에서 달라질 수 있습니다. 

- <!-- TBD - IS ASDK --> 이 버전의 Azure Stack을 사용 하 여 OEM 확장 패키지를 사용 하 여 드라이버 업데이트를 적용할 수 없습니다.  이 문제에 대 한 해결 방법은 없습니다.

- <!-- 1272111 - IS --> 를 설치 하거나 Azure Stack의이 버전으로 업데이트 한 후 관리 포털에서 Azure Stack 배율 단위를 볼 수 수 없습니다.  
  해결 방법: 배율 단위에 대 한 정보를 보려면 PowerShell을 사용 합니다. 자세한 내용은 참조는 [도움말](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) 1.3.0 Azure Stack 모듈에 대 한 콘텐츠. 

- <!-- 2332636 - IS -->  AD FS를 사용 하 여 Azure Stack의이 버전으로 업데이트 확인 하 고 Azure Stack id 시스템에 대 한 기본 제공 기본 공급자 구독의 기본 소유자 재설정 됩니다 **CloudAdmin** 사용자입니다.  
  해결 방법:이 업데이트를 설치한 후이 문제를 해결 하려면 사용에서 3 단계는 [트리거를 구성 하는 자동화 클레임 공급자 트러스트 Azure Stack에서](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) 기본 공급자 구독 소유자 다시 설정 하는 절차입니다.   

- <!-- TBD - IS ASDK --> 일부 관리 구독 유형은 사용할 수 없습니다.  Azure Stack이이 버전으로 업그레이드할 때 두 가지 구독 유형이 있었던 [도입 된 버전 1804](#new-features) 콘솔에 표시 되지 않습니다. 예상된 동작입니다. 사용할 수 없는 구독 유형은 *구독을 계량*, 및 *소비 구독*합니다. 이러한 구독 유형에 1804 버전부터 새 Azure Stack 환경에서 표시 되지만 아직 사용할 준비가 완료 됩니다. 계속 사용 해야 합니다 *기본 공급자* 구독 유형입니다.  


- <!-- TBD -  IS ASDK -->기능 [드롭다운 목록에서 새 지원 요청을 열려면](azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털을 사용할 수 없습니다. 다음 링크를 대신 사용 합니다.     
    - Azure Stack 통합된 시스템을 사용 하 여 https://aka.ms/newsupportrequest입니다.

- <!-- 2403291 - IS ASDK --> 관리자 및 사용자 포털의 아래쪽에 가로 스크롤 막대를 사용 하 여가 없을 수 있습니다. 가로 스크롤 막대에 액세스할 수 없는 경우 블레이드의 이름을 선택 하 여 포털에서 이전 블레이드로 이동 하려면 이동 경로 상단에 있는 이동 경로 탐색 목록에서 확인 하려는 사용 하 여 포털의 왼쪽입니다.
  ![이동 경로 탐색](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> 관리자 포털에서 계산 또는 저장소 리소스를 보려면 하지 못할 수도 있습니다. 이 문제의 원인은 잘못 보고 성공으로 업데이트 하면 업데이트를 설치 하는 동안 오류입니다. 이 문제가 발생 하는 경우 Microsoft 고객 지원 서비스에 문의 하십시오.

- <!-- TBD - IS --> 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 기어 아이콘을 선택 하 고 선택한 **기본 설정 복원**합니다.

- <!-- TBD - IS ASDK --> 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- <!-- TBD - IS ASDK --> Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- <!-- TBD - IS ASDK --> 관리 포털에 대 한 중요 한 경고가 표시 될 수도 있습니다는 *Microsoft.Update.Admin* 구성 요소입니다. 경고 이름, 설명 및 수정으로 모두 표시 합니다.  
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
 

#### <a name="compute"></a>컴퓨팅
- <!-- TBD - IS --> 일부 F 시리즈 VM 크기는 가상 머신 배포에 대 한 가상 머신 크기를 선택할 때 표시 되지 일부로 VM을 만들 때 크기 선택기입니다. 다음 VM 크기 선택기에 표시 되지 않습니다: *F8s_v2*, *F16s_v2*, *F32s_v2*, 및 *F64s_v2*합니다.  
  해결 방법으로 VM을 배포 하려면 다음 방법 중 하나를 사용 합니다. 각 메서드를 사용 하려면 VM 크기를 지정 해야 합니다.

  - **Azure Resource Manager 템플릿:** 템플릿을 사용 하는 경우 설정 합니다 *vmSize* 템플릿의 원하는 VM 크기와 같아야 합니다. 예를 들어, 다음 되는 VM을 배포 합니다 *F32s_v2* 크기:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** 사용할 수는 [az vm 만들기](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 명령 및 유사한 매개 변수로 VM 크기를 지정할 `--size "Standard_F32s_v2"`합니다.

  - **PowerShell:** Powershell을 사용할 수 있습니다 [New-azurermvmconfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) 비슷합니다 VM 크기를 지정 하는 매개 변수를 사용 하 여 `-VMSize "Standard_F32s_v2"`입니다.


- <!-- TBD - IS ASDK --> 가상 머신 확장 집합에 대 한 크기 조정 설정을 포털에서 사용할 수 없는 경우 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이로 인해 사용 해야 합니다 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- <!-- TBD - IS --> 가용성으로 이동 하 여 포털에서 집합을 만들면 **새로 만들기** > **계산** > **가용성 집합**를 만들 수 있습니다는 장애 도메인 및 업데이트 도메인 1을 사용 하 여 가용성 집합. 해결 방법으로, 새 가상 머신을 만들 때, 가용성 집합 내에서 또는 PowerShell, CLI를 사용 하 여 만들기 포털입니다.

- <!-- TBD - IS ASDK --> Azure Stack 사용자 포털에서 가상 컴퓨터를 만들 때 포털 잘못 된 D 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. 모든 지원 되는 D 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- <!-- TBD - IS ASDK --> VM 이미지를 만들려는 못하면 VM 이미지 계산 블레이드로 삭제할 수 없습니다는 실패 한 항목을 추가할 수 있습니다.

  해결 방법으로 Hyper-v를 통해 만들 수 있는 더미 VHD를 사용 하 여 새 VM 이미지 만들기 (새 VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하는 것을 금지 하는 문제를 수정 해야 합니다. 그런 다음 더미 이미지를 만든 후 15 분 삭제할 수 있습니다 했습니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드를 시도할 수 있습니다.

- <!-- TBD - IS ASDK --> VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

- <!-- 1662991 IS ASDK --> Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다.  


#### <a name="networking"></a>네트워킹
- <!-- 1766332 - IS ASDK --> 아래 **네트워킹**를 클릭 하면 **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 만 **경로 기반** 옵션은 Azure Stack에서 지원 됩니다.

- <!-- 2388980 - IS ASDK --> VM을 만들고 공용 IP 주소와 연결 된 후 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 연결 해제 하려면 나타나지만 이전에 할당된 된 공용 IP 주소 유지 원래 VM과 연결 됩니다.

  현재, 만든 새 Vm에 대 한 새 공용 IP 주소만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고 한 *VIP 교체*). 앞으로의 모든 새 아닌 원래 연결 된 VM에 연결에서이 IP 주소 결과 통해 연결 하려고 시도 합니다.

- <!-- 2292271 - IS ASDK --> 제품 및 테 넌 트 구독을 사용 하 여 연결 된 계획의 일부인 네트워크 리소스에 대 한 할당량 한도 늘릴 경우 해당 구독에 새 제한이 적용 되지 않습니다. 그러나 할당량 증가 이후에 만든 새 구독에 새 제한이 적용지 않습니다. 

  이 문제를 해결 하려면 계획을 이미 구독과 연결 된 경우 네트워크 할당량을 늘리려면는 추가 요금제를 사용 합니다. 자세한 내용은 참조 하는 방법 [는 추가 요금제를 사용할 수 있도록](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)입니다.

- <!-- 2304134 IS ASDK --> DNS 영역 리소스 또는 연결 된 경로 테이블 리소스는 구독을 삭제할 수 없습니다. 구독을 성공적으로 삭제 하려면 먼저 테 넌 트 구독에서 DNS 영역 및 경로 테이블 리소스를 삭제 해야 합니다. 
  

- <!-- 1902460 - IS ASDK --> Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

- <!-- 16309153 - IS ASDK --> DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.

- <!-- TBD - IS ASDK --> Azure Stack VM을 배포한 후 추가 네트워크 인터페이스 VM 인스턴스를 추가 하는 것을 지원 하지 않습니다. VM이 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.

- <!-- 2096388 IS --> 네트워크 보안 그룹 규칙을 업데이트 하는 관리 포털을 사용할 수 없습니다. 

    App Service에 대 한 해결 방법: PowerShell 사용 하 여 네트워크 보안 그룹 내에서 보안 규칙을 수정할 컨트롤러 인스턴스에 원격 데스크톱 연결 하는 경우.  다음은 하는 방법의 예 *허용*, 다음 구성을 사용 하 여 복원 *거부*:  
    
    - *허용:*
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *거부:*

        ```powershell
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```

#### <a name="sql-and-mysql"></a>SQL 및 MySQL

- <!-- TBD - IS --> 항목을 만드는 서버에서 해당 호스트 SQL 또는 MySQL 리소스 공급자에만 사용할 수 있습니다. 리소스 공급자에 의해 생성 되지 않은 호스트 서버에서 생성 하는 항목 일치 하지 않는 상태가 될 수 있습니다.  

- <!-- IS, ASDK --> 공간 및 마침표를 포함 하 여 특수 문자를 사용할 수 없습니다는 **제품군** 또는 **계층** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름을 지정 합니다.


> [!NOTE]  
> <!-- TBD - IS --> Azure Stack 1804를 업데이트 한 후에 이전에 배포한 SQL 및 MySQL 리소스 공급자를 사용 하도록 계속 수 있습니다.  새 릴리스를 사용할 수 있을 때 SQL 및 MySQL를 업데이트 하는 것이 좋습니다. 예: Azure Stack에 SQL 및 MySQL 리소스 공급자 업데이트를 순차적으로 적용 됩니다.  예를 들어, 1802 버전을 사용 하는 경우 먼저 버전 1803에서를 적용 하 고 1804로 업데이트 합니다.      
>   
> 1804 업데이트의 설치를 현재 사용자가 SQL 또는 MySQL 리소스 공급자 사용에 영향을 주지 않습니다.
> 사용할 리소스 공급자의 버전에 관계 없이 해당 데이터베이스의 사용자 데이터는 그대로 유지, 및 계속 액세스할 수 있습니다.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- <!-- TBD - IS ASDK --> (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.

- <!-- TBD - IS ASDK --> App Service에 배포할 수 있습니다 합니다 **기본 공급자 구독** 지금은 합니다.  향후 업데이트에서 App Service는 Azure Stack 1804에 도입 된 새 계량 구독에 배포 하 고 기존의 모든 배포로 곧 마이그레이션될이 새 구독도 합니다.

#### <a name="usage"></a>사용 현황  
- <!-- TBD - IS ASDK --> 사용량 공용 IP 주소 사용 측정기 데이터를 보여 줍니다 동일 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>업데이트 다운로드
Azure Stack 1804 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.


## <a name="see-also"></a>참고 항목
- 권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.
- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.
- Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.
