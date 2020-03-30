---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117179"
---
공유 이미지 갤러리는 관리되는 이미지를 중심으로 구조와 조직을 구축하는 데 도움이 되는 서비스입니다. 공유 이미지 갤러리는 다음을 제공합니다.

- 이미지의 글로벌 복제를 관리합니다.
- 더 쉬운 관리를 위해 이미지의 버전 관리 및 그룹화.
- 가용성 영역을 지원하는 리전소의 ZRS(영역 중복 저장소) 계정이 있는 고가용성 이미지입니다. ZRS는 구역 오류에 대한 더 나은 복원력을 제공합니다.
- RBAC를 사용하여 구독 간에, 심지어 AD(Active Directory) 테넌자 간에 공유합니다.
- 각 리전에서 이미지 복제본으로 배포를 확장합니다.

공유 이미지 갤러리를 사용하면 조직 내의 여러 사용자, 서비스 주체 또는 AD 그룹에게 이미지를 공유할 수 있습니다. 배포의 크기를 더 빠르게 조정하기 위해 여러 지역에 공유 이미지를 복제할 수 있습니다.

관리되는 이미지는 이미지를 만드는 방법에 따라 전체 VM(연결된 데이터 디스크 포함) 또는 OS 디스크의 사본입니다. 이미지에서 VM을 만드는 경우 이미지의 VHD 복사본을 사용하여 새 VM의 디스크를 만듭니다. 관리되는 이미지는 스토리지에 유지되며 새 VM을 만들 때 계속 반복해서 사용할 수 있습니다.

유지 관리해야 하는 관리되는 이미지가 많고 회사 전체에서 이미지를 사용할 수 있도록 하려면 공유 이미지 갤러리를 리포지토리로 사용하여 이미지를 쉽게 공유할 수 있습니다. 

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다.

