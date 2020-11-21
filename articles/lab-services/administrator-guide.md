---
title: Azure Lab Services - 관리자 가이드 | Microsoft Docs
description: 이 가이드는 Azure Lab Services를 사용 하 여 랩 계정을 만들고 관리 하는 관리자에 게 도움을 줍니다.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 08d2fea719ad67f666ea9da09721dc3f7ab54768
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024639"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - 관리자 가이드
대학 클라우드 리소스를 관리 하는 IT (정보 기술) 관리자는 일반적으로 학교에 대 한 랩 계정을 설정 해야 합니다. 랩 계정을 설정한 후 administrators 또는 교육자는 계정 내에 포함 된 랩을 만듭니다. 이 문서에서는 관련 된 Azure 리소스에 대 한 개략적인 개요와 해당 리소스를 만들기 위한 지침을 제공 합니다.

![랩 계정에서 Azure 리소스에 대 한 개략적인 보기의 다이어그램입니다.](./media/administrator-guide/high-level-view.png)

- 랩은 Azure Lab Services 소유 하는 Azure 구독 내에서 호스팅됩니다.
- 랩 계정, 공유 이미지 갤러리 및 이미지 버전은 구독 내에서 호스팅됩니다.
- 동일한 리소스 그룹에서 랩 계정 및 공유 이미지 갤러리를 사용할 수 있습니다. 이 다이어그램에서는 서로 다른 리소스 그룹에 있습니다.

아키텍처에 대 한 자세한 내용은 [랩 아키텍처 기본 사항](./classroom-labs-fundamentals.md)을 참조 하세요.

## <a name="subscription"></a>Subscription
대학에는 하나 이상의 Azure 구독이 있을 수 있습니다. 구독을 사용 하면 랩 계정을 포함 하 여 it 내에서 사용 되는 모든 Azure 리소스 및 서비스에 대 한 청구 및 보안을 관리할 수 있습니다.

랩 계정과 해당 구독 간의 관계는 다음과 같은 이유로 중요합니다.

- 청구는 랩 계정이 포함된 구독을 통해 보고됩니다.
- 구독의 Azure Active Directory (Azure AD) 테 넌 트에 있는 사용자에 게 Azure Lab Services에 대 한 액세스 권한을 부여할 수 있습니다. 사용자를 랩 계정 소유자 또는 참가자로 추가 하거나 랩 작성자 또는 랩 소유자로 추가할 수 있습니다.

랩 및 해당 Vm (가상 컴퓨터)은 Azure Lab Services에서 소유 하는 구독 내에서 관리 및 호스팅됩니다.

## <a name="resource-group"></a>Resource group
구독은 하나 이상의 리소스 그룹을 포함합니다. 리소스 그룹은 동일한 솔루션 내에서 함께 사용되는 Azure 리소스의 논리 그룹을 만드는 데 사용됩니다.  

랩 계정을 만들 때 랩 계정이 포함된 리소스 그룹을 구성해야 합니다. 

