---
title: Azure VMware 솔루션에 수평 배포
description: Azure VMware 솔루션에 VMware 수평을 배포 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 2cf6fc5cb7662188650365cb019774d6c778d405
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684878"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Azure VMware 솔루션에 수평 배포 

>[!NOTE]
>이 문서에서는 이전의 수평 7 이라고 하는 VMware 수평 제품에 중점을 둔 문서입니다. 수평은 몇 가지 공유 구성 요소가 있지만 Azure의 수평 클라우드와는 다른 솔루션입니다. Azure VMware 솔루션의 주요 이점에는 보다 간단한 크기 조정 방법과 VMware Cloud Foundation 관리를 Azure Portal 통합 하는 작업이 포함 됩니다.

가상 데스크톱 및 응용 프로그램 플랫폼인 [VMware 수평](https://www.vmware.com/products/horizon.html)®은 데이터 센터에서 실행 되며 간단 하 고 중앙 집중식 관리를 제공 합니다. 어디서 든 모든 장치에서 가상 데스크톱 및 응용 프로그램을 제공 합니다. 수평을 사용 하면 Windows 및 Linux 가상 데스크톱, RDS (원격 데스크톱 서버) 호스트 된 응용 프로그램, 데스크톱 및 물리적 컴퓨터에 대 한 연결을 만들고 broker 할 수 있습니다.

여기서는 Azure VMware 솔루션에 대 한 수평 배포에 중점을 둡니다. VMware 수평에 대 한 일반 정보는 수평 프로덕션 설명서를 참조 하세요.

* [VMware 수평 이란?](https://www.vmware.com/products/horizon.html)

* [VMware 수평에 대해 자세히 알아보기](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [수평 참조 아키텍처](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Azure VMware 솔루션에 대 한 간단한 소개를 통해 Azure 플랫폼에는 두 개의 VDI (가상 데스크톱 인프라) 솔루션이 있습니다. 다음 다이어그램에는 높은 수준의 주요 차이점이 요약 되어 있습니다.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Azure의 Azure VMware 솔루션 및 수평 클라우드의 수평" border="false":::

수평 8 릴리스 라인의 수평 2006 이상 버전은 온-프레미스 배포와 Azure VMware 솔루션 배포를 모두 지원 합니다. 온-프레미스에서 지원 되지만 Azure VMware 솔루션에서는 지원 되지 않는 몇 가지 수평 기능이 있습니다. 또한 수평 에코 시스템의 추가 제품이 지원 됩니다. 자세한 내용은 [기능 패리티 및 상호 운용성](https://kb.vmware.com/s/article/80850)을 참조 하세요.

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>하이브리드 클라우드에서 수평 배포

수평 클라우드 Pod 아키텍처 (CPA)를 사용 하 여 온-프레미스 및 Azure 데이터 센터를 상호 연결 하는 경우 하이브리드 클라우드 환경에서 수평을 배포할 수 있습니다. CPA는 배포를 확장 하 고, 하이브리드 클라우드를 구축 하 고, 비즈니스 연속성 및 재해 복구를 위한 중복성을 제공 합니다.  자세한 내용은 [기존 수평 7 환경 확장](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)을 참조 하세요.

>[!IMPORTANT]
>CPA은 스트레치 된 배포가 아닙니다. 각 수평 pod는 고유 합니다. 각 개별 pod에 속하는 모든 연결 서버는 단일 위치에 배치 하 고 네트워크 관점에서 동일한 브로드캐스트 도메인에서 실행 해야 합니다.

온-프레미스 또는 개인 데이터 센터와 같이 Azure VMware 솔루션 사설 클라우드에 수평을 배포할 수 있습니다. 다음 섹션에서는 온-프레미스와 Azure VMware 솔루션의 배포에 대 한 주요 차이점을 설명 합니다.

Azure 사설 클라우드는 일반적으로 수평 설명서에서 사용 되는 용어 인 VMware SDDC와 동일 합니다. 이 문서의 나머지 부분에서는 Azure 사설 클라우드 및 VMware SDDC 교환 이라는 용어를 사용 합니다.

수평 클라우드 커넥터는 구독 라이선스를 관리 하기 위해 Azure VMware 솔루션의 수평에 필요 합니다. 클라우드 커넥터는 수평 연결 서버와 함께 Azure Virtual Network에 배포할 수 있습니다.

>[!IMPORTANT]
>Azure VMware 솔루션에 대 한 수평 제어 평면 지원은 아직 사용할 수 없습니다. 수평 클라우드 커넥터의 VHD 버전을 다운로드 해야 합니다.

## <a name="vcenter-cloud-admin-role"></a>vCenter 클라우드 관리자 역할

Azure VMware 솔루션은 SDDC 서비스이 고 azure는 azure VMware 솔루션에서 SDDC의 수명 주기를 관리 하므로 Azure VMware 솔루션의 vCenter 권한 모델은 설계로 제한 됩니다.

고객은 제한 된 vCenter 권한 집합이 있는 클라우드 관리자 역할을 사용 해야 합니다. 수평 제품은 Azure VMware 솔루션에서 클라우드 관리자 역할을 사용 하도록 수정 되었습니다. 특히 다음과 같은 작업을 수행 합니다.

* 즉시 복제 프로 비전이 Azure VMware 솔루션에서 실행 되도록 수정 되었습니다.

* 특정 vSAN 정책 (VMware_Horizon)이 수평으로 작업 하기 위해 Azure VMware 솔루션에 생성 되었습니다 .이 작업은 사용 가능 하 고 수평으로 배포 된 SDDCs에서 사용 되어야 합니다.

* Azure VMware 솔루션에서 실행 되는 경우 View Storage Accelerator 라고도 하는 vSphere CBRC (콘텐츠 기반 읽기 캐시)를 사용할 수 없습니다.

>[!IMPORTANT]
>CBRC를 다시 설정 하지 않아야 합니다.

>[!NOTE]
>Azure VMware 솔루션은 수평 8 분기에서 수평 2006 (즉, 수평 8) 이상을 배포 하 고 수평 연결 서버 설치 관리자에서 **azure** 옵션을 선택 하는 동안 특정 수평 설정을 자동으로 구성 합니다.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Azure VMware 솔루션 배포 아키텍처의 수평

일반적인 수평 아키텍처 설계에서는 pod 및 block 전략을 사용 합니다. 블록은 단일 vCenter 이지만 여러 블록을 결합 하 여 pod를 만듭니다. 수평 pod는 규모 확장성 제한에 따라 결정 되는 조직의 단위입니다. 각 수평 pod에는 별도의 관리 포털이 있으므로 표준 설계 방법은 pod의 수를 최소화 하는 것입니다.

모든 클라우드에는 자체 네트워크 연결 체계가 있습니다. VMware SDDC 네트워킹/NSX Edge와 결합 된 Azure VMware 솔루션 네트워크 연결은 온-프레미스와 다른 수평을 배포 하기 위한 고유한 요구 사항을 제공 합니다.

각 Azure 사설 클라우드 및 SDDC은 다음과 같은 경우 4000 데스크톱 또는 응용 프로그램 세션을 처리할 수 있습니다.

* 작업 트래픽은 Loginvsi 등과 작업 작업자 프로필에 맞춰집니다.

* 프로토콜 트래픽만 고려 되며 사용자 데이터는 없습니다.

* NSX Edge가 크게 구성 되어 있습니다.

>[!NOTE]
>워크 로드 프로필과 요구 사항이 다를 수 있으므로 사용 사례에 따라 결과가 달라질 수 있습니다. 사용자 데이터 볼륨은 워크 로드의 컨텍스트에서 규모 제한을 낮출 수 있습니다. 그에 따라 배포를 조정 하 고 계획 합니다. 자세한 내용은 [수평 배포를 위한 Azure VMware 솔루션 호스트 크기](#size-azure-vmware-solution-hosts-for-horizon-deployments) 섹션에서 크기 조정 지침을 참조 하세요.

Azure 사설 클라우드 및 SDDC 최대 한도를 고려 하 여 Azure Virtual Network 내에서 수평 연결 서버 및 VMware UAGs (통합 액세스 게이트웨이)를 실행 하는 배포 아키텍처를 사용 하는 것이 좋습니다. 이를 통해 각 Azure 사설 클라우드 및 SDDC을 블록으로 효과적으로 전환할 수 있습니다. 그리고 Azure VMware 솔루션에서 실행 되는 규모의 확장성을 극대화 합니다.

Azure Virtual Network에서 Azure 사설 클라우드/SDDCs로의 연결은 Express 경로 FastPath를 사용 하 여 구성 해야 합니다. 다음 다이어그램에서는 기본 수평 pod 배포를 보여 줍니다.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="ExpressPath Fast 경로를 사용 하는 일반적인 수평 pod 배포" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Azure VMware 솔루션의 수평 확장에 대 한 네트워크 연결

이 섹션에서는 Azure VMware 솔루션을 수평으로 확장 하는 데 도움이 되는 몇 가지 일반적인 배포 예제를 사용 하 여 네트워크 아키텍처를 개략적으로 설명 합니다. 특히 중요 한 네트워킹 요소에 중점을 둡니다. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Azure VMware 솔루션의 단일 수평 pod

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Azure VMware 솔루션의 단일 수평 pod" border="false":::

단일 수평 pod는 미국 동부 지역에 하나의 수평 pod를 배포 하기 때문에 가장 간단한 전달 배포 시나리오입니다.  각 사설 클라우드와 SDDC는 4000 데스크톱 세션을 처리 하기 때문에 최대 수평 pod 크기를 배포 합니다.  최대 3 개의 사설 클라우드/SDDCs 배포를 계획할 수 있습니다.

Azure Virtual Network에 배포 된 수평 인프라 Vm (가상 머신)을 사용 하면 수평 pod 당 12000 세션에 도달할 수 있습니다. 각 사설 클라우드와 SDDC와 Azure Virtual Network 간의 연결은 Express 경로 빠른 경로입니다.  사설 클라우드 간에 동-서 트래픽이 필요 하지 않습니다. 

이 기본 배포 예제에 대 한 주요 가정은 다음과 같습니다.

* 클라우드 Pod 아키텍처 (CPA)를 사용 하 여이 새 pod에 연결 하려는 온-프레미스 수평 pod가 없습니다.

* 최종 사용자는 인터넷을 통해 가상 데스크톱에 연결 합니다 (온-프레미스 데이터 센터를 통해 연결).

VPN 또는 Express 경로 회로를 통해 Azure Virtual Network의 AD 도메인 컨트롤러를 온-프레미스 AD와 연결 합니다.

기본 예제의 변형은 온-프레미스 리소스에 대 한 연결을 지 원하는 것입니다. 예를 들어 사용자는 데스크톱에 액세스 하 여 가상 데스크톱 응용 프로그램 트래픽을 생성 하거나 CPA를 사용 하 여 온-프레미스 수평 pod에 연결 합니다.

이 다이어그램에서는 온-프레미스 리소스에 대 한 연결을 지 원하는 방법을 보여 줍니다. Azure Virtual Network에 회사 네트워크에 연결 하려면 Express 경로 회로가 필요 합니다.  또한 Express 경로 Global Reach를 사용 하 여 각 사설 클라우드 및 SDDCs와 회사 네트워크를 연결 해야 합니다.  SDDC에서 Express 경로 회로 및 온-프레미스 리소스에 연결할 수 있습니다. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Azure Virtual Network에 회사 네트워크 연결" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>여러 지역에서 Azure VMware 솔루션의 여러 수평 pod

또 다른 시나리오는 여러 pod에 걸친 수평 확장입니다.  이 시나리오에서는 서로 다른 두 지역에 두 개의 수평 pod를 배포 하 고 CPA를 사용 하 여 페더레이션 합니다. 앞의 예제에서 네트워크 구성과 유사 하지만 일부 추가 지역 간 링크가 있습니다. 

각 지역의 Azure Virtual Network을 다른 지역의 사설 클라우드/SDDCs에 연결 합니다. 이를 통해 CPA 페더레이션의 수평 연결 서버를 통해 관리 되는 모든 데스크톱에 연결할 수 있습니다. 이 구성에 사설 클라우드/SDDCs를 추가 하면 전체 24000 세션으로 확장할 수 있습니다. 

동일한 지역에 두 개의 수평 pod를 배포 하는 경우에도 동일한 원칙이 적용 됩니다.  *별도의 Azure Virtual Network* 에 두 번째 수평 pod를 배포 해야 합니다. 단일 pod 예제와 마찬가지로 Express 경로를 사용 하 고 Global Reach를 사용 하 여 회사 네트워크 및 온-프레미스 pod를이 다중 pod/지역 예제에 연결할 수 있습니다. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" 여러 지역에서 Azure VMware 솔루션의 여러 수평 pod" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>수평 배포를 위한 Azure VMware 솔루션 호스트 크기 조정 

Azure VMware 솔루션에서 실행 되는 호스트에 대 한 수평 크기 조정 방법은 수평 온-프레미스 보다 간단 합니다.  Azure VMware 솔루션 호스트가 표준화 되었기 때문입니다.  정확한 호스트 크기 조정은 VDI 요구 사항을 지 원하는 데 필요한 호스트 수를 결정 하는 데 도움이 됩니다.  데스크톱 당 비용을 결정 하는 것이 중요 합니다.

### <a name="sizing-tables"></a>테이블 크기 조정

수평 가상 데스크톱에 대 한 특정 vCPU/vRAM 요구 사항은 고객의 특정 작업 프로필에 따라 다릅니다.   MSFT 및 VMware 판매 팀과 협력 하 여 가상 데스크톱에 대 한 vCPU/vRAM 요구 사항을 확인 하세요. 

| VM 당 vCPU | VM 당 vRAM (GB) | 인스턴스 | 100 Vm | 200 Vm | 300 Vm | 400 Vm | 500 Vm | 600 Vm | 700 Vm | 800 Vm | 900 Vm | 1000 Vm | 2000 Vm | 3000 Vm | 4000 Vm | 5000 Vm | 6000 Vm | 6400 Vm |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3.5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3.5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3.5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3.5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>수평 크기 조정 입력

계획 된 워크 로드에 대해 수집 해야 하는 사항은 다음과 같습니다.

* 동시 데스크톱 수

* 데스크톱 당 필수 vCPU

* 데스크톱 당 필요한 vRAM

* 데스크톱 당 필요한 저장소

일반적으로 VDI 배포는 호스트 크기를 결정 하는 CPU 또는 RAM의 제약을 받습니다. Loginvsi 등과 지식 근로자의 작업 부하에 대 한 다음 예제를 사용 하 여 성능 테스트로 유효성을 검사 합니다.

* 2000 동시 데스크톱 배포

* 데스크톱 당 2vCPU

* 데스크톱 당 4gb vRAM.

* 데스크톱 당 50 GB의 저장소

이 예에서 총 호스트 수는 18 개까지 이며, 111의 호스트 단위 밀도를 생성 합니다.

>[!IMPORTANT]
>고객 작업은 Loginvsi 등과 지식 근로자의이 예제와 다릅니다. 배포 계획의 일부로, 특정 크기 조정 및 성능 요구 사항에 맞게 VMware EUC SEs를 사용 합니다. 호스트 크기 조정을 완료 하기 전에 계획 된 실제 워크 로드를 사용 하 여 사용자 고유의 성능 테스트를 실행 하 고 적절 하 게 조정 해야 합니다.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Azure VMware 솔루션 라이선스의 수평 

Azure VMware 솔루션에 대 한 전체 실행 비용의 네 가지 구성 요소가 있습니다. 

### <a name="azure-vmware-solution-capacity-cost"></a>Azure VMware 솔루션 용량 비용

가격 책정에 대 한 자세한 내용은 [Azure VMware 솔루션 가격 책정](https://azure.microsoft.com/pricing/details/azure-vmware/) 페이지를 참조 하세요.

### <a name="horizon-licensing-cost"></a>수평 라이선스 비용

Azure VMware 솔루션과 함께 사용할 수 있는 두 가지 라이선스가 있습니다. 이러한 라이선스는 동시 사용자 (CCU) 또는 이름 지정 된 사용자 (뉴) 일 수 있습니다.

* 수평 구독 라이선스

* 수평 범용 구독 라이선스

예측 가능한 미래를 위해 Azure VMware 솔루션에 수평을 배포 하는 경우에는 저렴 한 비용으로 가격 구독 라이선스를 사용 합니다.

재해 복구 사용 사례와 마찬가지로 Azure VMware 솔루션 및 온-프레미스에 배포 된 경우에는 최종 범용 구독 라이선스를 선택 합니다. 온-프레미스 배포에 대 한 vSphere 라이선스를 포함 하므로 비용이 더 많이 듭니다.

사용자의 요구에 따라 수평 라이선스 비용을 확인 하려면 VMware EUC 판매 팀과 함께 작업 하세요.

### <a name="azure-instance-types"></a>Azure 인스턴스 유형

수평 인프라에 필요한 Azure virtual machine 크기를 이해 하려면 [여기](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution)에서 찾을 수 있는 VMware의 지침을 참조 하세요.

## <a name="next-steps"></a>다음 단계
Azure VMware 솔루션의 VMware 수평에 대해 자세히 알아보려면 [Vmware 수평 FAQ](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)를 참조 하세요.
