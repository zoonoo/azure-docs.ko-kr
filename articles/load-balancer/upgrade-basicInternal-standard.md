---
title: 기본 내부에서 표준 내부 - Azure 로드 밸러커로 업그레이드
description: 이 문서에서는 Azure 내부 로드 밸러서를 기본 SKU에서 표준 SKU로 업그레이드하는 방법을 보여 주며
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770382"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Azure 내부 부하 분산 검사기 업그레이드- 아웃바운드 연결이 필요하지 않습니다.
[Azure 표준 로드 밸러저는](load-balancer-overview.md) 영역 중복성을 통해 풍부한 기능과 고가용성을 제공합니다. 로드 밸러워 SKU에 대한 자세한 내용은 [비교 표를](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)참조하십시오.

이 문서에서는 기본 로드 밸러저에서 표준 로드 밸러워로 트래픽을 마이그레이션하는 것과 함께 기본 로드 밸러블러와 동일한 구성의 표준 로드 밸워서를 만드는 PowerShell 스크립트를 소개합니다.

## <a name="upgrade-overview"></a>업그레이드 개요

Azure PowerShell 스크립트는 다음을 수행하는 데 사용할 수 있습니다.

* 지정한 위치에 표준 내부 SKU 부하 밸러커를 만듭니다. 표준 내부 부하 밸런서에서 [아웃바운드 연결이](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) 제공되지 않습니다.
* 기본 SKU 로드 밸러블러의 구성을 새로 만든 표준 로드 밸러블러에 원활하게 복사합니다.
* 프라이빗 IP를 기본 로드 밸러저에서 새로 생성된 표준 로드 밸러저로 원활하게 이동합니다.
* VM을 기본 로드 밸랜서의 백 엔드 풀에서 표준 로드 밸러서의 백 엔드 풀로 원활하게 이동

### <a name="caveatslimitations"></a>주의 사항\제한 사항

* 스크립트는 아웃바운드 연결이 필요하지 않은 경우에만 내부 로드 밸런서 업그레이드를 지원합니다. 일부 VM에 대한 [아웃바운드 연결이](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) 필요한 경우 이 [페이지를](upgrade-InternalBasic-To-PublicStandard.md) 참조하십시오. 
* 표준 로드 밸러블러가 다른 리전에서 생성되는 경우 이전 지역에 있는 VM을 새로 만든 표준 로드 밸러커에 연결할 수 없습니다. 이러한 제한을 해결하려면 새 리전에서 새 VM을 만들어야 합니다.
* 로드 밸런서에 프런트 엔드 IP 구성 또는 백 엔드 풀이 없는 경우 스크립트를 실행하는 오류가 발생할 수 있습니다. 비어 있지 않은지 확인합니다.

## <a name="download-the-script"></a>스크립트 다운로드

[PowerShell 갤러리에서](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0)마이그레이션 스크립트를 다운로드합니다.
## <a name="use-the-script"></a>스크립트 사용

로컬 PowerShell 환경 설정 및 기본 설정에 따라 두 가지 옵션이 있습니다.

* Azure Az 모듈이 설치되어 있지 않거나 Azure Az 모듈을 제거해도 괜찮다면 이 `Install-Script` 옵션을 사용하여 스크립트를 실행하는 것이 가장 좋습니다.
* Azure Az 모듈을 유지해야 하는 경우 스크립트를 다운로드하여 직접 실행하는 것이 가장 좋습니다.

Azure Az 모듈이 설치되어 있는지 확인하려면 을 실행합니다. `Get-InstalledModule -Name az` 설치된 Az 모듈이 표시되지 않으면 이 `Install-Script` 방법을 사용할 수 있습니다.

### <a name="install-using-the-install-script-method"></a>설치 스크립트 방법을 사용하여 설치

이 옵션을 사용하려면 컴퓨터에 Azure Az 모듈이 설치되어 있지 않아야 합니다. 설치된 경우 다음 명령에 오류가 표시됩니다. Azure Az 모듈을 제거하거나 다른 옵션을 사용하여 스크립트를 수동으로 다운로드하여 실행할 수 있습니다.
  
다음 명령을 사용하여 스크립트를 실행합니다.

`Install-Script -Name AzureILBUpgrade`

이 명령은 필요한 Az 모듈도 설치합니다.  

### <a name="install-using-the-script-directly"></a>스크립트를 사용하여 직접 설치

일부 Azure Az 모듈이 설치되어 있고 제거할 수 없거나 제거할 수 없는 경우 스크립트 다운로드 링크의 수동 **다운로드** 탭을 사용하여 스크립트를 수동으로 다운로드할 수 있습니다. 스크립트는 원시 nupkg 파일로 다운로드됩니다. 이 nupkg 파일에서 스크립트를 설치하려면 [수동 패키지 다운로드](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)를 참조하십시오.

스크립트를 실행하려면

1. Azure에 연결하는 데 사용합니다. `Connect-AzAccount`

1. Az `Import-Module Az` 모듈을 가져오는 데 사용합니다.

1. 필요한 매개 변수를 검사합니다.

   * **rgName: [문자열]: 필수** – 기존 기본 로드 밸러터 및 새 표준 로드 밸러서에 대한 리소스 그룹입니다. 이 문자열 값을 찾으려면 Azure 포털로 이동하여 기본 로드 밸러터 소스를 선택하고 로드 밸러터에 대한 **개요를** 클릭합니다. 리소스 그룹은 해당 페이지에 있습니다.
   * **oldLBName: [문자열]: 필수** – 업그레이드하려는 기존 기본 밸러서의 이름입니다. 
   * **newlocation: [문자열]: 필수** – 표준 로드 밸러서가 생성되는 위치입니다. 선택한 기본 로드 밸러서의 동일한 위치를 다른 기존 리소스와 더 잘 연결하려면 표준 로드 밸러블러에 상속하는 것이 좋습니다.
   * **newLBName: [문자열]: 필수** – 이 이름은 만들 표준 로드 밸러서의 이름입니다.
1. 적절한 매개 변수를 사용하여 스크립트를 실행합니다. 완료하는 데 5~7분정도 걸릴 수 있습니다.

    **예제**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>일반적인 질문

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell 스크립트에서 구성을 v1에서 v2로 마이그레이션하는 데 제한이 있습니까?

예. [주의 사항/제한 사항을](#caveatslimitations)참조하십시오.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Azure PowerShell 스크립트도 기본 로드 밸러서에서 새로 생성된 표준 로드 밸러저로 트래픽을 전환합니까?

예, 트래픽을 마이그레이션합니다. 트래픽을 개인적으로 마이그레이션하려면 VM을 이동하지 않는 [이 스크립트를](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) 사용하십시오.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>이 스크립트를 사용하는 데 몇 가지 문제가 있습니다. 어떻게 도움을 받을 수 있나요?
  
slbupgradesupport@microsoft.com에 전자 메일을 보낼 수 있습니다 ., Azure 지원 으로 지원 사례를 열거나 둘 다 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[표준 로드 밸워서에 대해 알아보기](load-balancer-overview.md)