| 리소스 | 설명|
|----------|------------|
| **관리되는 이미지** | 단독으로 사용하거나 이미지 갤러리에서 이미지 버전을 만드는 데 사용할 수 있는 기본 **이미지입니다.** 관리되는 이미지는 [일반화된](#generalized-and-specialized-images) VM에서 만들어집니다. 관리되는 이미지는 여러 VM을 만드는 데 사용할 수 있는 특수한 유형의 VHD로, 이제 공유 이미지 버전을 만드는 데 사용할 수 있습니다. |
| **스냅숏** | **이미지 버전을**만드는 데 사용할 수 있는 VHD 의 복사본입니다. 스냅샷은 특수VM(일반화되지 않은 VM)에서 단독으로 사용하거나 데이터 디스크의 스냅숏과 함께 사용하여 특수화된 이미지 버전을 만들 수 있습니다. [specialized](#generalized-and-specialized-images)
| **이미지 갤러리** | Azure Marketplace와 마찬가지로 **이미지 갤러리**는 이미지를 관리하고 공유하는 데 사용되는 리포지토리이지만 액세스할 수 있는 사람을 제어할 수 있습니다. |
| **이미지 정의** | 이미지는 갤러리 내에 정의되며 이미지에 대한 정보와 조직 내에서 이미지 사용 요구 사항을 전달합니다. 이미지가 일반화되어 있는지 또는 전문화되었는지, 운영 체제, 최소 및 최대 메모리 요구 사항 및 릴리스 정보와 같은 정보를 포함할 수 있습니다. 이미지의 형식 정의입니다. |
| **이미지 버전** | **이미지 버전**은 갤러리를 사용하는 경우 VM을 만들 때 사용합니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. 관리되는 이미지와 마찬가지로 **이미지 버전**을 사용하여 VM을 만들 때는 이미지 버전을 사용하여 VM의 새 디스크를 만듭니다. 이미지 버전은 여러 번 사용할 수 있습니다. |

<br>

![갤러리에서 한 이미지의 여러 버전을 보유하는 방법을 보여주는 그래픽](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>이미지 정의

이미지 정의는 이미지 버전에 대한 논리적 그룹입니다. 이미지 정의에는 이미지가 생성된 이유, 사용 OS에 대한 정보 및 이미지 사용에 대한 정보가 있습니다. 이미지 정의는 특정 이미지 만들기와 관련된 모든 세부 사항에 대한 계획과 같습니다. 이미지 정의에서 VM을 배포하지 않고 정의에서 만든 이미지 버전에서 배포합니다.

각 이미지 정의에 대한 세 가지 매개 변수가 조합되어 있습니다 - **게시자,** **제안** 및 **SKU**. 특정 이미지 정의를 찾는 데 사용됩니다. 3개 값을 모두 공유하지는 않으면서 1개 또는 2개의 값을 공유하는 이미지 버전을 사용할 수 있습니다.  예를 들어 다음은 3개의 이미지 정의와 해당 값입니다.

|이미지 정의|게시자|제안|SKU|
|---|---|---|---|
|myImage1|Contoso|재무|백 엔드|
|myImage2|Contoso|재무|프런트 엔드|
|myImage3|테스트|재무|프런트 엔드|

이러한 세 가지 정의는 모두 고유한 값 세트를 갖습니다. 이 형식은 Azure PowerShell에서 [Azure 마켓플레이스 이미지에](../articles/virtual-machines/windows/cli-ps-findimage.md) 대한 게시자, 제공 및 SKU를 현재 지정하여 최신 버전의 마켓플레이스 이미지를 얻는 방법과 유사합니다. 각 이미지 정의에는 이러한 값의 고유한 집합이 있어야 합니다.

다음은 리소스를 보다 쉽게 추적할 수 있도록 이미지 정의에서 설정할 수 있는 다른 매개 변수입니다.

* 운영 체제 상태 - OS 상태를 [일반화 또는 특수으로](#generalized-and-specialized-images)설정할 수 있습니다.
* 운영 체제 - 윈도우 또는 리눅스 중 하나가 될 수 있습니다.
* 설명 - 설명을 사용하여 이미지 정의가 존재하는 이유에 대한 자세한 정보를 제공합니다. 예를 들어 응용 프로그램이 사전 설치된 프런트 엔드 서버에 대한 이미지 정의가 있을 수 있습니다.
* Eula-이미지 정의에 특정한 최종 사용자 라이선스 계약을 가리키는 데 사용할 수 있습니다.
* 개인 정보 취급 방침 및 릴리스 정보 - Azure 저장소에 릴리스 정보 및 개인 정보 취급 방침을 저장하고 이미지 정의의 일부로 액세스하기 위한 URI를 제공합니다.
* 단수 일자 - 자동화를 사용하여 이전 이미지 정의를 삭제할 수 있도록 이미지 정의에 단종 날짜를 첨부합니다.
* 태그 - 이미지 정의를 만들 때 태그를 추가할 수 있습니다. 태그에 대한 자세한 내용은 [태그 사용을 참조하여 리소스를 구성합니다.](../articles/azure-resource-manager/management/tag-resources.md)
* 최소 및 최대 vCPU 및 메모리 권장 사항 - 이미지에 vCPU 및 메모리 권장 사항이 있는 경우 해당 정보를 이미지 정의에 첨부할 수 있습니다.
* 허용되지 않는 디스크 유형 - VM의 저장소 요구 사항에 대한 정보를 제공할 수 있습니다. 예를 들어 이미지가 표준 HDD 디스크에 적합하지 않은 경우 허용되지 않는 목록에 추가합니다.

## <a name="generalized-and-specialized-images"></a>일반화 및 특수 이미지

공유 이미지 갤러리에서 지원하는 두 가지 운영 체제 상태가 있습니다. 일반적으로 이미지는 이미지를 만들기 전에 이미지를 만드는 데 사용되는 VM이 일반화되어야 합니다. 일반화는 VM에서 컴퓨터 및 사용자 별 정보를 제거하는 프로세스입니다. 윈도우의 경우 Sysprep도 사용됩니다. Linux의 경우 [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` 또는 `-deprovision+user` 매개 변수를 사용할 수 있습니다.

특수 VM은 컴퓨터 특정 정보 및 계정을 제거하는 프로세스를 거치지 않았습니다. 또한 특수 이미지에서 만든 VM에는 `osProfile` 연관된 VM이 없습니다. 즉, 특수 이미지에는 몇 가지 제한사항이 있습니다.

- VM에 로그인하는 데 사용할 수 있는 계정은 해당 VM에서 만든 특수 이미지를 사용하여 만든 모든 VM에서도 사용할 수 있습니다.
- VM에는 이미지가 가져온 VM의 **컴퓨터 이름이** 있습니다. 충돌을 방지하려면 컴퓨터 이름을 변경해야 합니다.
- 은 `osProfile` `secrets`일부 중요한 정보를 사용하여 VM에 전달되는 방법입니다. 이로 인해 에서 사용하는 `secrets` KeyVault, WinRM 및 `osProfile`기타 기능을 사용하는 데 문제가 발생할 수 있습니다. 경우에 따라 관리 되는 서비스 ID (MSI)를 사용 하 여 이러한 제한 사항을 해결할 수 있습니다.

> [!IMPORTANT]
> 특수 이미지는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> **알려진 미리 보기 제한 사항** VM은 포털 또는 API를 사용하여 특수 이미지에서만 만들 수 있습니다. 미리 보기에 대한 CLI 또는 PowerShell 지원이 없습니다.


## <a name="regional-support"></a>국가별 지원

원본 영역은 아래 표에 나열되어 있습니다. 모든 공용 지역은 대상 지역이 될 수 있지만 호주 중부 및 오스트레일리아 중부 2로 복제하려면 구독을 화이트리스트에 등록해야 합니다. 허용 목록 포함을 요청하려면 https://azure.microsoft.com/global-infrastructure/australia/contact/로 이동합니다.


| 소스 지역        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| 오스트레일리아 중부     | 중국 동부        | 인도 남부        | 서유럽        |
| 오스트레일리아 중부 2   | 중국 동부 2      | 동남아시아     | 영국 남부           |
| 오스트레일리아 동부        | 중국 북부       | 일본 동부         | 영국 서부            |
| 오스트레일리아 남동부   | 중국 북부 2     | 일본 서부         | US DoD 중부     |
| 브라질 남부          | 동아시아         | 한국 중부      | US DoD 동부        |
| 캐나다 중부        | 미국 동부           | 한국 남부        | US Gov 애리조나     |
| 캐나다 동부           | 미국 동부 2         | 미국 중북부   | US Gov 텍사스       |
| 인도 중부         | 미국 동부 2 EUAP    | 북유럽       | US Gov 버지니아    |
| 미국 중부            | 프랑스 중부    | 미국 중남부   | 인도 서부         |
| 미국 중부 EUAP       | 프랑스 남부      | 미국 중서부    | 미국 서부            |
|                       |                   |                    | 미국 서부 2          |



## <a name="limits"></a>제한 

공유 이미지 갤러리를 사용하여 리소스를 배포하는 경우 구독당 제한이 있습니다.
- 지역별 구독당 공유 이미지 갤러리 100개
- 리전당 구독당 1,000개의 이미지 정의
- 리전당 구독당 10,000개의 이미지 버전
- 이미지에 연결된 디스크의 크기는 1TB 미만이거나 같아야 합니다.

자세한 내용은 현재 사용량을 확인하는 방법에 대한 예제에 [대한 제한에 대한 리소스 사용량 확인을](https://docs.microsoft.com/azure/networking/check-usage-against-limits) 참조하세요.
 
## <a name="scaling"></a>확장
공유 이미지 갤러리를 사용하면 Azure에서 이미지를 보관하도록 하려는 복제본 수를 지정할 수 있습니다. 이렇게 하면 VM 배포를 여러 복제본으로 확대함으로써 단일 복제본 과부하로 인해 인스턴스 생성 처리가 제한될 가능성을 줄일 수 있으므로 다중 VM 배포 시나리오에 도움이 됩니다.

이제 공유 이미지 갤러리를 사용하면 관리되는 이미지가 있는 600개에서 최대 가상 시스템 규모 설정으로 최대 1,000VM 인스턴스를 배포할 수 있습니다. 이미지 복제본은 배포 성능, 안정성 및 일관성을 향상시킵니다. 지역에 대한 배율 요구에 따라 각 대상 지역에서 다른 복제본 수를 설정할 수 있습니다. 각 복제본은 이미지의 깊은 복사본이므로 각 추가 복제본을 통해 배포를 선형으로 확장하는 데 도움이 됩니다. 두 이미지 나 영역이 동일하지 는 않지만 리전에서 복제본을 사용하는 방법에 대한 일반적인 지침은 다음과 같습니다.

- VMSS(비가상 시스템 확장 집합) 배포의 경우 - 동시에 만드는 20개의 VM마다 하나의 복제본을 유지하는 것이 좋습니다. 예를 들어 리전에서 동일한 이미지를 사용하여 120개의 VM을 동시에 만드는 경우 이미지의 복제본을 6개 이상 유지하는 것이 좋습니다. 
- VMSS(가상 시스템 확장 집합) 배포의 경우 - 인스턴스가 최대 600개가 있는 모든 스케일 집합 배포에 대해 하나 이상의 복제본을 유지하는 것이 좋습니다. 예를 들어 단일 리전에서 동일한 이미지를 사용하는 600개의 VM 인스턴스가 있는 5개의 배율 집합을 동시에 만드는 경우 이미지의 복제본을 5개 이상 유지하는 것이 좋습니다. 

항상 이미지 크기, 콘텐츠 및 OS 유형과 같은 요인으로 인해 복제본 수를 오버프로비전하는 것이 좋습니다.

![이미지를 확장하는 방법을 보여 주는 그래픽](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>이미지를 고도로 사용할 수 있도록 만들기

[Azure 영역 중복 저장소(ZRS)는](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) 리전의 가용성 영역 오류에 대한 복원력을 제공합니다. 공유 이미지 갤러리의 일반 사용 가능 으로, 가용성 영역이 있는 지역에서 ZRS 계정에 이미지를 저장 하도록 선택할 수 있습니다. 

각 대상 지역에 대한 계정 유형을 선택할 수도 있습니다. 기본 저장소 계정 유형은 Standard_LRS 있지만 가용성 영역이 있는 지역의 Standard_ZRS 선택할 수 있습니다. 여기에서 ZRS의 지역 별 가용성을 [확인하십시오.](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)

![ZRS를 보여주는 그래픽](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>복제
공유 이미지 갤러리를 사용하여 다른 Azure 지역에 이미지를 자동으로 복제할 수도 있습니다. 조직에 적합한 방식으로 각 공유 이미지 버전을 여러 지역에 복제할 수 있습니다. 한 가지 예는 다중 지역에서 최신 이미지를 항상 복제하는 것이지만 이전 버전은 모두 1개 지역에서만 사용 가능합니다. 공유 이미지 버전의 스토리지 비용을 절약하는 데 도움이 될 수 있습니다. 

공유 이미지 버전이 복제되는 지역은 생성 시간 후 업데이트할 수 있습니다. 여러 지역에 복제하는 데 걸리는 시간은 복사되는 데이터의 양과 버전이 복제되는 지역 수에 따라 다릅니다. 경우에 따라 몇 시간이 걸릴 수 있습니다. 복제가 진행 중일 때 지역별 복제 상태를 볼 수 있습니다. 리전에서 이미지 복제가 완료되면 해당 이미지 버전을 사용하여 VM또는 배율 집합을 배포할 수 있습니다.

![이미지를 복제하는 방법을 보여 주는 그래픽](./media/shared-image-galleries/replication.png)

## <a name="access"></a>액세스 권한

공유 이미지 갤러리, 이미지 정의 및 이미지 버전은 모두 리소스이기 때문에 기본 제공 네이티브 Azure RBAC 컨트롤을 사용하여 공유할 수 있습니다. RBAC를 사용하면 이러한 리소스를 다른 사용자, 서비스 주체 및 그룹과 공유할 수 있습니다. 생성된 테넌트 외부의 개인에 대한 액세스를 공유할 수도 있습니다. 사용자가 공유 이미지 버전에 액세스할 수 있게 되면 VM 또는 가상 시스템 크기 집합을 배포할 수 있습니다.  사용자가 액세스할 수 있는 항목을 이해하는 데 도움이 되는 공유 행렬은 다음과 같습니다.

| 공유한 항목     | 공유 이미지 갤러리 | 이미지 정의 | 이미지 버전 |
|----------------------|----------------------|--------------|----------------------|
| 공유 이미지 갤러리 | yes                  | yes          | yes                  |
| 이미지 정의     | 예                   | yes          | yes                  |

최상의 경험을 위해 갤러리 수준에서 공유하는 것이 좋습니다. 개별 이미지 버전을 공유하지 않는 것이 좋습니다. RBAC에 대한 자세한 내용은 [RBAC를 사용하여 Azure 리소스에 대한 액세스 관리를](../articles/role-based-access-control/role-assignments-portal.md)참조하십시오.

다중 테넌트 앱 등록을 사용하는 테넌트 전체에서도 이미지를 대규모로 공유할 수도 있습니다. 테넌자 간에 이미지 공유에 대한 자세한 내용은 [Azure 테넌에서 갤러리 VM 이미지 공유를](../articles/virtual-machines/linux/share-images-across-tenants.md)참조하십시오.

## <a name="billing"></a>결제
공유 이미지 갤러리 서비스 사용에 대한 추가 비용은 없습니다. 다음 리소스에 대한 비용이 청구됩니다.
- 공유 이미지 버전 저장 시 스토리지 비용. 비용은 이미지 버전의 복제본 수와 버전이 복제되는 지역 수에 따라 달라집니다. 예를 들어 이미지가 2개있고 둘 다 3개 영역으로 복제된 경우 크기에 따라 관리되는 디스크 6개에 대해 요금이 부과됩니다. 자세한 내용은 [관리 디스크 가격 책정을](https://azure.microsoft.com/pricing/details/managed-disks/)참조하십시오.
- 네트워크 송신은 원본 리전에서 복제된 영역으로의 첫 번째 이미지 버전 복제에 대한 요금을 부과합니다. 후속 복제본은 리전 내에서 처리되므로 추가 요금이 부과되지 않습니다. 

## <a name="updating-resources"></a>리소스 업데이트

일단 생성되면 이미지 갤러리 리소스를 일부 변경할 수 있습니다. 다음은 다음과 같은 것으로 제한됩니다.
 
공유 이미지 갤러리:
- 설명

이미지 정의:
- 권장 vCPU
- 권장 메모리
- 설명
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신 항목에서 제외
- 수명 주기 끝

## <a name="sdk-support"></a>SDK 지원

다음 SDK를 사용하면 공유 이미지 갤러리를 만들 수 있습니다.

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [이동](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>템플릿

템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [Shared Image Gallery 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [이미지 버전에서 VM 만들기](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>질문과 대답 

* [구독 간에 모든 공유 이미지 갤러리 리소스를 나열하는 방법은 무엇인가요?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [기존 이미지를 공유 이미지 갤러리로 이동할 수 있나요?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [특수한 디스크에서 이미지 버전을 만들 수 있나요?](#can-i-create-an-image-version-from-a-specialized-disk)
* [공유 이미지 갤러리 리소스를 만든 후 다른 구독으로 이동할 수 있습니까?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Azure China 21Vianet 또는 Azure 독일 또는 Azure 정부 클라우드와 같은 클라우드에서 이미지 버전을 복제할 수 있습니까?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [내 이미지 버전을 구독 간에 복제할 수 있나요?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Azure AD 테넌트 간에 이미지 버전을 공유할 수 있나요?](#can-i-share-image-versions-across-azure-ad-tenants)
* [대상 지역 간에 이미지 버전을 복제하려면 얼마나 걸리나요?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [원본 지역과 대상 지역 사이의 차이점은 무엇인가요?](#what-is-the-difference-between-source-region-and-target-region)
* [이미지 버전을 만드는 동안 원본 영역을 지정하는 방법은 무엇인가요?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [각 지역에 만들 이미지 버전 복제본 수를 지정하는 방법은 무엇인가요?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [이미지 정의 및 이미지 버전과 다른 위치에 공유 이미지 갤러리를 만들 수 있습니까?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [공유 이미지 갤러리 사용 요금은 얼마인가요?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [공유 이미지 갤러리 및 이미지 정의 및 이미지 버전을 만드는 데 어떤 API 버전을 사용해야 합니까?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [이미지 버전에서 공유 VM 또는 가상 컴퓨터 스케일 세트를 만드는 데 어떤 API 버전을 사용해야 합니까?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>구독 간에 모든 공유 이미지 갤러리 리소스를 나열하는 방법은 무엇인가요?

Azure 포털에서 액세스할 수 있는 구독의 모든 공유 이미지 갤러리 리소스를 나열하려면 아래 단계를 따르세요.

1. Azure [포털을](https://portal.azure.com)엽니다.
1. **모든 리소스**로 이동합니다.
1. 모든 리소스를 나열하려는 모든 구독을 선택합니다.
1. **프라이빗 갤러리** 형식의 리소스를 찾습니다.
 
   이미지 정의 및 이미지 버전을 확인하려면 **숨겨진 형식 표시**도 선택해야 합니다.
 
   사용 권한이 있는 구독 간에 공유 이미지 갤러리 리소스를 나열하려면 Azure CLI에서 다음 명령을 사용하세요.

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>기존 이미지를 공유 이미지 갤러리로 이동할 수 있나요?
 
예. 가지고 있는 이미지의 유형에 따라 3가지 시나리오가 있습니다.

 시나리오 1: SIG와 동일한 구독에 관리되는 이미지가 있는 경우 이미지 정의 및 이미지 버전을 만들 수 있습니다.

 시나리오 2: SIG와 동일한 구독에 관리되지 않는 이미지가 있는 경우 관리되는 이미지를 만든 다음 이미지 정의 및 이미지 버전을 만들 수 있습니다. 

 시나리오 3: 로컬 파일 시스템에 VHD가 있는 경우 관리되는 이미지에 VHD를 업로드해야 하는 경우 이미지 정의 및 이미지 버전을 만들 수 있습니다.

- VHD가 Windows VM인 경우 [VHD 업로드를](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)참조하십시오.
- Linux VM용 VHD일 경우 [VHD 업로드](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)를 참조하세요.

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>특수한 디스크에서 이미지 버전을 만들 수 있나요?

예. 이미지가 미리 보기로 되어 있는 특수 디스크에 대한 지원입니다. [포털(Windows](../articles/virtual-machines/linux/shared-images-portal.md) 또는 [Linux)](../articles/virtual-machines/linux/shared-images-portal.md)및 API를 사용하여 특수 이미지에서만 VM을 만들 수 있습니다. 미리 보기에 는 PowerShell 지원이 없습니다.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>공유 이미지 갤러리 리소스를 만든 후 다른 구독으로 이동할 수 있습니까?

아니요. 공유 이미지 갤러리 리소스를 다른 구독으로 이동할 수는 없습니다. 그러나 필요에 따라 갤러리의 이미지 버전을 다른 지역으로 복제할 수는 있습니다.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Azure China 21Vianet 또는 Azure 독일 또는 Azure 정부 클라우드와 같은 클라우드에서 이미지 버전을 복제할 수 있습니까?

아니요, 클라우드 간에 이미지 버전을 복제할 수는 없습니다.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>내 이미지 버전을 구독 간에 복제할 수 있나요?

아니요. 구독에서 지역 간에 이미지 버전을 복제하고 RBAC를 통해 다른 구독에서 사용할 수 있습니다.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Azure AD 테넌트 간에 이미지 버전을 공유할 수 있나요? 

예. RBAC를 사용하여 테넌스 전체의 개인과 공유할 수 있습니다. 그러나 규모에서 공유하려면 [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) 또는 [CLI를](../articles/virtual-machines/linux/share-images-across-tenants.md)사용하여 "Azure 테넌트 간에 갤러리 이미지 공유"를 참조하십시오.

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>대상 지역 간에 이미지 버전을 복제하려면 얼마나 걸리나요?

이미지 버전 복제 시간은 이미지의 크기와 이미지가 복제되는 지역 수에 따라 크게 달라집니다. 그러나 최상의 결과를 얻으려면 이미지를 작게 유지하고 원본 지역과 대상 지역을 가깝게 유지하는 것이 가장 좋습니다. -ReplicationStatus 플래그를 사용하여 복제의 상태를 확인할 수 있습니다.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>원본 지역과 대상 지역 사이의 차이점은 무엇인가요?

원본 지역은 이미지 버전이 생성되는 지역이며, 대상 지역은 이미지 버전의 복사본이 저장되는 지역입니다. 각 이미지 버전에 하나의 원본 지역만 사용할 수 있습니다. 또한 이미지 버전을 만들 때 원본 지역 위치를 대상 지역 중 하나로 전달하세요.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>이미지 버전을 만드는 동안 원본 영역을 지정하는 방법은 무엇인가요?

이미지 버전을 만들 때 CLI의 **--location** 태그와 PowerShell의 **-Location** 태그를 사용하여 원본 지역을 지정할 수 있습니다. 이미지 버전을 만들려면 기본 이미지로 사용 중인 관리되는 이미지가 이미지 버전을 만들려는 위치와 동일한 위치에 있는지 확인하세요. 또한 이미지 버전을 만들 때 원본 지역 위치를 대상 지역 중 하나로 전달하세요.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>각 지역에 만들 이미지 버전 복제본 수를 지정하는 방법은 무엇인가요?

각 지역에 만들 이미지 버전 복제본 수를 지정하는 방법은 두 가지가 있습니다.
 
1. 지역당 만들려는 복제본의 수를 지정하는 지역당 복제본 수. 
2. 지역당 복제본 수가 지정되지 않은 경우 지역당 기본 개수인 일반적인 복제본 수입니다. 

지역 복제본 수를 지정하려면 해당 리전에서 만들 복제본 수와 함께 위치를 전달합니다. 

각 위치에 지역당 복제본 수가 지정되지 않은 경우 기본 복제본 수가 일반적인 복제본 수로 지정됩니다. 

CLI에서 일반적인 복제본 수를 지정하려면 `az sig image-version create` 명령에 **--replica-count** 인수를 사용합니다.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>이미지 정의 및 이미지 버전과 다른 위치에 공유 이미지 갤러리를 만들 수 있습니까?

예, 구성할 수 있습니다. 그러나 모범 사례로 리소스 그룹, 공유 이미지 갤러리, 이미지 정의 및 이미지 버전을 동일한 위치에 유지하는 것이 좋습니다.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>공유 이미지 갤러리 사용 요금은 얼마인가요?

공유 이미지 갤러리 서비스 사용 요금은 없습니다(단, 이미지 버전을 저장할 때 발생하는 스토리지 요금과 원본 지역에서 대상 지역으로 이미지 버전을 복제할 때 발생하는 네트워크 송신 요금 제외).

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>공유 이미지 갤러리 및 이미지 정의 및 이미지 버전을 만드는 데 어떤 API 버전을 사용해야 합니까?

공유 이미지 갤러리, 이미지 정의 및 이미지 버전으로 작업하려면 API 버전 2018-06-01을 사용하는 것이 좋습니다. ZRS(영역 중복 저장소)는 버전 2019-03-01 이상이 필요합니다.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>이미지 버전에서 공유 VM 또는 가상 컴퓨터 스케일 세트를 만드는 데 어떤 API 버전을 사용해야 합니까?

이미지 버전을 사용한 VM 및 Virtual Machine Scale Set 배포의 경우 API 버전 2018-04-01 이상을 사용하는 것이 좋습니다.
