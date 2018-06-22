---
title: Azure 스택 1804 업데이트 | Microsoft Docs
description: Azure 스택 1804 업데이트에 포함 된 내용에 대 한 자세한 내용은 시스템, 알려진된 문제 및 업데이트를 다운로드 위치에 통합 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 2c2813a7f2d909a23c8f5d4f5ac0280b3f932ba6
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "34700127"
---
# <a name="azure-stack-1804-update"></a>Azure 스택 1804 업데이트

*적용 대상: Azure 스택 시스템 통합*

이 문서는 향상 된 기능에 설명 하 고 알려진 문제가이 릴리스의 경우 및 업데이트를 다운로드 하는 위치에 대 한 1804 업데이트 패키지를 수정 합니다. 알려진된 문제는 직접 업데이트 프로세스와 관련 된 문제 및 문제 빌드와 (설치 후)으로 구분 됩니다.

> [!IMPORTANT]        
> 이 업데이트 패키지는 Azure 스택 통합 시스템에만. Azure 스택 개발 키트를이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조    
Azure 스택 1804 업데이트 빌드 번호는 **20180513.1**합니다.   

### <a name="new-features"></a>새로운 기능
이 업데이트에는 Azure 스택에 대 한 다음과 같은 개선 사항이 포함 됩니다.

- <!-- 15028744 - IS -->  **AD FS를 사용 하 여 연결이 끊긴된 Azure 스택 배포에 대 한 visual Studio 지원**합니다. 이제 수 구독을 추가 하 고 인증 하는 Visual Studio 내에서 AD FS를 사용 하 여 사용자 자격 증명을 페더레이션 합니다. 
 
- <!-- 1779474, 1779458 - IS --> **Av2 및 F 시리즈 가상 컴퓨터를 사용 하 여**합니다. Azure 스택 이제 Av2 계열과 F 시리즈 가상 컴퓨터 크기에 따라 가상 컴퓨터를 사용할 수 있습니다. 자세한 내용은 참조 [Azure 스택에서 지원 되는 가상 컴퓨터 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)합니다. 

- <!-- 1759172 - IS, ASDK --> **새로운 관리 구독**합니다. 1804로 두 가지가 새 구독 포털에서 사용할 수 있습니다. 이러한 새 구독 유형은 공급자 기본 구독 뿐 아니라 및 표시 1804 버전부터 새 Azure 스택 설치입니다. *이 버전의 Azure 스택 이러한 새 구독 유형을 사용 하지 마십시오*합니다. 향후 업데이트를 사용 하 여 이러한 구독 유형을 사용 하도록 가용성을 알려 드리겠습니다. 

  Azure 스택 1804 버전을 업데이트 하면 새 구독 이라는 표시 되지 않습니다. 그러나 Azure 스택의 새 배포 시스템을 통합 있고 Azure 스택 개발 키트 버전 1804 이상이 설치 된 모든 세 가지 구독 유형에 대 한 액세스.  

  이러한 새 구독 유형은 기본 공급자 구독을 보호 하 고 쉽게 SQL 호스팅 서버와 같은 공유 리소스를 배포 하는 큰 변경의 일부입니다. Azure 스택에이 더 큰 변경 향후 업데이트의 더 많은 부분을 추가 했습니다 이러한 새 구독 유형 아래에서 배포 된 리소스가 손실 될 수 있습니다. 

  이제 표시 세 구독 유형은 다음과 같습니다.  
  - 기본 공급자 구독:이 구독 유형을 사용 하 여 계속 합니다. 
  - 구독을 계량: *이 구독의 유형은 사용 하지 마십시오.*
  - 소비 구독: *이 구독의 유형은 사용 하지 마십시오*

  



## <a name="fixed-issues"></a>해결된 문제

- <!-- IS, ASDK -->  관리 포털에서 더 이상 정보를 표시 하기 전에 업데이트 타일 새로 고침 해야 합니다.
 
- <!-- 2050709  -->  이제 Blob 서비스, 테이블 서비스 및 큐 서비스에 대 한 저장소 메트릭을 편집 하는 관리 포털을 사용할 수 있습니다.
 
- <!-- IS, ASDK --> 아래 **네트워킹**클릭 **연결** VPN 연결을 설정 하려면 **사이트 (IPsec)** 경우만 사용할 수 있는 옵션이 있습니다.

- **다양 한 수정 프로그램이** 성능, 안정성, 보안 및 Azure 스택에서 사용 되는 운영 체제에 대 한 합니다.

