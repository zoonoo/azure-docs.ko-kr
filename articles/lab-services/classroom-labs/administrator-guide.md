---
title: Azure Lab Services-관리자 가이드 | Microsoft Docs
description: 이 가이드는 Azure Lab Services를 사용 하 여 랩 계정을 만들고 관리 하는 관리자에 게 도움을 줍니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 307ca08e733417efc9496415a09a0898fe10393e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183469"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services-관리자 가이드
대학 클라우드 리소스를 관리 하는 IT (정보 기술) 관리자는 일반적으로 학교에 대 한 랩 계정을 설정 해야 합니다. 랩 계정이 설정 되 면 관리자 또는 교육자는 랩 계정 내에 포함 된 교실 랩을 만듭니다. 이 문서에서는 관련 된 Azure 리소스 및 해당 리소스를 만들기 위한 지침에 대 한 개략적인 개요를 제공 합니다.

![랩 계정에서 Azure 리소스에 대 한 상위 수준 보기](../media/administrator-guide/high-level-view.png)

- 교실 랩은 Azure Lab Services 소유의 Azure 구독 내에서 호스팅됩니다.
- 랩 계정, 공유 이미지 갤러리 및 이미지 버전은 구독 내에서 호스팅됩니다.
- 동일한 리소스 그룹에서 랩 계정 및 공유 이미지 갤러리를 사용할 수 있습니다. 이 다이어그램에서는 서로 다른 리소스 그룹에 있습니다. 

## <a name="subscription"></a>Subscription
대학에는 하나 이상의 Azure 구독이 있습니다. 구독은 랩 계정을 포함 하 여 it 내에서 사용 되는 모든 Azure resources\services의 청구 및 보안을 관리 하는 데 사용 됩니다.

랩 계정과 해당 구독 간의 관계는 다음과 같은 경우에 중요 합니다.

- 요금은 랩 계정이 포함 된 구독을 통해 보고 됩니다.
- 구독의 AD (Azure Active Directory) 테 넌 트에 있는 사용자에 게 Azure Lab Services에 대 한 액세스 권한을 부여할 수 있습니다. 사용자를 랩 계정 소유자 \ 기여자, 교실 랩 작성자 또는 교실 랩 소유자로 추가할 수 있습니다.

클래스 룸 랩 및 해당 Vm (가상 머신)은 Azure Lab Services 소유 하는 구독 내에서 관리 및 호스팅됩니다.

## <a name="resource-group"></a>Resource group
구독은 하나 이상의 리소스 그룹을 포함 합니다. 리소스 그룹은 동일한 솔루션 내에서 함께 사용 되는 Azure 리소스의 논리적 그룹을 만드는 데 사용 됩니다.  

랩 계정을 만들 때 랩 계정이 포함 된 리소스 그룹을 구성 해야 합니다. 