리소스 그룹은 [공유 이미지 갤러리](#shared-image-gallery)를 만들 때에도 필요 합니다. 랩 계정 및 공유 이미지 갤러리를 동일한 리소스 그룹이 나 별도의 두 리소스 그룹에 둘 수 있습니다. 여러 솔루션에서 이미지 갤러리를 공유 하려는 경우이 두 번째 방법을 사용 하는 것이 좋습니다.

랩 계정을 만들면 동시에 공유 이미지 갤러리를 자동으로 만들고 연결할 수 있습니다.  이 옵션을 선택하면 랩 계정 및 공유 이미지 갤러리가 별도의 리소스 그룹에 만들어집니다. [랩 계정 생성 시 공유 이미지 갤러리 구성](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) 자습서에 설명 된 단계를 따를 때이 동작을 확인할 수 있습니다. 이 문서의 시작 부분에 있는 이미지는이 구성을 사용 합니다. 

랩 계정이 나 공유 이미지 갤러리 리소스 그룹을 만든 후에는 *변경할 수 없기* 때문에 리소스 그룹의 구조를 계획 하기 위해 앞에 시간을 투자 하는 것이 좋습니다. 이러한 리소스에 대 한 리소스 그룹을 변경 해야 하는 경우 랩 계정이 나 공유 이미지 갤러리를 삭제 하 고 다시 만들어야 합니다.

## <a name="lab-account"></a>랩 계정

랩 계정은 하나 이상의 랩에 대 한 컨테이너 역할을 합니다. Azure Lab Services를 시작 하는 경우 가장 일반적인 단일 랩 계정이 있습니다. 랩 사용량이 확장 됨에 따라 나중에 더 많은 랩 계정을 만들도록 선택할 수 있습니다.

다음 목록에서는 두 개 이상의 랩 계정이 유용할 수 있는 시나리오를 보여 줍니다.

- **랩에서 여러 정책 요구 사항 관리**

    랩 계정을 설정 하는 경우 다음과 같이 랩 계정에서 *모든* 랩에 적용 되는 정책을 설정 합니다.
    - 랩에서 액세스할 수 있는 공유 리소스가 포함 된 Azure 가상 네트워크입니다. 예를 들어 가상 네트워크 내에서 공유 데이터 집합에 액세스 해야 하는 랩 집합이 있을 수 있습니다.
    - 랩에서 Vm을 만드는 데 사용할 수 있는 가상 머신 이미지입니다. 예를 들어 Linux Azure Marketplace 이미지 [에 대 한 Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 액세스 해야 하는 랩 집합이 있을 수 있습니다.

    각 랩에서 고유한 정책 요구 사항이 있는 경우 각 랩을 별도로 관리 하기 위한 별도의 랩 계정을 만드는 것이 유용할 수 있습니다.

- **각 랩 계정에 별도의 예산 할당**
  
    단일 랩 계정을 통해 모든 랩 비용을 보고 하는 대신 더 명확 하 게 할당 예산이 필요할 수 있습니다. 예를 들어, 대학의 수학 부서, 컴퓨터 과학 부서 등에 대 한 별도의 랩 계정을 만들어 부서 간에 예산을 배포할 수 있습니다.  그런 다음 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)를 사용 하 여 각 개별 랩 계정에 대 한 비용을 볼 수 있습니다.

- **활성 또는 프로덕션 랩에서 파일럿 랩 격리**
  
    활성 또는 프로덕션 랩에서 잠재적으로 영향을 받지 않고 랩 계정에 대 한 정책 변경을 파일럿 하려는 경우가 있을 수 있습니다. 이러한 유형의 시나리오에서는 파일럿을 목적으로 별도의 랩 계정을 만들면 변경 내용을 분리시킬 수 있습니다. 

## <a name="lab"></a>랩

랩에는 단일 학생에 각각 할당 된 Vm이 포함 됩니다.  일반적으로 다음을 기대할 수 있습니다.

- 각 클래스에 대 한 랩을 하나씩 포함 합니다.
- 사용 중인 각 반기, 사분기 또는 기타 교육 시스템에 대해 새로운 랩 집합을 만듭니다. 동일한 이미지를 사용 해야 하는 클래스의 경우 [공유 이미지 갤러리](#shared-image-gallery)를 사용 해야 합니다. 이러한 방식으로 실습 및 교육 기간에 걸쳐 이미지를 다시 사용할 수 있습니다.

랩을 구성 하는 방법을 결정 하는 경우 다음 사항을 고려 하세요.

- **랩 내의 모든 Vm은 게시 된 것과 동일한 이미지를 사용 하 여 배포 됩니다.**

    결과적으로 다른 랩 이미지를 동시에 게시 해야 하는 클래스가 있는 경우 각 이미지에 대해 별도의 랩을 만들어야 합니다.
  
- **사용 할당량은 랩 수준에서 설정 되며 랩 내의 모든 사용자에 게 적용 됩니다.**

    사용자에 대해 다른 할당량을 설정 하려면 별도의 랩을 만들어야 합니다. 그러나 할당량을 설정한 후에는 특정 사용자에 게 시간을 더 추가할 수 있습니다.
  
- **시작 또는 종료 일정은 랩 수준에서 설정되고 랩 내의 모든 VM에 적용됨**

    할당량 설정과 유사 하 게 사용자에 대해 다른 일정을 설정 해야 하는 경우 각 일정에 대해 별도의 랩을 만들어야 합니다.

기본적으로 각 랩에는 자체의 가상 네트워크가 있습니다.  가상 네트워크 피어 링을 사용 하도록 설정한 경우 각 랩에서는 지정 된 가상 네트워크를 사용 하는 자체 서브넷 피어 링 있습니다.

## <a name="shared-image-gallery"></a>공유 이미지 갤러리

공유 이미지 갤러리는 랩 계정에 연결되며 이미지를 저장하기 위한 중앙 리포지토리로 사용됩니다. 교육자가 랩의 템플릿 VM에서 해당 이미지를 내보내도록 선택 하면 갤러리에 이미지가 저장 됩니다. 교육자 템플릿 VM을 변경 하 고 내보낼 때마다 새 버전의 이미지가 저장 되 고 이전 버전이 유지 됩니다.

강사는 새 랩을 만들 때 공유 이미지 갤러리에서 이미지 버전을 게시할 수 있습니다. 갤러리에는 여러 버전의 이미지가 저장 되어 있지만 교사는 랩 생성 중에 최신 버전만 선택할 수 있습니다.

공유 이미지 갤러리 서비스는 몇 가지 실습으로 시작 하는 경우 즉시 필요 하지 않을 수 있는 선택적 리소스입니다. 그러나 공유 이미지 갤러리는 추가 랩을 확장할 때 도움이 되는 다양 한 이점을 제공 합니다.

- **템플릿 VM 이미지의 버전을 저장 하 고 관리할 수 있습니다.**

    공용 Azure Marketplace 갤러리에서 이미지에 대 한 사용자 지정 이미지를 만들거나 변경 (소프트웨어, 구성 등) 하는 것이 유용 합니다.  예를 들어 교육자가 다른 소프트웨어 또는 도구를 설치 하도록 요구 하는 것이 일반적입니다. 학습자는 이러한 필수 구성 요소를 수동으로 설치 하도록 요구 하는 대신 다른 버전의 템플릿 VM 이미지를 공유 이미지 갤러리로 내보낼 수 있습니다. 그런 다음 새 랩을 만들 때 이러한 이미지 버전을 사용할 수 있습니다.

- **랩에서 템플릿 VM 이미지를 공유 하 고 다시 사용할 수 있습니다.**

    새 랩을 만들 때마다 처음부터 구성할 필요가 없도록 이미지를 저장 하 고 다시 사용할 수 있습니다. 예를 들어 여러 클래스가 동일한 이미지를 사용 해야 하는 경우 한 번 만든 다음 공유 이미지 갤러리로 내보내 실습에서 공유할 수 있습니다.

- **자동 복제를 통해 이미지 가용성을 보장 합니다.**

    랩에서 공유 이미지 갤러리에 이미지를 저장 하면 [동일한 지리 내의 다른 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 자동으로 복제 됩니다. 영역에 대 한 가동 중단이 발생 한 경우 다른 지역의 이미지 복제본을 사용할 수 있기 때문에 랩에 이미지를 게시 하는 것은 영향을 받지 않습니다.  여러 복제본에서 VM을 게시하면 성능에 도움이 될 수 있습니다.

공유 이미지를 논리적으로 그룹화 하려면 다음 중 하나를 수행할 수 있습니다.

- 여러 개의 공유 이미지 갤러리를 만듭니다. 각 랩 계정은 하나의 공유 이미지 갤러리에만 연결할 수 있으므로이 옵션을 사용 하려면 여러 랩 계정도 만들어야 합니다.
- 여러 랩 계정에서 공유 하는 단일 공유 이미지 갤러리를 사용 합니다. 이 경우 각 랩 계정은 해당 계정의 랩에서 적용할 수 있는 이미지만 사용 하도록 설정할 수 있습니다.

## <a name="naming"></a>이름 지정

Azure Lab Services를 시작 하는 경우 리소스 그룹, 랩 계정, 랩 및 공유 이미지 갤러리에 대 한 명명 규칙을 설정 하는 것이 좋습니다. 사용자가 설정 하는 명명 규칙은 조직의 요구 사항에 따라 고유 하지만 다음 표에서는 일반적인 지침을 제공 합니다.

| 리소스 유형 | 역할 | 제안된 패턴 | 예 |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | 하나 이상의 랩 계정과 하나 이상의 공유 이미지 갤러리 포함 | \<organization short name\>-\<environment\>-rg<ul><li>**조직 약식 이름** 리소스 그룹이 지 원하는 조직의 이름을 식별 합니다.</li><li>**환경** 은 *파일럿* 또는 *프로덕션* 과 같은 리소스에 대 한 환경을 식별 합니다.</li><li>**Rg** 는 리소스 종류 *리소스 그룹* 을 나타냅니다.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| 랩 계정 | 하나 이상의 랩 포함 | \<organization short name\>-\<environment\>-la<ul><li>**조직 약식 이름** 리소스 그룹이 지 원하는 조직의 이름을 식별 합니다.</li><li>**환경** 은 *파일럿* 또는 *프로덕션* 과 같은 리소스에 대 한 환경을 식별 합니다.</li><li>**La** 는 리소스 유형 *랩 계정을* 나타냅니다.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| 랩 | 하나 이상의 VM 포함 |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**클래스 이름** 은 랩에서 지 원하는 클래스의 이름을 식별 합니다.</li><li>**시간 범위** 는 클래스가 제공되는 시간 범위를 식별합니다.</li>**교육자 identifier** 는 랩을 소유 하는 교육자를 식별 합니다.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 공유 이미지 갤러리 | 하나 이상의 VM 이미지 버전 포함 | \<organization short name\>갤러리 | contosouniversitylabsgallery |

다른 Azure 리소스의 이름을 지정 하는 방법에 대 한 자세한 내용은 [azure 리소스에 대 한 명명 규칙](/azure/architecture/best-practices/naming-conventions)을 참조 하세요.

## <a name="regionslocations"></a>지역\위치

Azure Lab Services 리소스를 설정 하는 경우 리소스를 호스트 하는 데이터 센터의 지역 또는 위치를 제공 해야 합니다. 다음 섹션에서는 지역 또는 위치가 랩 설정에 관련 된 각 리소스에 영향을 줄 수 있는 방법에 대해 설명 합니다.

### <a name="resource-group"></a>Resource group

지역은 리소스 그룹에 대 한 정보가 저장 되는 데이터 센터를 지정 합니다. 리소스 그룹 내에 포함 된 Azure 리소스는 부모와 다른 지역에 있을 수 있습니다.

### <a name="lab-account"></a>랩 계정

랩 계정의 위치는 리소스가 있는 지역을 나타냅니다.  

### <a name="lab"></a>랩

랩에서 존재 하는 위치는 다음과 같은 요인에 따라 달라 집니다.

  - **랩 계정이 가상 네트워크와 피어 링**
  
    동일한 지역에 있는 경우 [가상 네트워크를 사용 하 여 랩 계정을 피어](./how-to-connect-peer-virtual-network.md) 링 할 수 있습니다.  랩 계정이 가상 네트워크와 피어 링 랩 계정이 랩 계정 및 가상 네트워크와 동일한 지역에 자동으로 생성 됩니다.

    > [!NOTE]
    > 랩 계정을 가상 네트워크와 피어 링 하는 경우 랩 작성자가 **랩 위치를 선택할 수 있도록 허용** 설정을 사용 하지 않도록 설정 됩니다. 자세한 내용은 [랩 작성자가 랩의 위치를 선택하도록 허용](./allow-lab-creator-pick-lab-location.md)을 참조하세요.
    
  - **피어 링 가상 네트워크가 없고 랩 작성자 *가 랩 위치* 를 선택할 수 없습니다.**
  
    랩 계정으로 피어 링 되는 가상 네트워크가 *없고* [랩 작성자가 랩 위치 *not allowed* 를 선택할](./allow-lab-creator-pick-lab-location.md)수 없는 경우 랩을 사용 가능한 VM 용량이 있는 지역에 자동으로 생성 됩니다.  특히 Azure Lab Services는 [랩 계정과 동일한 지리적 위치 내에 있는 지역](https://azure.microsoft.com/global-infrastructure/regions)에서 가용성을 찾습니다.

  - ***피어 링 가상 네트워크가 없고 랩 작성자* 가 랩 위치를 선택할 수 있습니다.**
       
    피어 링 가상 네트워크가 *없고* 랩 작성자가 [랩 위치를 선택할 *are allowed*](./allow-lab-creator-pick-lab-location.md)수 있는 경우 랩 작성자가 선택할 수 있는 위치는 사용 가능한 용량에 따라 달라 집니다.

> [!NOTE]
> 지역에 VM 용량이 충분 한지 확인 하려면 랩을 만들 때 랩 계정을 통해 용량을 먼저 요청 하는 것이 중요 합니다.

일반적인 규칙은 리소스의 지역을 해당 사용자에 게 가장 가까운 것으로 설정 하는 것입니다. 랩을 위해이는 학생 들에 게 가장 가까운 랩을 만드는 것을 의미 합니다. 전 세계에 있는 학생 들이 있는 온라인 과정의 경우 가장 좋은 판단을 사용 하 여 중앙에 있는 랩을 만드세요. 또는 학생 지역에 따라 클래스를 여러 랩으로 분할할 수 있습니다.

## <a name="vm-sizing"></a>VM 크기 조정

관리자 또는 랩 작성자가 랩을 만들 때 교실 요구 사항에 따라 다양 한 VM 크기 중에서 선택할 수 있습니다. 계산 크기 가용성은 랩 계정이 있는 지역에 따라 다릅니다.

| 크기 | 사양 | 계열 | 권장 사용 |
| ---- | ----- | ------ | ------------- |
| 작음| <ul><li>2 &nbsp; 코어</li><li>3.5 기가바이트 (GB) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 명령줄에 가장 적합 하며, 웹 브라우저, 트래픽이 적은 웹 서버, 중소 규모의 데이터베이스를 엽니다. |
| 중간 | <ul><li>4 &nbsp; 코어</li><li>&nbsp;7gb &nbsp; RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다. |
| 중간 (중첩 된 가상화) | <ul><li>4 &nbsp; 코어</li><li>16gb &nbsp; &nbsp; RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다.
| 큰 | <ul><li>8 &nbsp; 코어</li><li>16gb &nbsp; &nbsp; RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | 더 빠른 Cpu, 더 뛰어난 로컬 디스크 성능, 대량 데이터베이스, 많은 메모리 캐시를 필요로 하는 응용 프로그램에 가장 적합 합니다.  이 크기는 중첩된 가상화도 지원합니다. |
| 큼 (중첩 된 가상화) | <ul><li>8 &nbsp; 코어</li><li>32 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | 더 빠른 Cpu, 더 뛰어난 로컬 디스크 성능, 대량 데이터베이스, 많은 메모리 캐시를 필요로 하는 응용 프로그램에 가장 적합 합니다. |
| 소형 GPU (시각화) | <ul><li>6 &nbsp; 코어</li><li>56 &nbsp; GB &nbsp; RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | OpenGL 및 DirectX와 같은 프레임 워크를 사용 하 여 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합 합니다. |
| 소형 GPU(컴퓨팅) | <ul><li>6 &nbsp; 코어</li><li>56 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |AI 및 심층 학습 등 컴퓨터를 많이 사용 하는 응용 프로그램에 가장 적합 합니다. |
| 중형 GPU (시각화) | <ul><li>12 &nbsp; 코어</li><li>112 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | OpenGL 및 DirectX와 같은 프레임 워크를 사용 하 여 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합 합니다. |

## <a name="manage-identity"></a>ID 관리

[Azure 역할 기반 액세스 제어 (RBAC)](../role-based-access-control/overview.md) 를 사용 하 여 랩 계정 및 랩에 액세스 하는 경우 다음 역할을 할당할 수 있습니다.

- 랩 계정 **소유자**

    랩 계정을 만드는 관리자에 게는 랩 계정 소유자 역할이 자동으로 할당 됩니다. 소유자 역할은 다음을 수행할 수 있습니다.
     - 랩 계정 설정을 변경 합니다.
     - 다른 관리자에 게 소유자 또는 참가자로 랩 계정에 대 한 액세스 권한을 부여 합니다.
     - 교사에 게 작성자, 소유자 또는 참가자로 랩에 대 한 액세스 권한을 부여 합니다.
     - 랩 계정에서 모든 랩을 만들고 관리 합니다.

- 랩 계정 **기여자**

    참가자 역할이 할당 된 관리자는 다음 작업을 수행할 수 있습니다.
    - 랩 계정 설정을 변경 합니다.
    - 랩 계정에서 모든 랩을 만들고 관리 합니다.

    그러나 참가자는 다른 사용자에 게 랩 계정이 나 랩에 대 한 액세스 권한을 부여할 *수 없습니다* .

- **랩 작성자**

    랩 계정 내에서 랩을 만들려면 교육자가 랩 작성자 역할의 멤버 여야 합니다.  랩을 만드는 교육자는 자동으로 랩 소유자로 추가 됩니다. 자세한 내용은 [랩 작성자 역할에 사용자 추가](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)를 참조 하세요. 

- 랩 **소유자** 또는 **참가자**
  
    랩 소유자 또는 참가자 역할의 교육자는 랩의 설정을 보고 변경할 수 있습니다. 또한 사용자는 랩 계정 판독기 역할의 멤버 여야 합니다.

    랩 소유자와 참가자 역할의 주요 차이점은 소유자만이 랩을 관리 하기 위해 다른 사용자에 게 액세스 권한을 부여할 수 있다는 것입니다. 참가자는 다른 사용자에 게 랩을 관리할 수 있는 액세스 권한을 부여할 *수 없습니다* .

- **공유 이미지 갤러리**

    공유 이미지 갤러리를 랩 계정에 연결 하면 랩 계정 소유자와 기여자 및 랩 작성자, 랩 소유자 및 랩 참가자에 게 갤러리에서 이미지를 보고 저장할 수 있는 권한이 자동으로 부여 됩니다.

역할을 할당 하는 경우 다음 팁을 따르는 데 도움이 됩니다.

   - 일반적으로 관리자만 랩 계정 소유자 또는 참가자 역할의 구성원 이어야 합니다. 랩 계정에 소유자 또는 참가자가 둘 이상 있을 수 있습니다.
   - 강사에 게 새 랩을 만들고 만든 랩을 관리할 수 있는 기능을 제공 하려면 랩 작성자 역할에만 할당 하면 됩니다.
   - 교육자에 게 특정 랩을 관리할 수 있는 기능을 제공 *하 고 새* 랩을 만들 수 있는 기능을 제공 하기 위해 관리 하려는 각 랩의 소유자 또는 참가자 역할을 할당 합니다. 예를 들어 교수 및 조교 도우미가 랩을 공동 소유 하도록 허용할 수 있습니다. 자세한 내용은 [랩에 소유자 추가](./how-to-add-user-lab-owner.md)를 참조 하세요.

## <a name="pricing"></a>가격 책정

### <a name="azure-lab-services"></a>Azure Lab Services

가격 책정에 대 한 자세한 내용은 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.


### <a name="shared-image-gallery"></a>공유 이미지 갤러리

또한 공유 이미지 갤러리를 사용 하 여 이미지 버전을 저장 하 고 관리 하려는 경우 공유 이미지 갤러리 서비스에 대 한 가격 책정을 고려해 야 합니다. 

공유 이미지 갤러리를 만들고 랩 계정에 연결하는 것은 무료입니다. 갤러리에 이미지 버전을 저장할 때까지 비용이 발생 하지 않습니다. 공유 이미지 갤러리를 사용 하는 경우의 가격은 일반적으로 매우 무시할 수 있지만 Azure Lab Services의 가격에 포함 되지 않기 때문에 계산 되는 방식을 이해 하는 것이 중요 합니다.  

#### <a name="storage-charges"></a>스토리지 요금

이미지 버전을 저장 하기 위해 공유 이미지 갤러리는 표준 HDD (하드 디스크 드라이브) 관리 디스크를 사용 합니다. 사용 되는 HDD 관리 디스크의 크기는 저장 되는 이미지 버전의 크기에 따라 달라 집니다. 가격 책정에 대 한 자세한 내용은 [Managed disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조 하세요.

#### <a name="replication-and-network-egress-charges"></a>복제 및 네트워크 송신 요금

랩 템플릿 VM을 사용 하 여 이미지 버전을 저장 하는 경우 Azure Lab Services는 먼저 원본 영역에 저장 한 다음 원본 이미지 버전을 하나 이상의 대상 지역에 자동으로 복제 합니다. 

Azure Lab Services에서 랩을 찾은 [지리 내의 모든 대상 지역](https://azure.microsoft.com/global-infrastructure/regions/) 에 원본 이미지 버전을 자동으로 복제 한다는 점에 유의 해야 합니다. 예를 들어 랩이 미국 지리에 있는 경우에는 미국 내에 있는 8 개 지역 각각에 이미지 버전이 복제 됩니다.

네트워크 송신 요금은 원본 지역에서 추가 대상 지역으로 이미지 버전이 복제될 때 발생합니다. 청구되는 금액은 처음에 이미지의 데이터가 원본 지역에서 아웃바운드로 전송될 때의 이미지 버전 크기를 기준으로 합니다.  가격 책정에 대 한 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조 하세요.

전송 요금은 [교육 솔루션](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) 고객에 게 면제 받으려면 될 수 있습니다. 자세히 알아보려면 계정 관리자에 게 문의 하세요. 

자세한 내용은 "교육용 고객을 위한 데이터 전송 프로그램은 무엇 인가요?"를 참조 하세요. [교육 기관에 대 한 프로그램](https://azure.microsoft.com/pricing/details/bandwidth/) 페이지의 FAQ 섹션에서

#### <a name="pricing-example"></a>가격 책정 예

공유 이미지 갤러리에 템플릿 VM 이미지를 저장 하는 비용의 예를 살펴보겠습니다. 다음 시나리오를 가정합니다.

- 하나의 사용자 지정 VM 이미지가 있습니다.
- 두 버전의 이미지를 저장하고 있습니다.
- 랩은 총 8 개 지역이 있는 미국에 있습니다.
- 각 이미지 버전의 크기는 32GB입니다. 따라서 HDD 관리 디스크의 가격은 월 $1.54입니다.

월별 총 비용은 다음과 같이 예상 됩니다.

* *이미지 수 &times; &times; 복제본으로 &times; 관리 되는 디스크 버전 수 가격 = 월별 총 비용*

이 예에서 비용은 다음과 같습니다.

* 1 사용자 지정 이미지 (32 g b) &times; 2 버전 &times; 8 미국 지역 &times; $1.54 = $24.64/월

> [!NOTE]
> 위의 계산은 예를 들어 목적 으로만 사용 됩니다. 공유 이미지 갤러리를 사용 하는 경우와 관련 된 저장소 비용이 포함 되며 송신 비용은 포함 *되지* 않습니다. 저장소에 대 한 실제 가격은 [Managed Disks 가격 책정](https://azure.microsoft.com/en-us/pricing/details/managed-disks/)을 참조 하세요.

#### <a name="cost-management"></a>비용 관리

랩 계정 관리자는 갤러리에서 불필요 한 이미지 버전을 정기적으로 삭제 하 여 비용을 관리 하는 것이 중요 합니다. 

이 옵션은 공유 이미지 갤러리에 있더라도 특정 지역에 대 한 복제를 특정 지역으로 삭제 하지 마세요. 공유 이미지 갤러리에 저장 된 이미지에서 Vm을 게시 하는 Azure Lab Services 기능에 부정적인 영향을 미칠 수 있습니다.

## <a name="next-steps"></a>다음 단계

랩 설정 및 관리에 대 한 자세한 내용은 다음을 참조 하세요.

- [랩 계정 설정 가이드](account-setup-guide.md)  
- [랩 설정 가이드](setup-guide.md)  
- [랩용 Cost Management](cost-management-guide.md)  
- [팀에서 Azure Lab Services 사용](lab-services-within-teams-overview.md)