## <a name="additional-releases-timed-with-this-update"></a>추가 릴리스가이 업데이트 시간이 초과 되었습니다.  
다음 사용할 수 있지만 Azure 스택 업데이트 1804 필요 하지 않습니다.
- **Microsoft Azure 스택 System Center Operations Manager 모니터링 팩으로 업데이트**합니다. 새 버전 (1.0.3.0)는 Microsoft System Center Operations Manager 모니터링 팩에 대 한 Azure 스택의를 사용할 수 있는 [다운로드](https://www.microsoft.com/download/details.aspx?id=55184)합니다. 이 버전에서는 연결 된 Azure 스택 배포를 추가 하면 서비스 사용자를 사용할 수 있습니다. 또한이 버전의 Operations Manager 내에서 직접 수정 조치를 취할 수 있는 업데이트 관리 경험을 기능 합니다. 리소스 공급자를 표시, 단위, 확장 및 단위 노드를 확장 하는 새 대시보드도 있습니다.

- **새로운 Azure 스택 관리자 PowerShell 버전 1.3.0**합니다.  Azure 스택 PowerShell 1.3.0 설치용으로 제공 되었습니다. 이 버전에는 Azure 스택을 관리 하는 모든 관리자 리소스 공급자에 대 한 명령을 제공 합니다.  이 릴리스에서 일부 콘텐츠를 Azure 스택 도구 GitHub에서 중단 예정 [리포지토리](https://github.com/Azure/AzureStack-Tools)합니다. 

   설치 세부 정보에 따라는 [지침](azure-stack-powershell-install.md) 또는 [도움말](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) 1.3.0 Azure 스택 모듈에 대 한 콘텐츠입니다. 

- **초기 버전의 Azure 스택 API Rest 참조**합니다. [모든 Azure 스택 관리자 리소스 공급자에 대 한 API 참조](https://docs.microsoft.com/rest/api/azure-stack/) 이제 게시 되었습니다. 


## <a name="before-you-begin"></a>시작하기 전에    

### <a name="prerequisites"></a>필수 조건
- Azure 스택 설치 [1803 업데이트](azure-stack-update-1803.md) Azure 스택 1804 업데이트를 적용 하기 전에.    

### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스의 알려진된 문제   
- 경고 제목 1804 업데이트를 설치 하는 동안 표시 될 수 있습니다 *오류 – FaultType UserAccounts.New에 대 한 템플릿이 누락 되었습니다.*  이러한 경고를 안전 하 게 무시할 수 있습니다. 이러한 경고 1804 업데이트가 완료 된 후 자동으로 닫힙니다.   
 
- <!-- TBD - IS --> 이 업데이트의 설치 하는 동안 가상 컴퓨터를 만들 하려고 하지 마십시오. 업데이트를 관리 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md#plan-for-updates)합니다.


### <a name="post-update-steps"></a>업데이트 후 단계
*1804 업데이트에 대 한 업데이트 후 단계가 없습니다.*



### <a name="known-issues-post-installation"></a>알려진된 문제 (설치 후)
빌드에 대 한 설치 후 알려진된 문제는 다음과 같은 **20180513.1**합니다.

#### <a name="portal"></a>포털
- <!-- 1272111 - IS --> 를 설치 하거나 스택 Azure의이 버전으로 업데이트 한 후 Azure 스택 배율 단위 관리자 포털에서 볼 수 없습니다.  
  해결 방법: 배율 단위에 대 한 정보를 보려면 PowerShell을 사용 하 여 합니다. 자세한 내용은 참조는 [도움말](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) 1.3.0 Azure 스택 모듈에 대 한 콘텐츠입니다. 

- <!-- 2332636 - IS -->  Azure 스택 id 시스템 및이 버전의 Azure 스택을 업데이트에 대 한 AD FS를 사용 하는 경우 기본 공급자 구독의 기본 소유자는 기본 제공으로 다시 설정 **CloudAdmin** 사용자입니다.  
  해결 방법:이 업데이트를 설치한 후이 문제를 해결 하려면 사용에서 3 단계는 [트리거를 구성 하는 자동화 클레임 공급자 트러스트 Azure 스택의](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) 프로시저는 구독 소유자의 기본 공급자를 다시 설정 합니다.   

- <!-- TBD - IS ASDK --> 일부 관리 구독 유형은 사용할 수 있습니다.  Azure 스택이이 버전으로 업그레이드 하면 두 가지 구독 유형을 했던 [1804 버전에 도입](#new-features) 콘솔에 표시 되지 않습니다. 예상된 동작입니다. 사용할 수 없는 구독 유형은 *구독 계량*, 및 *소비 구독*합니다. 이 구독 유형은 1804 버전부터 새로운 Azure 스택 환경에 표시 됩니다 되지만 아직 사용할 준비가 되지 않습니다. 계속 사용 해야는 *기본 공급자* 구독 유형입니다.  


- <!-- TBD -  IS ASDK -->기능 [새 지원 요청을 드롭다운 목록에서 열려는](azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털 사용할 수 없습니다. 대신, 다음 링크를 사용 합니다.     
    - Azure 스택 통합된 시스템을 사용 하 여 https://aka.ms/newsupportrequest합니다.

- <!-- 2403291 - IS ASDK --> 관리자 및 사용자 포털의 아래쪽에 가로 스크롤 막대를 사용 하 여가 없을 수도 있습니다. 포털의 상단에 있는 이동 경로 목록에서 표시할 블레이드 이름을 선택 하 여 포털에서 이전 블레이드로 이동 하려면 이동 경로 사용 하 여 남아 있는 가로 스크롤 막대에 액세스할 수 없는 경우.
  ![이동 경로 탐색](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> 관리자 포털에서 계산 또는 저장소 리소스를 보려면 하지 못할 수도 있습니다. 이 문제의 원인은 잘못 보고 될 성공으로를 업데이트 하는 업데이트를 설치 하는 동안 오류입니다. 이 문제가 발생 한 경우 Microsoft 고객 지원 서비스에 문의 하십시오.

- <!-- TBD - IS --> 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 톱니 바퀴형 아이콘을 선택 하 고 다음 선택 **기본 설정을 복원**합니다.

- <!-- TBD - IS ASDK --> 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- <!-- TBD - IS ASDK --> Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- <!-- TBD - IS ASDK --> 관리 포털에 대 한 중요 한 알림이 표시 될 수도 있습니다는 *Microsoft.Update.Admin* 구성 요소입니다. 경고 이름, 설명 및으로 표시 하는 모든 업데이트 관리:  
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
 

#### <a name="compute"></a>컴퓨팅
- <!-- TBD - IS --> 일부 F 시리즈 VM 크기는 가상 컴퓨터 배포에 대 한 가상 컴퓨터 크기를 선택할 때는 표시 되지 않습니다는 VM을 만들 때 크기 선택기의 일환으로 합니다. 다음 VM 크기 선택기에 표시 되지 않습니다: *F8s_v2*, *F16s_v2*, *F32s_v2*, 및 *F64s_v2*합니다.  
  문제를 해결 VM을 배포 하는 다음 방법 중 하나를 사용 합니다. 각 방법에 사용할 VM 크기를 지정 해야 합니다.

  - **Azure 리소스 관리자 템플릿:** 서식 파일을 사용 하면 설정는 *vmSize* 템플릿의 원하는 VM 크기와 같아야 합니다. 예를 들어 다음 하는 데 사용 하는 VM을 배포는 *F32s_v2* 크기:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** 사용할 수 있습니다는 [az vm 만들기](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 명령 및 VM 크기 비슷합니다 매개 변수로 지정 `--size "Standard_F32s_v2"`합니다.

  - **PowerShell:** Powershell을 사용할 수 있습니다 [새로 AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) 비슷합니다 v M 크기를 지정 하는 매개 변수와 함께 `-VMSize "Standard_F32s_v2"`합니다.


- <!-- TBD - IS ASDK --> 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- <!-- TBD - IS --> 가용성으로 이동 하 여 포털에서 집합을 만들 때 **새로** > **계산** > **가용성 집합**를 만들 수 있습니다는 가용성 장애 도메인과 업데이트 도메인 1으로 설정합니다. 해결 방법으로, 새 가상 컴퓨터를 만들 때, 가용성 집합 내에서 또는 PowerShell, CLI를 사용 하 여 만듭니다 포털입니다.

- <!-- TBD - IS ASDK --> Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 D 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. 모든 지원 되는 D 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- <!-- TBD - IS ASDK --> VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

- <!-- TBD - IS ASDK --> VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 IS ASDK --> Linux VM 진단 Azure 스택에서 지원 되지 않습니다. VM 진단을 사용 하는 Linux VM을 배포 하는 경우 배포에 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정 하는 경우에 배포 실패 합니다.  


#### <a name="networking"></a>네트워킹
- <!-- 1766332 - IS ASDK --> 아래 **네트워킹**누르면, **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 만 **경로 기반** 옵션은 Azure 스택에서 지원 합니다.

- <!-- 2388980 - IS ASDK --> VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 연결 되어 있던 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.

- <!-- 2292271 - IS ASDK --> 제안 및 연결 된 테 넌 트 구독 계획의 일부인 네트워크 리소스에 대 한 할당량 한도 늘릴 경우 해당 구독에 새 제한이 적용 되지 않습니다. 그러나 새 한도 할당량을 늘릴 후 생성 된 새 구독에 적용지 않습니다. 

  이 문제를 해결 하려면 계획 이미 구독과 연결 된 경우 네트워크 할당량을 늘려야 하는 추가 기능 계획을 사용 합니다. 자세한 내용은 참조 하는 방법 [플랜을 통해 추가 기능을 사용할 수 있도록](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)합니다.

- <!-- 2304134 IS ASDK --> DNS 영역 리소스 또는 연결 된 경로 테이블 리소스가 있는 구독을 삭제할 수 없습니다. 구독을 삭제 하려면 먼저 테 넌 트 구독에서 DNS 영역 및 경로 테이블 리소스를 삭제 해야 합니다. 
  

- <!-- 1902460 - IS ASDK --> 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- <!-- 16309153 - IS ASDK --> DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.

- <!-- TBD - IS ASDK --> Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.

- <!-- 2096388 IS --> 네트워크 보안 그룹에 대 한 규칙을 업데이트 하는 관리 포털을 사용할 수 없습니다. 

    응용 프로그램 서비스에 대 한 해결 방법: PowerShell 사용한 네트워크 보안 그룹 내에서 보안 규칙을 수정 컨트롤러 인스턴스를 원격 데스크톱을 해야 하는 경우.  다음은 하는 방법의 예 *허용*, 다음 구성을 사용 하 여 복원 *거부*:  
    
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

- <!-- TBD - IS --> 항목을 만드는 서버에서 해당 호스트 SQL 또는 MySQL 리소스 공급자에만 사용할 수 있습니다. 리소스 공급자가 생성 되지 않은 호스트 서버에 만든 항목 일치 하지 않는 상태가 될 수 있습니다.  

- <!-- IS, ASDK --> 특수 문자, 공백 및 마침표를 포함 하 여에서 지원 되지 않습니다는 **제품군** 또는 **계층** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름을 지정 합니다.


> [!NOTE]  
> <!-- TBD - IS --> Azure 스택 1804를 업데이트 한 후 이전에 배포 하는 SQL 및 MySQL 리소스 공급자를 사용 하도록 계속 수 있습니다.  새 릴리스는 사용할 수 있을 때 SQL 및 MySQL를 업데이트 하는 것이 좋습니다. Azure 스택과 마찬가지로 SQL 및 MySQL 리소스 공급자에 업데이트를 순서 대로 적용 합니다.  예를 들어 1802 버전을 사용 하는 경우 먼저 1803, 버전을 적용 하 고 1804로 업데이트 합니다.      
>   
> 1804 업데이트의 설치는 현재 사용자가 SQL 또는 MySQL 리소스 공급자를 사용 하는 영향을 주지 않습니다.
> 사용 리소스 공급자의 버전에 관계 없이 데이터베이스에 사용자가 데이터 연결, 아니며에 계속 액세스할 수 합니다.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> 사용자가 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- <!-- TBD - IS ASDK --> (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.

- <!-- TBD - IS ASDK --> 앱 서비스에만 배포할 수는 **공급자 구독 기본** 이 이번에 있습니다.  Azure 스택 1804에 도입 된 새 계량 구독으로 배포 응용 프로그램 서비스는 향후 업데이트에서 및 기존의 모든 배포를 새 구독에도 마이그레이션할 수 됩니다.

#### <a name="usage"></a>사용 현황  
- <!-- TBD - IS ASDK --> 사용 현황 공용 IP 주소 사용 계량 데이터를 보여 줍니다 동일한 *EventDateTime* 대신 각 레코드에 대 한 값은 *TimeDate* 레코드를 만들 때 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용 정확한 계정 작업을 수행 하려면이 데이터를 사용할 수 없습니다.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>업데이트 다운로드
Azure 스택 1804 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.


## <a name="see-also"></a>참고 항목
- 권한 있는 끝점 (PEP)를 모니터링 하 고 업데이트를 다시 시작을 사용 하려면 참조 [업데이트 권한이 있는 끝점을 사용 하 여 Azure 스택의 모니터링](azure-stack-monitor-update.md)합니다.
- Azure 스택에서 업데이트 관리의 개요를 참조 하십시오. [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md)합니다.
- Azure 스택을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택에서 업데이트 적용](azure-stack-apply-updates.md)합니다.
