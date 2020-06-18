---
title: Azure Lab Services - 관리자 가이드 | Microsoft Docs
description: 이 가이드는 Azure Lab Services를 사용하여 랩 계정을 만들고 관리하는 관리자에게 도움을 줍니다.
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
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: ce9f8ee592c1fb2f7ac98339bbd14ce57440bc1a
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815709"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - 관리자 가이드
대학 클라우드 리소스를 관리하는 IT(정보 기술) 관리자가 일반적으로 학교의 랩 계정을 설정합니다. 랩 계정이 설정되면 관리자 또는 교육자가 랩 계정 내에 포함된 클래스룸 랩을 만듭니다. 이 문서에서는 관련 Azure 리소스 및 해당 리소스를 만들기 위한 지침에 대한 대략적인 개요를 제공합니다.

![랩 계정의 Azure 리소스 개괄적으로 보기](../media/administrator-guide/high-level-view.png)

- 클래스룸 랩은 Azure Lab Services에서 소유한 Azure 구독 내에서 호스팅됩니다.
- 랩 계정, 공유 이미지 갤러리 및 이미지 버전은 구독 내에서 호스팅됩니다.
- 동일한 리소스 그룹에서 랩 계정 및 공유 이미지 갤러리를 사용할 수 있습니다. 이 다이어그램에서는 서로 다른 리소스 그룹에 있습니다. 

## <a name="subscription"></a>Subscription
대학에는 하나 이상의 Azure 구독이 있습니다. 구독은 랩 계정을 포함하여 구독 내에서 사용되는 모든 Azure 리소스\서비스의 청구 및 보안을 관리하는 데 사용됩니다.

랩 계정과 해당 구독 간의 관계는 다음과 같은 이유로 중요합니다.

- 청구는 랩 계정이 포함된 구독을 통해 보고됩니다.
- 구독의 Azure AD(Active Directory) 테넌트에 있는 사용자에게 Azure Lab Services에 대한 액세스 권한을 부여할 수 있습니다. 사용자를 랩 계정 소유자\기여자, 클래스룸 랩 작성자 또는 클래스룸 랩 소유자로 추가할 수 있습니다.

클래스 룸 랩 및 VM(가상 머신)은 Azure Lab Services에서 소유한 구독 내에서 관리 및 호스팅됩니다.

## <a name="resource-group"></a>Resource group
구독은 하나 이상의 리소스 그룹을 포함합니다. 리소스 그룹은 동일한 솔루션 내에서 함께 사용되는 Azure 리소스의 논리 그룹을 만드는 데 사용됩니다.  

랩 계정을 만들 때 랩 계정이 포함된 리소스 그룹을 구성해야 합니다. 