리소스 그룹은 [공유 이미지 갤러리](#shared-image-gallery)를 만들 때에도 필요 합니다. 별도의 두 리소스 그룹에 랩 계정 및 공유 이미지 갤러리를 배치 하도록 선택할 수 있습니다 .이는 서로 다른 솔루션에서 이미지 갤러리를 공유 하려는 경우 일반적입니다. 또는 동일한 리소스 그룹에 배치 하도록 선택할 수도 있습니다.

랩 계정을 만들 때 공유 이미지 갤러리를 동시에 자동으로 만들고 연결할 수 있습니다.  이 옵션을 선택 하면 랩 계정 및 공유 이미지 갤러리가 별도의 리소스 그룹에 만들어집니다. 이 자습서: [랩 계정 생성 시 공유 이미지 갤러리 구성](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation)에서 설명 하는 단계를 사용 하는 경우이 동작을 확인할 수 있습니다. 이 문서의 맨 위에 있는 이미지는이 구성도 사용 합니다. 

랩 계정의 또는 공유 이미지 갤러리의 리소스 그룹을 만든 후에는 *변경할 수 없기* 때문에 리소스 그룹의 구조를 계획 하기 위해 앞으로 시간을 투자 하는 것이 좋습니다. 이러한 리소스에 대 한 리소스 그룹을 변경 해야 하는 경우 랩 계정 및/또는 공유 이미지 갤러리를 삭제 하 고 다시 만들어야 합니다.

## <a name="lab-account"></a>랩 계정

랩 계정은 하나 이상의 교실 랩에 대 한 컨테이너 역할을 합니다. Azure Lab Services를 시작 하는 경우 단일 랩 계정만 있는 것이 일반적입니다. 랩 사용량이 규모에 따라 나중에 더 많은 랩 계정을 만들도록 선택할 수 있습니다.

다음 목록에서는 두 개 이상의 랩 계정이 유용할 수 있는 시나리오를 보여 줍니다.

- **교실 랩에서 여러 정책 요구 사항 관리**

    랩 계정을 설정할 때 다음과 같은 랩 계정에서 *모든* 교실 랩에 적용 되는 정책을 설정 합니다.
    - 교실 랩에서 액세스할 수 있는 공유 리소스가 포함 된 Azure 가상 네트워크입니다. 예를 들어 가상 네트워크 내에서 공유 데이터 집합에 액세스 해야 하는 교실 labs 집합이 있을 수 있습니다.
    - 교실 labs에서 Vm을 만드는 데 사용할 수 있는 VM (가상 머신) 이미지입니다. 예를 들어 Linux 마켓플레이스 이미지 [에 대 한 Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 액세스 해야 하는 교실 labs 집합이 있을 수 있습니다.

    서로 고유한 정책 요구 사항이 있는 교실 랩을 보유 한 경우 이러한 교실 랩을 별도로 관리 하는 별도의 랩 계정을 만드는 것이 유용할 수 있습니다.

- **랩 계정 별로 예산 분리**
  
    단일 랩 계정을 통해 모든 교실 랩 비용을 보고 하는 대신 보다 명확 하 게 분리 된 예산이 필요할 수 있습니다. 예를 들어 대학의 수학 부서, 컴퓨터 과학 부서 등에 대 한 랩 계정을 만들어 여러 부서에서 예산을 구분할 수 있습니다.  그런 다음 [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)를 사용 하 여 각 개별 랩 계정에 대 한 비용을 볼 수 있습니다.

- **Active\production labs에서 파일럿 랩 격리**
  
    활성 랩에 영향을 미칠 가능성이 없는 랩 계정에 대 한 정책 변경을 파일럿 하려는 경우가 있을 수 있습니다. 이러한 유형의 시나리오에서는 파일럿을 위해 별도의 랩 계정을 만들면 변경 내용을 격리할 수 있습니다. 

## <a name="classroom-lab"></a>교실 랩

교실 랩에서는 단일 학생에 각각 할당 된 Vm (가상 머신)이 포함 되어 있습니다.  일반적으로 다음을 예측할 수 있습니다.

- 각 클래스에 대해 하나의 교실 랩을 보유 합니다.
- 각 반기 (또는 클래스가 제공 되는 각 시간 프레임)에 대해 새로운 교실 랩 집합을 만듭니다. 일반적으로 이미지 요구가 동일한 클래스의 경우 [공유 이미지 갤러리](#shared-image-gallery) 를 사용 하 여 labs 및 반기에서 이미지를 다시 사용 해야 합니다.

클래스 룸 랩을 구조화 하는 방법을 결정할 때 다음 사항을 고려 하십시오.

- **교실 랩 내의 모든 Vm은 게시 된 것과 동일한 이미지를 사용 하 여 배포 됩니다.**

    결과적으로 다른 랩 이미지를 동시에 게시 해야 하는 클래스가 있는 경우 각각에 대해 별도의 교실 랩을 만들어야 합니다.
  
- **사용 할당량은 랩 수준에서 설정 되며 랩 내의 모든 사용자에 게 적용 됩니다.**

    사용자에 대해 다른 할당량을 설정 하려면 별도의 교실 랩을 만들어야 합니다. 그러나 할당량을 설정한 후에는 특정 사용자에 게 시간을 더 추가할 수 있습니다.
  
- **시작 또는 종료 일정은 랩 수준에서 설정 되 고 랩 내의 모든 Vm에 적용 됩니다.**

    이전 지점과 마찬가지로 사용자에 대해 다른 일정을 설정 해야 하는 경우 별도의 교실 랩을 만들어야 합니다.

기본적으로 각 교실 랩에서는 자체 가상 네트워크가 있습니다.  Vnet 피어 링을 사용 하도록 설정한 경우 각 교실 랩에서는 지정 된 가상 네트워크에 대 한 자체 서브넷 피어 링 있습니다.

## <a name="shared-image-gallery"></a>공유 이미지 갤러리

공유 이미지 갤러리는 랩 계정에 연결 되 고 이미지를 저장 하기 위한 중앙 리포지토리로 사용 됩니다. 교육자가 교실 랩의 템플릿 가상 머신 (VM)에서 내보내기를 선택 하면 갤러리에 이미지가 저장 됩니다. 교육자 템플릿 VM 및 내보내기를 변경할 때마다 이전 버전을 유지 하면서 새 버전의 이미지가 저장 됩니다.

강사는 새로운 교실 랩을 만들 때 공유 이미지 갤러리에서 이미지 버전을 게시할 수 있습니다. 갤러리에는 여러 버전의 이미지가 저장 되어 있지만 교사는 랩 생성 중에 최신 버전만 선택할 수 있습니다.

공유 이미지 갤러리는 일부 교실 labs로 시작 하는 경우 즉시 필요 하지 않을 수 있는 선택적 리소스입니다. 그러나 공유 이미지 갤러리를 사용 하면 더 많은 교실 랩을 사용 하도록 확장할 때 도움이 되는 많은 이점이 있습니다.

- **템플릿 VM 이미지의 버전을 저장 하 고 관리할 수 있습니다.**

    사용자 지정 이미지를 만들거나 공용 Marketplace 갤러리에서 이미지로 변경 (소프트웨어, 구성 등) 하는 것이 유용 합니다.  예를 들어, 교육자는 서로 다른 기타 \tools를 설치 해야 하는 경우가 많습니다. 학습자는 이러한 필수 구성 요소를 수동으로 설치 하도록 요구 하는 대신 다른 버전의 템플릿 VM 이미지를 공유 이미지 갤러리로 내보낼 수 있습니다. 이러한 이미지 버전은 새 교실 랩을 만들 때 사용할 수 있습니다.
- **교실 랩에서 템플릿 VM 이미지를 sharing\reuse 사용 하도록 설정 합니다.**

    새 교실 랩을 만들 때마다 이미지를 처음부터 구성할 필요가 없도록 이미지를 저장 하 고 다시 사용할 수 있습니다. 예를 들어 동일한 이미지를 필요로 하는 여러 클래스를 제공 하는 경우이 이미지를 한 번만 만들고 공유 이미지 갤러리로 내보내 교실 실습에서 공유할 수 있습니다.
- **복제를 통해 이미지 가용성을 보장 합니다.**

    교실 랩에서 공유 이미지 갤러리에 저장 하면 이미지가 [동일한 지리 내의 다른 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 자동으로 복제 됩니다. 영역에 대 한 중단이 발생 한 경우 다른 지역의 이미지 복제본을 사용할 수 있으므로 클래스 룸 랩에 이미지를 게시 하는 것은 영향을 받지 않습니다.  여러 복제본에서 Vm을 게시 하면 성능을 높일 수도 있습니다.

공유 이미지를 논리적으로 그룹화 하려면 다음 두 가지 옵션을 사용할 수 있습니다.

- 공유 이미지 갤러리를 여러 개 만듭니다. 각 랩 계정은 하나의 공유 이미지 갤러리에만 연결할 수 있으므로이 옵션을 선택 하면 여러 랩 계정도 만들어야 합니다.
- 또는 여러 랩 계정에서 공유 하는 단일 공유 이미지 갤러리를 사용할 수 있습니다. 이 경우 각 랩 계정은 포함 하는 교실 labs에 적용 되는 이미지만 사용할 수 있습니다.

## <a name="naming"></a>이름 지정

Azure Lab Services를 시작 하는 경우 리소스 그룹, 랩 계정, 교실 labs 및 공유 이미지 갤러리에 대 한 명명 규칙을 설정 하는 것이 좋습니다. 사용자가 설정 하는 명명 규칙은 조직의 요구 사항에 따라 고유 하지만 다음 표에서는 일반적인 지침을 간략하게 설명 합니다.

| 리소스 유형 | 역할 | 제안된 패턴 | 예 |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | 하나 이상의 랩 계정 및 하나 이상의 공유 이미지 갤러리를 포함 합니다. | \<조직 단기 이름\>-\<환경\>-rg<ul><li>**조직 약식 이름** 리소스 그룹이 지 원하는 조직의 이름을 식별 합니다.</li><li>**환경** 에서 파일럿 또는 프로덕션과 같은 리소스에 대 한 환경을 식별 합니다.</li><li>**Rg** 는 리소스 종류: 리소스 그룹을 나타냅니다.</li></ul> | a n y i n y-rg<br/>a n y i n a d-모의 실험-rg<br/>a n y i n a d-prod-rg |
| 랩 계정 | 하나 이상의 랩 포함 | \<조직 단기 이름\>-\<환경\>-la<ul><li>**조직 약식 이름** 리소스 그룹이 지 원하는 조직의 이름을 식별 합니다.</li><li>**환경** 에서 파일럿 또는 프로덕션과 같은 리소스에 대 한 환경을 식별 합니다.</li><li>**La** 는 리소스 유형: 랩 계정을 나타냅니다.</li></ul> | a n y i n y-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-la<br/>sciencedeptlabs-prod-la |
| 교실 랩 | 하나 이상의 Vm 포함 |\<클래스 이름\>-\<\>기간-교육자 identifier\<\><ul><li>**클래스 이름** 은 랩에서 지 원하는 클래스의 이름을 식별 합니다.</li><li>**시간대** 는 클래스가 제공 되는 기간을 식별 합니다.</li>**교육 식별자** 는 랩을 소유 하는 교육자을 식별 합니다.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 공유 이미지 갤러리 | 하나 이상의 VM 이미지 버전이 포함 되어 있습니다. | \<조직 약식 이름\>갤러리 | 이 갤러리에 대 한 |

다른 Azure 리소스의 이름을 지정 하는 방법에 대 한 자세한 내용은 [azure 리소스에 대 한 명명 규칙](/azure/architecture/best-practices/naming-conventions)을 참조 하세요.

## <a name="regionslocations"></a>지역 \ 위치

Azure Lab Services 리소스를 설정 하는 경우 리소스를 호스팅할 데이터 센터의 지역 (또는 위치)을 제공 해야 합니다. 지역이 랩을 설정 하는 것과 관련 된 각 자원에 영향을 주는 방법에 대 한 자세한 내용은 다음과 같습니다.

### <a name="resource-group"></a>Resource group

지역은 리소스 그룹에 대 한 정보가 저장 되는 데이터 센터를 지정 합니다. 리소스 그룹 내에 포함 된 Azure 리소스는 부모와 다른 지역에 있을 수 있습니다.

### <a name="lab-account"></a>랩 계정

랩 계정의 위치는이 리소스가 있는 지역을 나타냅니다.  

### <a name="classroom-lab"></a>교실 랩

교실 랩을 존재 하는 위치는 다음과 같은 요인에 따라 달라 집니다.

  - **랩 계정이 VNet (가상 네트워크)에 피어 링**
  
    동일한 지역에 있는 경우 [VNet을 사용 하](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) 여 랩 계정을 피어 링 수 있습니다.  랩 계정이 VNet을 사용 하 여 피어 링 면 랩 계정과 VNet 모두와 동일한 지역에 자동으로 교실 랩을 만듭니다.

    > [!NOTE]
    > 랩 계정이 VNet을 사용 하 여 피어 링 면 랩 **작성자가 랩 위치를 선택 하도록 허용** 하는 설정을 사용할 수 없습니다. 이 설정에 대 한 자세한 내용은 [랩 작성자가 랩 위치를 선택할 수 있도록 허용](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)문서에서 찾을 수 있습니다.
    
  - **피어 링가 없고 랩 작성자 ***가 랩 위치*** 를 선택할 수 없습니다.**
  
    랩 *계정을 사용 하는* VNet 피어 링 **없고** [랩 작성자가 랩 위치를 **not** 선택할](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)수 없는 경우 사용 가능한 VM 용량이 있는 지역에 교실 labs가 자동으로 만들어집니다.  특히 Azure Lab Services는 [랩 계정과 동일한 지리 내](https://azure.microsoft.com/global-infrastructure/regions)에 있는 지역에서 가용성을 찾습니다.

  - *****피어 링가 없고 랩 작성자*** 가 랩 위치를 선택할 수 있는 VNet이 없습니다.**
       
    VNet 피어 링 **없고** 랩 [작성자가 랩 위치를 선택할](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)수 있는 경우 랩 작성자가 선택할 수 있는 위치는 사용 가능한 용량에 따라 달라 집니다.

> [!NOTE]
> 지역에 충분 한 VM 용량이 있는지 확인 하려면 랩 계정을 통해 또는 랩을 만들 때 먼저 용량을 요청 하는 것이 중요 합니다.

일반적인 규칙은 리소스의 지역을 해당 사용자에 게 가장 가까운 것으로 설정 하는 것입니다. 이는 교실 랩의 경우 학생 들에 게 가장 가까운 교실 랩을 만드는 것을 의미 합니다. 학생 들이 전 세계에 있는 온라인 과정의 경우 최고의 판단을 사용 하 여 중앙에 있는 교실 랩을 만들어야 합니다. 또는 학생의 지역에 따라 클래스를 여러 교실 랩으로 분할 합니다.

### <a name="shared-image-gallery"></a>공유 이미지 갤러리

지역은 대상 지역에 자동으로 복제 되기 전에 첫 번째 이미지 버전이 저장 된 원본 지역을 나타냅니다.

## <a name="vm-sizing"></a>VM 크기 조정

관리자 또는 랩 작성자가 교실 랩을 만들 때 사용자의 교실 요구에 따라 다음 VM 크기 중에서 선택할 수 있습니다. 사용 가능한 계산 크기는 랩 계정이 있는 지역에 따라 다릅니다.

| Size | 사양 | 계열 | 권장 사용 |
| ---- | ----- | ------ | ------------- |
| 작음| <ul><li>2개 코어</li><li>3.5 GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 이 크기는 명령줄, 웹 브라우저 열기, 트래픽이 적은 웹 서버, 중소 규모의 데이터베이스에 가장 적합 합니다. |
| 보통 | <ul><li>4 코어</li><li>7GB RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다. |
| 중간 (중첩 된 가상화) | <ul><li>4 코어</li><li>16GB RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다.  이 크기는 중첩 된 가상화도 지원 합니다. |
| 큰 | <ul><li>8개 코어</li><li>32 GB RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 이 크기는 더 빠른 Cpu, 더 뛰어난 로컬 디스크 성능, 큰 데이터베이스, 큰 메모리 캐시를 필요로 하는 응용 프로그램에 가장 적합 합니다.  이 크기는 중첩 된 가상화도 지원 합니다. |
| 소형 GPU (시각화) | <ul><li>6 코어</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | 이 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합 합니다. |
| 소형 GPU (계산) | <ul><li>6 코어</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |이 크기는 인공 지능 및 심층 학습 같은 컴퓨터 집약적 응용 프로그램에 가장 적합 합니다. |
| 중형 GPU (시각화) | <ul><li>12 코어</li><li>112 GB RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | 이 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합 합니다. |

## <a name="manage-identity"></a>Id 관리

[Azure의 역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)를 사용 하 여 랩 계정 및 교실 랩에 대 한 액세스를 제공 하기 위해 다음 역할을 할당할 수 있습니다.

- **랩 계정 소유자**

    랩 계정을 만드는 관리자는 랩 계정의 **소유자** 역할에 자동으로 추가 됩니다.  **소유자** 역할이 할당 된 관리자는 다음 작업을 수행할 수 있습니다.
     - 랩 계정의 설정을 변경 합니다.
     - 다른 관리자에 게 랩 계정에 대 한 액세스 권한을 소유자 또는 참가자로 지정 합니다.
     - 교사 실습실에 게 작성자, 소유자 또는 참가자로 교육을 받을 수 있는 권한을 부여 합니다.
     - 랩 계정 내에서 모든 교실 랩을 만들고 관리 합니다.

- **랩 계정 기여자**

    **참가자** 역할이 할당 된 관리자는 다음 작업을 수행할 수 있습니다.
    - 랩 계정의 설정을 변경 합니다.
    - 랩 계정 내에서 모든 교실 랩을 만들고 관리 합니다.

    그러나 다른 사용자에 게 랩 계정이 나 교실 랩에 대 한 액세스 권한을 부여할 *수는 없습니다* .

- **교실 랩 작성자**

    랩 계정 내에서 교실 랩을 만들려면 교육자가 **랩 작성자** 역할의 멤버 여야 합니다.  교육자는 교실 랩을 만들 때 자동으로 랩 소유자로 추가 됩니다.  [ **랩 작성자** 역할에 사용자를 추가](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)하는 방법에 대 한 자습서를 참조 하세요. 

- **교실 랩 소유자 \ 기여자**
  
    교육자는 랩의 **소유자** 또는 **참가자** 역할의 구성원 인 경우 교실 랩의 설정을 보고 변경할 수 있습니다. 또한 랩 계정의 **읽기** 역할의 멤버 여야 합니다.

    랩에서 **소유자** 와 **참가자** 역할의 주요 차이점은 기여자는 다른 사용자에 게 랩 전용 소유자를 관리 하기 위해 다른 사용자에 게 액세스 권한을 *부여할 수 없다는* 것입니다.

    또한 교육자는 **랩 작성자** 역할의 멤버가 아닌 경우 새 교실 랩을 만들 *수 없습니다* .

- **공유 이미지 갤러리**

    공유 이미지 갤러리를 랩 계정에 연결 하는 경우 랩 계정 소유자 \ 기여자 및 랩 creatorss\ss\sgss\somss\som\ 참가자에 게 갤러리에서 이미지를 보고 저장할 수 있는 권한이 자동으로 부여 됩니다.

역할을 할당 하는 데 도움이 되는 몇 가지 팁은 다음과 같습니다.
   - 일반적으로 관리자만 랩 계정의 **소유자** 또는 **참가자** 역할의 구성원 이어야 합니다. owner\contributor.가 둘 이상 있을 수 있습니다.
   - 교육자에 게 새로운 교실 랩을 만들고 만든 랩을 관리할 수 있는 기능을 제공 합니다. **랩 작성자** 역할에 액세스 권한을 할당 하기만 하면 됩니다.
   - 교육자에 게 특정 교실 랩을 관리할 수 있는 기능을 제공 하기 위해 새 랩을 만들 수는 *없습니다* . 관리 하는 각 교실 labs에 대 한 **소유자** 또는 **참가자** 역할에 액세스 권한을 할당 해야 합니다.  예를 들어 교수 및 학습 도우미가 교실 랩을 공동 소유 하도록 허용할 수 있습니다.  [사용자를 교실 랩에 소유자로 추가](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)하는 방법에 대 한 가이드를 참조 하세요.

## <a name="pricing"></a>가격 책정

### <a name="azure-lab-services"></a>Azure Lab Services

Azure Lab Services의 가격은 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)문서에 설명 되어 있습니다.

또한 이미지 버전을 저장 하 고 관리 하는 데 사용할 계획인 경우 공유 이미지 갤러리에 대 한 가격 책정을 고려해 야 합니다. 

### <a name="shared-image-gallery"></a>공유 이미지 갤러리

공유 이미지 갤러리를 만들고 랩 계정에 연결 하는 것은 무료입니다. 갤러리에 이미지 버전을 저장할 때까지 비용은 발생 하지 않습니다. 일반적으로 공유 이미지 갤러리 사용에 대 한 가격은 매우 무시할 수 있지만 Azure Lab Services 가격에 포함 되지 않기 때문에 계산 되는 방식을 이해 하는 것이 중요 합니다.  

#### <a name="storage-charges"></a>저장소 요금

이미지 버전을 저장 하기 위해 공유 이미지 갤러리는 표준 HDD 관리 디스크를 사용 합니다. 사용 되는 HDD 관리 디스크의 크기는 저장 되는 이미지 버전의 크기에 따라 달라 집니다. 가격 책정: [Managed disks 가격](https://azure.microsoft.com/pricing/details/managed-disks/)책정을 보려면 다음 문서를 참조 하세요.

#### <a name="replication-and-network-egress-charges"></a>복제 및 네트워크 송신 요금

교실 랩의 템플릿 VM (가상 머신)을 사용 하 여 이미지 버전을 저장 하는 경우 Azure Lab Services는 먼저 원본 지역에 저장 한 다음 원본 이미지 버전을 하나 이상의 대상 지역에 자동으로 복제 합니다. Azure Lab Services는 교실 랩을 있는 [지리 내의](https://azure.microsoft.com/global-infrastructure/regions/) 모든 대상 지역에 원본 이미지 버전을 자동으로 복제 한다는 점에 유의 해야 합니다. 예를 들어, 교실 랩이 미국 지리에 있는 경우 미국 내에 있는 8 개 지역 각각에 이미지 버전이 복제 됩니다.

네트워크 송신 요금은 원본 지역에서 추가 대상 지역으로 이미지 버전이 복제 될 때 발생 합니다. 청구 되는 금액은 처음에는 이미지의 데이터가 원본 영역에서 아웃 바운드로 전송 될 때 이미지 버전의 크기를 기준으로 합니다.  가격 책정에 대 한 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)문서를 참조 하세요.

[교육 솔루션](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) 고객은 면제 받으려면 수신 요금을 지불 하지 못할 수 있습니다. 자세한 내용은 계정 관리자에 게 문의 하세요.  자세한 내용은 연결 된 문서의 **FAQ** 섹션을 참조 하세요. 특히 "교육용 고객을 위한 데이터 전송 프로그램은 무엇 인가요?" 라는 질문을 참조 하세요.

#### <a name="pricing-example"></a>가격 책정 예

위에서 설명한 가격 책정을 요약 하자면, 공유 이미지 갤러리에 템플릿 VM 이미지를 저장 하는 예를 살펴보겠습니다. 다음 시나리오를 가정 합니다.

- 사용자 지정 VM 이미지가 하나 있습니다.
- 두 버전의 이미지를 저장 하 고 있습니다.
- 랩은 총 8 개 지역을 포함 하는 미국에 있습니다.
- 각 이미지 버전의 크기는 32입니다. 따라서 HDD로 관리 되는 디스크 가격은 월 $1.54입니다.

총 비용은 다음과 같이 예상 됩니다.

이미지 수 × 개 버전 번호 × 복제본 수 × 관리 디스크 가격

이 예제에서 비용은 다음과 같습니다.

1 사용자 지정 이미지 (32 g b) x 2 버전 x 8 미국 지역 x $1.54 = $24.64/월

#### <a name="cost-management"></a>비용 관리

랩 계정 관리자는 갤러리에서 불필요 한 이미지 버전을 정기적으로 삭제 하 여 비용을 관리 하는 것이 중요 합니다. 

비용을 절감 하는 방법으로 특정 지역에 대 한 복제를 삭제 하지 않아야 합니다 (이 옵션은 공유 이미지 갤러리에 있음). 공유 이미지 갤러리에 저장 된 이미지에서 Vm을 게시 하는 Azure Lab Service의 기능에 부정적인 영향을 미칠 수 있습니다.

## <a name="next-steps"></a>다음 단계

랩 계정 및 랩을 만드는 방법에 대 한 단계별 지침은 자습서를 참조 하십시오. [설정 가이드](tutorial-setup-lab-account.md)
