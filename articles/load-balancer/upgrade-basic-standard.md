---
title: 기본 공용에서 표준 공용-Azure Load Balancer로 업그레이드
description: 이 문서에서는 Azure Public Load Balancer를 Basic SKU에서 Standard SKU로 업그레이드 하는 방법을 보여 줍니다.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 3bf910d3309285c8b700c39af68fb90715f8863a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987717"
---
# <a name="upgrade-azure-public-load-balancer"></a>Azure Public Load Balancer 업그레이드
[Azure 표준 Load Balancer](load-balancer-overview.md) 는 영역 중복성을 통해 다양 한 기능 및 고가용성 집합을 제공 합니다. Load Balancer SKU에 대 한 자세한 내용은 [비교 표](./skus.md#skus)를 참조 하세요.

업그레이드에는 두 단계가 있습니다.

1. IP 할당 방법을 동적에서 정적으로 변경 합니다.
2. PowerShell 스크립트를 실행 하 여 업그레이드 및 트래픽 마이그레이션을 완료 합니다.

> [!IMPORTANT]
> 이 스크립트는 현재 유지 관리 중입니다. 기본 SKU 및 표준 SKU에서 공용 IP 주소를 업그레이드 하는 방법 [에 대 한 지침을](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address-upgrade?tabs=option-upgrade-cli%2Coption-migrate-powershell#tabpanel_CeZOj-G++Q_option-upgrade-cli) 참조할 수 있습니다.

## <a name="upgrade-overview"></a>업그레이드 개요

다음을 수행 하는 Azure PowerShell 스크립트를 사용할 수 있습니다.

* 기본 표준 Load Balancer의 동일한 리소스 그룹에서 지정 하는 위치를 사용 하 여 표준 SKU Load Balancer를 만듭니다.
* 기본 SKU에서 표준 SKU로 공용 IP 주소를 업그레이드 합니다.
* 기본 SKU Load Balancer의 구성을 새로 만든 표준 Load Balancer에 원활 하 게 복사 합니다.
* 아웃 바운드 연결을 사용 하는 기본 아웃 바운드 규칙을 만듭니다.

### <a name="caveatslimitations"></a>Caveats\Limitations

* 스크립트는 공용 Load Balancer 업그레이드만 지원 합니다. 내부 기본 Load Balancer 업그레이드에 대 한 자세한 내용은 [이 페이지](./upgrade-basicinternal-standard.md) 를 참조 하세요.
* 스크립트를 실행 하기 전에 공용 IP 주소의 할당 방법을 "정적"으로 변경 해야 합니다. 
* Load Balancer 프런트 엔드 IP 구성 또는 백 엔드 풀이 없는 경우 스크립트를 실행 하는 동안 오류가 발생할 수 있습니다. 비어 있지 않은지 확인 하세요.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>공용 IP 주소의 할당 방법을 정적으로 변경

* * * 권장 단계는 다음과 같습니다.

    1. 이 빠른 시작의 작업을 수행하려면 [Azure Portal](https://portal.azure.com)에 로그인해야 합니다.
 
    1. 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **기본 Load Balancer와 연결 된 기본 공용 IP 주소** 를 선택 합니다.
   
    1. **설정** 아래에서 **구성** 을 선택 합니다.
   
    1. **할당** 에서 **고정** 을 선택합니다.
    1. **저장** 을 선택합니다.
    >[!NOTE]
    >공용 ip가 있는 Vm의 경우에는 동일한 IP 주소를 보장 하지 않는 표준 IP 주소를 먼저 만들어야 합니다. 기본 Ip에서 Vm의 연결을 끊고 새로 만든 표준 IP 주소에 연결 합니다. 그런 다음 표준 Load Balancer의 백 엔드 풀에 Vm을 추가 하는 지침을 따를 수 있습니다. 

* **새로 만든 표준 공용 Load Balancer의 백 엔드 풀에 추가할 새 vm을 만듭니다**.
    * VM을 만들고 표준 Load Balancer와 연결 하는 방법에 대 한 자세한 지침은 [여기](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines)에서 찾을 수 있습니다.


## <a name="download-the-script"></a>스크립트 다운로드

[PowerShell 갤러리](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0)에서 마이그레이션 스크립트를 다운로드 합니다.
## <a name="use-the-script"></a>스크립트 사용

로컬 PowerShell 환경 설정 및 기본 설정에 따라 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

* Azure Az 모듈이 설치 되어 있지 않거나 Azure Az 모듈을 제거 하는 것이 아닌 경우 옵션을 사용 하 여 스크립트를 실행 하는 것이 가장 좋습니다 `Install-Script` .
* Azure Az modules를 유지 해야 하는 경우에는 스크립트를 다운로드 하 여 직접 실행 하는 것이 가장 좋습니다.

Azure Az 모듈이 설치 되어 있는지 확인 하려면를 실행 `Get-InstalledModule -Name az` 합니다. 설치 된 Az modules가 표시 되지 않으면 메서드를 사용할 수 있습니다 `Install-Script` .

### <a name="install-using-the-install-script-method"></a>Install-Script 메서드를 사용 하 여 설치

이 옵션을 사용 하려면 컴퓨터에 Azure Az 모듈이 설치 되어 있지 않아야 합니다. 설치 된 경우 다음 명령에서 오류를 표시 합니다. Azure Az modules를 제거 하거나 다른 옵션을 사용 하 여 스크립트를 수동으로 다운로드 하 여 실행할 수 있습니다.
  
다음 명령을 사용하여 스크립트를 실행합니다.

`Install-Script -Name AzurePublicLBUpgrade`

또한이 명령은 필요한 Az modules을 설치 합니다.  

### <a name="install-using-the-script-directly"></a>스크립트를 사용 하 여 직접 설치

일부 Azure Az 모듈이 설치 되어 있고 제거할 수 없는 경우 (또는 제거 하지 않으려는 경우) 스크립트 다운로드 링크의 **수동 다운로드** 탭을 사용 하 여 스크립트를 수동으로 다운로드할 수 있습니다. 이 스크립트는 원시 nupkg 파일로 다운로드 됩니다. 이 nupkg 파일에서 스크립트를 설치 하려면 [수동 패키지 다운로드](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)를 참조 하세요.

스크립트를 실행하려면

1. `Connect-AzAccount`를 사용 하 여 Azure에 연결 합니다.

1. `Import-Module Az`를 사용 하 여 Az 모듈을 가져옵니다.

1. 필수 매개 변수를 검사 합니다.

   * **Oldrgname: [String]: 필수** – 업그레이드 하려는 기존 기본 Load Balancer의 리소스 그룹입니다. 이 문자열 값을 찾으려면 Azure Portal로 이동 하 고, 기본 Load Balancer 원본을 선택 하 고, 부하 분산 장치에 대 한 **개요** 를 클릭 합니다. 리소스 그룹은 해당 페이지에 있습니다.
   * **Oldlbname: [String]: 필수** – 업그레이드 하려는 기존 기본 분산 장치의 이름입니다. 
   * **newLBName: [String]: Required** – 만들려는 표준 Load Balancer의 이름입니다.
1. 적절 한 매개 변수를 사용 하 여 스크립트를 실행 합니다. 완료 하는 데 5 ~ 7 분 정도 걸릴 수 있습니다.

    **예제**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>아웃 바운드 연결에 대 한 아웃 바운드 규칙 만들기

[지침](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) 에 따라 아웃 바운드 규칙을 만들 수 있습니다.
* 아웃 바운드 NAT를 처음부터 정의 합니다.
* 기존 아웃 바운드 NAT의 동작을 확장 하 고 조정 합니다.

## <a name="common-questions"></a>일반적인 질문

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>V1에서 v 2로 구성을 마이그레이션하기 위한 Azure PowerShell 스크립트에 제한이 있나요?

예. [주의 사항/제한 사항](#caveatslimitations)을 참조 하세요.

### <a name="how-long-does-the-upgrade-take"></a>업그레이드는 얼마나 걸립니까?

스크립트를 완료 하는 데는 일반적으로 약 5-10 분이 소요 되며 Load Balancer 구성의 복잡성에 따라 시간이 오래 걸릴 수 있습니다. 따라서 가동 중지 시간을 염두에 두고 필요한 경우 장애 조치 (failover)를 계획 합니다.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>또한 Azure PowerShell 스크립트는 기본 Load Balancer에서 새로 만든 표준 Load Balancer으로 트래픽을 전환 하나요?

예. Azure PowerShell 스크립트는 공용 IP 주소를 업그레이드할 뿐만 아니라, 구성을 Basic에서 표준 Load Balancer로 복사 하 고, 새로 만든 표준 공용 Load Balancer 뒤에도 VM을 마이그레이션합니다. 

## <a name="next-steps"></a>다음 단계

[표준 Load Balancer에 대해 알아보기](load-balancer-overview.md)