리소스 그룹은 [공유 이미지 갤러리](#shared-image-gallery)를 만들 때도 필요합니다. 별도의 두 리소스 그룹에 랩 계정 및 공유 이미지 갤러리를 배치하도록 선택할 수 있습니다. 이는 서로 다른 솔루션에서 이미지 갤러리를 공유하려는 경우 일반적으로 사용됩니다. 또는 동일한 리소스 그룹에 배치하도록 선택할 수 있습니다.

랩 계정을 만들면 동시에 공유 이미지 갤러리를 자동으로 만들고 연결할 수 있습니다.  이 옵션을 선택하면 랩 계정 및 공유 이미지 갤러리가 별도의 리소스 그룹에 만들어집니다. 이 자습서에 설명된 단계를 사용하는 경우 이 동작이 표시됩니다. [랩 계정을 만들 때 공유 이미지 갤러리를 구성합니다](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). 이 문서의 맨 위에 있는 이미지 또한 이 구성을 사용합니다. 

랩 계정 또는 공유 이미지 갤러리의 리소스 그룹을 만든 후에는 변경할 수 *없으므로* 시간을 투자하여 먼저 리소스 그룹의 구조를 계획하는 것이 좋습니다. 이러한 리소스에 대한 리소스 그룹을 변경해야 하는 경우 랩 계정 및\또는 공유 이미지 갤러리를 삭제하고 다시 만들어야 합니다.

## <a name="lab-account"></a>랩 계정

랩 계정은 하나 이상의 클래스룸 랩의 컨테이너 역할을 합니다. Azure Lab Services를 시작할 때 단일 랩 계정만 있는 것이 일반적입니다. 랩 사용량이 증가함에 따라 나중에 더 많은 랩 계정을 만들도록 선택할 수 있습니다.

다음 목록에서는 두 개 이상의 랩 계정이 유용할 수 있는 시나리오를 보여 줍니다.

- **클래스룸 랩 간 여러 정책 요구 사항 관리**

    랩 계정을 설정하는 경우 다음과 같이 랩 계정에서 *모든* 클래스룸 랩에 적용되는 정책을 설정합니다.
    - 클래스룸 랩에서 액세스할 수 있는 공유 리소스가 포함된 Azure Virtual Network. 예를 들어 가상 네트워크 내에서 공유 데이터 집합에 액세스해야 하는 클래스룸 랩 집합이 있을 수 있습니다.
    - 클래스룸 랩에서 VM을 만드는 데 사용할 수 있는 VM(가상 머신) 이미지. 예를 들어 [Linux Marketplace 이미지용 Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)에 액세스해야 하는 클래스룸 랩 집합이 있을 수 있습니다.

    서로 다른 정책 요구 사항이 있는 클래스룸 랩을 보유한 경우 별도의 랩 계정을 만들어 이러한 클래스룸 랩을 별도로 관리하는 것이 좋습니다.

- **랩 계정별 별도 예산**
  
    단일 랩 계정을 통해 모든 클래스룸 랩 비용을 보고하는 대신 보다 명확하게 분리된 예산이 필요할 수 있습니다. 예를 들어 대학의 수학 부서, 컴퓨터 과학 부서 등을 위한 랩 계정을 만들어 부서별로 예산을 분리할 수 있습니다.  그런 다음 [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)를 사용하여 각 개별 랩 계정의 비용을 확인할 수 있습니다.

- **활성\프로덕션 랩에서 파일럿 랩 격리**
  
    활성\프로덕션 랩에 영향을 미칠 가능성이 없는 랩 계정에 대한 정책 변경을 파일럿하려는 경우가 있을 수 있습니다. 이러한 유형의 시나리오에서는 파일럿을 목적으로 별도의 랩 계정을 만들면 변경 내용을 분리시킬 수 있습니다. 

## <a name="classroom-lab"></a>클래스룸 랩

클래스룸 랩에서는 한 명의 학생에 각각 할당된 VM(가상 머신)이 포함되어 있습니다.  일반적으로 다음을 기대할 수 있습니다.

- 각 클래스당 하나의 클래스룸 랩을 보유합니다.
- 각 학기마다(또는 클래스가 제공되는 각 시간마다) 새로운 클래스룸 랩 집합을 만듭니다. 일반적으로 이미지 요구가 동일한 클래스의 경우 [공유 이미지 갤러리](#shared-image-gallery)를 사용하여 랩 및 학기에서 이미지를 다시 사용합니다.

클래스룸 랩을 구조화하는 방법을 결정할 때 다음 사항을 고려하세요.

- **클래스룸 랩 내의 모든 VM이 게시된 것과 동일한 이미지를 사용하여 배포됨**

    따라서 다른 랩 이미지를 동시에 게시해야 하는 클래스가 있는 경우 각각에 대해 별도의 클래스룸 랩을 만들어야 합니다.
  
- **사용 할당량이 랩 수준에서 설정되며 랩 내의 모든 사용자에게 적용됨**

    사용자에 대해 다른 할당량을 설정하려면 별도의 클래스룸 랩을 만들어야 합니다. 그러나 할당량을 설정한 후에는 특정 사용자에게 시간을 추가할 수 있습니다.
  
- **시작 또는 종료 일정은 랩 수준에서 설정되고 랩 내의 모든 VM에 적용됨**

    이전 사항과 마찬가지로 사용자에 대해 다른 일정을 설정해야 하는 경우 별도의 클래스룸 랩을 만들어야 합니다.

기본적으로 각 클래스룸 랩에는 자체 가상 네트워크가 있습니다.  Vnet 피어링을 사용하도록 설정한 경우 각 클래스룸 랩은 지정된 가상 네트워크에 피어링된 자체 서브넷이 있습니다.

## <a name="shared-image-gallery"></a>공유 이미지 갤러리

공유 이미지 갤러리는 랩 계정에 연결되며 이미지를 저장하기 위한 중앙 리포지토리로 사용됩니다. 교육자가 클래스룸 랩의 템플릿 VM(가상 머신)에서 내보내기를 선택하면 이미지가 갤러리에 저장됩니다. 교육자 템플릿 VM 및 내보내기를 변경할 때마다 이전 버전을 유지하면서 새 버전의 이미지가 저장됩니다.

강사는 새로운 클래스룸 랩을 만들 때 공유 이미지 갤러리에서 이미지 버전을 게시할 수 있습니다. 갤러리에는 여러 버전의 이미지가 저장되어 있지만 교육자는 랩 생성 중에 최신 버전만 선택할 수 있습니다.

공유 이미지 갤러리는 몇 개의 클래스룸 랩으로 시작하는 경우 즉시 필요하지 않을 수 있는 선택적 리소스입니다. 그러나 공유 이미지 갤러리를 사용하면 더 많은 클래스룸 랩으로 확장할 때 도움이 되는 많은 이점이 있습니다.

- **템플릿 VM 이미지의 버전 저장 및 관리 사용**

    사용자 지정 이미지를 만들거나 공용 Marketplace 갤러리의 이미지를 변경(소프트웨어, 구성 등)하는 것이 유용합니다.  예를 들어 교육자는 서로 다른 소프트웨어\도구를 설치해야 하는 것이 일반적입니다. 학생들에게 이러한 필수 구성 요소를 직접 설치하도록 요구하는 대신 다른 버전의 템플릿 VM 이미지를 공유 이미지 갤러리로 내보낼 수 있습니다. 이러한 이미지 버전은 새 클래스룸 랩을 만들 때 사용할 수 있습니다.
- **클래스룸 랩에서 템플릿 VM 이미지 공유\재사용 가능**

    새 클래스룸 랩을 만들 때마다 이미지를 처음부터 구성할 필요가 없도록 이미지를 저장하고 다시 사용할 수 있습니다. 예를 들어 동일한 이미지를 필요로 하는 여러 클래스를 제공하는 경우 이 이미지를 한 번만 만들고 공유 이미지 갤러리로 내보내 클래스룸 랩에서 공유할 수 있습니다.
- **복제를 통한 이미지 가용성 보장**

    클래스룸 랩에서 공유 이미지 갤러리에 저장하면 이미지가 [동일한 지리 내의 다른 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 자동으로 복제됩니다. 지역이 중단된 경우 다른 지역의 이미지 복제본을 사용할 수 있으므로 클래스 룸 랩에 이미지를 게시하는 데 영향을 미치지 않습니다.  여러 복제본에서 VM을 게시하면 성능에 도움이 될 수 있습니다.

공유 이미지를 논리적으로 그룹화하려면 다음 두 가지 옵션을 사용할 수 있습니다.

- 여러 개의 공유 이미지 갤러리를 만듭니다. 각 랩 계정은 하나의 공유 이미지 갤러리에만 연결할 수 있으므로 이 옵션을 사용하려면 여러 랩 계정을 만들어야 합니다.
- 또는 여러 랩 계정에서 공유하는 단일 공유 이미지 갤러리를 사용할 수 있습니다. 이 경우 각 랩 계정은 포함하는 클래스룸 랩에 적용되는 이미지만 사용할 수 있습니다.

## <a name="naming"></a>이름 지정

Azure Lab Services를 시작하는 경우 리소스 그룹, 랩 계정, 클래스룸 랩 및 공유 이미지 갤러리에 대한 명명 규칙을 설정하는 것이 좋습니다. 설정하는 명명 규칙은 조직의 요구 사항에 따라 고유하지만 다음 표에서는 일반적인 지침을 간략하게 설명합니다.

| 리소스 유형 | 역할 | 제안된 패턴 | 예 |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | 하나 이상의 랩 계정과 하나 이상의 공유 이미지 갤러리 포함 | \<조직 약식 이름\>-\<환경\>-rg<ul><li>**조직 약식 이름**은 리소스 그룹이 지원하는 조직의 이름을 식별합니다.</li><li>**환경**은 파일럿 또는 프로덕션과 같은 리소스의 환경을 식별합니다.</li><li>**Rg**는 리소스 종류인 리소스 그룹을 나타냅니다.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| 랩 계정 | 하나 이상의 랩 포함 | \<조직 약식 이름\>-\<환경\>-la<ul><li>**조직 약식 이름**은 리소스 그룹이 지원하는 조직의 이름을 식별합니다.</li><li>**환경**은 파일럿 또는 프로덕션과 같은 리소스의 환경을 식별합니다.</li><li>**La**는 리소스 종류인 랩 계정을 나타냅니다.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| 클래스룸 랩 | 하나 이상의 VM 포함 |\<클래스 이름\>-\<시간 범위\>-\<교육자 식별자\><ul><li>**클래스 이름**은 랩에서 지원하는 클래스의 이름을 식별합니다.</li><li>**시간 범위**는 클래스가 제공되는 시간 범위를 식별합니다.</li>**교육자 식별자**는 랩을 소유하는 교육자를 식별합니다.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 공유 이미지 갤러리 | 하나 이상의 VM 이미지 버전 포함 | \<조직 약식 이름\>gallery | contosouniversitylabsgallery |

다른 Azure 리소스의 이름을 지정하는 방법에 대한 자세한 내용은 [Azure 리소스에 대한 명명 규칙](/azure/architecture/best-practices/naming-conventions)을 참조하세요.

## <a name="regionslocations"></a>지역\위치

Azure Lab Services 리소스를 설정하는 경우 리소스를 호스팅할 데이터 센터의 지역(또는 위치)을 제공해야 합니다. 지역이 랩을 설정하는 데 관련된 각 리소스에 미치는 영향에 대한 자세한 내용은 다음과 같습니다.

### <a name="resource-group"></a>Resource group

지역은 리소스 그룹에 대한 정보가 저장되는 데이터 센터를 지정합니다. 리소스 그룹에 포함된 Azure 리소스는 부모와 다른 지역에 있을 수 있습니다.

### <a name="lab-account"></a>랩 계정

랩 계정의 위치는 이 리소스가 있는 지역을 나타냅니다.  

### <a name="classroom-lab"></a>클래스룸 랩

클래스룸 랩이 존재하는 위치는 다음과 같은 요인에 따라 달라집니다.

  - **랩 계정이 VNet(가상 네트워크)에 피어링됨**
  
    동일한 지역에 있는 경우 랩 계정을 [VNet과 피어링](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)할 수 있습니다.  랩 계정이 VNet과 피어링되면 랩 계정 및 VNet과 동일한 지역에 클래스룸 랩이 자동으로 만들어집니다.

    > [!NOTE]
    > 랩 계정이 VNet과 피어링되면 **랩 작성자가 랩 위치를 선택하도록 허용** 설정을 사용할 수 없습니다. 이 설정에 대한 추가 정보는 다음 문서에서 찾을 수 있습니다. [랩 작성자가 랩의 위치를 선택하도록 허용](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **피어링된 VNet이 없으며****** 랩 작성자가 랩 위치를 선택할 수 없음**
  
    랩 계정과 피어링된 VNet이 **없고** *그리고* [랩 작성자가 랩 위치를 선택할 수 **없으면**](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location) 클래스룸 랩이 사용 가능한 VM 용량이 있는 지역에 자동으로 만들어집니다.  특히 Azure Lab Services는 [랩 계정과 동일한 지리적 위치 내에 있는 지역](https://azure.microsoft.com/global-infrastructure/regions)에서 가용성을 찾습니다.

  - **피어링된 VNet이 없으며****** 랩 작성자가 랩 위치를 선택할 수 있음**
       
    피어링된 VNet이 **없고** [랩 작성자 랩 위치를 선택할 수 있는 경우](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location) 랩 작성자가 선택할 수 있는 위치는 사용 가능한 용량을 기준으로 합니다.

> [!NOTE]
> 지역에 충분한 VM 용량이 있는지 확인하려면 랩 계정을 통하거나 랩을 만들 때 먼저 용량을 요청하는 것이 중요합니다.

일반적인 규칙은 리소스의 지역을 해당 사용자에게 가장 가까운 지역으로 설정하는 것입니다. 이는 클래스룸 랩의 경우 학생들에게 가장 가까운 클래스룸 랩을 만드는 것을 의미합니다. 학생들이 전 세계에 있는 온라인 과정의 경우 클래스룸 랩을 중앙에 위치하도록 만들어야 합니다. 또는 학생의 지역에 따라 클래스를 여러 클래스룸 랩으로 분할합니다.

### <a name="shared-image-gallery"></a>공유 이미지 갤러리

지역은 첫 번째 이미지 버전이 대상 지역에 자동으로 복제되기 전에 저장된 원본 지역을 나타냅니다.

## <a name="vm-sizing"></a>VM 크기 조정

관리자 또는 랩 작성자가 클래스룸 랩을 만들 때 클래스룸 요구에 따라 다음 VM 크기 중에서 선택할 수 있습니다. 사용 가능한 컴퓨팅 크기는 랩 계정이 있는 지역에 따라 다릅니다.

| 크기 | 사양 | 계열 | 권장 사용 |
| ---- | ----- | ------ | ------------- |
| 작음| <ul><li>2개 코어</li><li>3.5GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 이 크기는 명령줄, 웹 브라우저 열기, 낮은 트래픽 웹 서버, 중소규모 데이터베이스에 가장 적합합니다. |
| 중간 | <ul><li>4개 코어</li><li>7GB RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 이 크기는 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다. |
| 중간(중첩된 가상화) | <ul><li>4개 코어</li><li>16GB RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | 이 크기는 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다.
| 큰 | <ul><li>8개 코어</li><li>16GB RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 이 크기는 더 빠른 CPU, 향상된 로컬 디스크 성능, 큰 데이터베이스, 큰 메모리 캐시가 필요한 애플리케이션에 가장 적합합니다.  이 크기는 중첩된 가상화도 지원합니다. |
| 대형(중첩된 가상화) | <ul><li>8개 코어</li><li>16GB RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 이 크기는 더 빠른 CPU, 향상된 로컬 디스크 성능, 큰 데이터베이스, 큰 메모리 캐시가 필요한 애플리케이션에 가장 적합합니다. |
| 소형 GPU(시각화) | <ul><li>6개 코어</li><li>56GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | 이 크기는 OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. |
| 소형 GPU(컴퓨팅) | <ul><li>6개 코어</li><li>56GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |이 크기는 인공 지능과 Deep Learning 같은 컴퓨팅을 많이 사용하는 애플리케이션에 가장 적합합니다. |
| 중간 GPU(시각화) | <ul><li>12개 코어</li><li>112GB RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 이 크기는 OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. |

## <a name="manage-identity"></a>ID 관리

[Azure의 역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)를 사용하여 랩 계정 및 클래스룸 랩에 대한 액세스를 제공하기 위해 다음 역할을 할당할 수 있습니다.

- **랩 계정 소유자**

    랩 계정을 만드는 관리자는 랩 계정의 **소유자** 역할에 자동으로 추가됩니다.  **소유자** 역할이 할당된 관리자는 다음 작업을 수행할 수 있습니다.
     - 랩 계정의 설정을 변경합니다.
     - 다른 관리자에게 랩 계정에 대한 액세스 권한을 소유자 또는 기여자로 부여합니다.
     - 교육자에게 클래스룸 랩에 대한 액세스 권한을 작성자, 소유자 또는 기여자로 부여합니다.
     - 랩 계정 내에서 모든 클래스룸 랩을 만들고 관리합니다.

- **랩 계정 기여자**

    **기여자** 역할이 할당된 관리자는 다음 작업을 수행할 수 있습니다.
    - 랩 계정의 설정을 변경합니다.
    - 랩 계정 내에서 모든 클래스룸 랩을 만들고 관리합니다.

    그러나 다른 사용자에게 랩 계정이 나 클래스룸 랩에 대한 액세스 권한을 부여할 수 *없습니다*.

- **클래스룸 랩 작성자**

    랩 계정 내에서 클래스룸 랩을 만들려면 교육자는 **랩 작성자** 역할의 멤버여야 합니다.  교육자는 클래스룸 랩을 만들 때 자동으로 랩 소유자로 추가됩니다.  [**랩 작성자** 역할에 사용자를 추가](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)하는 방법에 대한 자습서를 참조하세요. 

- **클래스룸 랩 소유자\기여자**
  
    교육자가 랩의 **소유자** 또는 **기여자** 역할의 멤버인 경우 클래스 룸 랩의 설정을 보고 변경할 수 있습니다. 또한 랩 계정의 **읽기 권한자** 역할의 멤버여야 합니다.

    랩의 **소유자**와 **기여자** 역할의 주요 차이점은 기여자는 다른 사용자에게 랩을 관리할 수 있는 액세스 권한을 부여할 수 *없다*는 것입니다. 소유자만 다른 사용자에게 랩을 관리할 수 있는 액세스 권한을 부여할 수 있습니다.

    또한 교육자는 **랩 작성자** 역할의 멤버가 아닌 경우 새 클래스룸 랩을 만들 수 *없습니다*.

- **공유 이미지 갤러리**

    공유 이미지 갤러리를 랩 계정에 연결하는 경우 랩 계정 소유자\기여자 및 랩 작성자\소유자\기여자에게 갤러리에서 이미지를 보고 저장할 수 있는 액세스 권한이 자동으로 부여됩니다.

다음은 역할을 할당하는 데 도움이 되는 팁입니다.
   - 일반적으로 관리자만 랩 계정의 **소유자** 또는 **기여자** 역할의 멤버여야 합니다. 소유자\기여자가 둘 이상 있을 수 있습니다.
   - 교육자에게 새로운 클래스룸 랩을 만들고 만든 랩을 관리할 수 있는 기능을 제공하려면 **랩 작성자** 역할에 대한 액세스 권한만 할당하면 됩니다.
   - 교육자에게 특정 클래스룸 랩을 관리할 수 있는 기능은 제공하지만 새 랩을 만드는 기능은 제공하지 *않으려면* 관리하는 각 클래스룸 랩에 대해 **소유자** 또는 **기여자** 역할에 대한 액세스 권한을 할당해야 합니다.  예를 들어 교수 및 보조 교사가 클래스룸 랩을 공동 소유하도록 허용할 수 있습니다.  [사용자를 클래스룸 랩에 대한 소유자로 추가](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)하는 방법에 대한 가이드를 참조하세요

## <a name="pricing"></a>가격 책정

### <a name="azure-lab-services"></a>Azure Lab Services

Azure Lab Services의 가격은 다음 문서에 설명되어 있습니다. [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/).

이미지 버전을 저장하고 관리하는 데 사용하려면 공유 이미지 갤러리에 대한 가격 또한 고려해야 합니다. 

### <a name="shared-image-gallery"></a>공유 이미지 갤러리

공유 이미지 갤러리를 만들고 랩 계정에 연결하는 것은 무료입니다. 갤러리에 이미지 버전을 저장할 때까지 비용이 발생하지 않습니다. 일반적으로 공유 이미지 갤러리 사용에 대한 가격은 무시해도 되는 수준이지만 Azure Lab Services 가격에 포함되지 않기 때문에 계산되는 방식을 이해하는 것이 중요합니다.  

#### <a name="storage-charges"></a>스토리지 요금

이미지 버전을 저장하기 위해 공유 이미지 갤러리는 표준 HDD 관리 디스크를 사용합니다. 사용되는 HDD 관리 디스크의 크기는 저장되는 이미지 버전의 크기에 따라 달라 집니다. 가격을 확인하려면 다음 문서를 참조하세요. [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>복제 및 네트워크 송신 요금

클래스룸 랩의 템플릿 VM(가상 머신)을 사용하여 이미지 버전을 저장하는 경우 Azure Lab Services는 먼저 이미지 버전을 원본 지역에 저장한 다음 원본 이미지 버전을 하나 이상의 대상 지역에 자동으로 복제합니다. Azure Lab Services는 원본 이미지 버전을 클래스 룸 랩이 위치한 [지리 내의 모든 대상 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 자동으로 복제한다는 점이 중요합니다. 예를 들어 클래스룸 랩이 미국 지리에 있는 경우 미국 내에 있는 8개 지역 각각에 이미지 버전이 복제됩니다.

네트워크 송신 요금은 원본 지역에서 추가 대상 지역으로 이미지 버전이 복제될 때 발생합니다. 청구되는 금액은 처음에 이미지의 데이터가 원본 지역에서 아웃바운드로 전송될 때의 이미지 버전 크기를 기준으로 합니다.  가격 책정에 대한 자세한 내용은 다음 문서를 참조하세요. [대역폭 가격 책정세부 정보](https://azure.microsoft.com/pricing/details/bandwidth/).

[교육 솔루션](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) 고객은 송신 요금 지불이 면제될 수 있습니다. 자세한 내용은 계정 관리자에게 문의하세요.  자세한 내용은 연결된 문서의 **FAQ** 섹션, 특히 "교육용 고객을 위한 데이터 전송 프로그램은 무엇이며, 자격 요건은 어떻게 되나요?"라는 질문을 참조하세요.

#### <a name="pricing-example"></a>가격 책정 예

위에서 설명한 가격 책정을 확인하기 위해 공유 이미지 갤러리에 템플릿 VM 이미지를 저장하는 예를 살펴보겠습니다. 다음 시나리오를 가정합니다.

- 하나의 사용자 지정 VM 이미지가 있습니다.
- 두 버전의 이미지를 저장하고 있습니다.
- 랩은 총 8개 지역을 포함하는 미국에 있습니다.
- 각 이미지 버전의 크기는 32GB입니다. 따라서 HDD 관리 디스크의 가격은 월 $1.54입니다.

총 비용은 다음과 같이 예상됩니다.

이미지 수 × 버전 수 × 복제본 수 × 관리 디스크 가격

이 예에서 비용은 다음과 같습니다.

1개의 사용자 지정 이미지(32GB) x 2개의 버전 x 8개의 미국 지역 x $1.54 = $24.64/월

#### <a name="cost-management"></a>비용 관리

랩 계정 관리자는 갤러리에서 불필요한 이미지 버전을 정기적으로 삭제하여 비용을 관리하는 것이 중요합니다. 

비용을 절감하는 방법으로 특정 지역에 대한 복제본을 삭제하지 않아야 합니다(이 옵션은 공유 이미지 갤러리에 있음). 복제본 변경은 공유 이미지 갤러리에 저장된 이미지에서 VM을 게시하는 Azure Lab Service의 기능에 부정적인 영향을 미칠 수 있습니다.

## <a name="next-steps"></a>다음 단계

랩 계정 및 랩을 만드는 방법에 대한 단계별 지침은 다음 자습서를 참조하세요. [설정 가이드](tutorial-setup-lab-account.md)
