 <properties
   pageTitle="Azure 및 Linux | Microsoft Azure"
   description="Linux 가상 컴퓨터를 사용하여 Azure 계산, 저장소 및 네트워킹 서비스를 설명합니다."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="rickstercdn"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/01/2016"
   ms.author="rclaus"/>

# Azure 및 Linux
Microsoft Azure는 솔루션을 호스팅하는 데 적합한 분석, 가상 컴퓨터, 데이터베이스, 모바일, 네트워킹, 저장소 및 웹을 비롯한 나날이 다양해지는 통합 공용 클라우드 서비스입니다. Microsoft Azure는 온-프레미스 하드웨어에 투자하지 않고 원하는 때에 사용에 대한 비용을 지불할 수 있도록 확장할 수 있는 계산 플랫폼을 제공합니다. Azure는 솔루션을 강화하고 클라이언트의 요구를 맞추기 위해 필요한 규모에 준비되어 있습니다.
 
## Azure 가상 컴퓨터
Azure 가상 컴퓨터를 사용하면 다양한 컴퓨팅 솔루션을 민첩하게 배포할 수 있습니다. 거의 모든 운영 체제(Windows, Linux 또는 증가하는 파트너 목록 중 하나에서 사용자 지정으로 만든 운영 체제)에서 거의 모든 워크로드 및 언어를 배포할 수 있습니다. 그래도 원하는 내용이 표시되지 않나요? 걱정하지 마세요. 온-프레미스에서 고유한 이미지를 가져올 수 있습니다.
 
## Microsoft Azure에서 Linux 시작

Microsoft Azure 가상 컴퓨터, 저장소 및 네트워킹을 함께 사용하여 인터넷 규모로 요구 사항을 계산하는 Linux에 대한 "서비스 제공 인프라"를 제공합니다. Azure에서 Linux를 사용하기 시작하려면 다음이 필요합니다.

1. 무료 평가판 계정. [하나 가져옵니다](https://azure.microsoft.com/pricing/free-trial/).
2. Linux, Mac 및 Windows용 Azure 명령줄 인터페이스(Azure CLI). [설치합니다](../xplat-cli-install.md).
3. Linux VM. [만듭니다](virtual-machines-linux-quick-create-cli.md).
4. [SLA(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)를 충족하는 방법을 포함하여 Linux 및 Azure에 대한 자세한 내용입니다. **법적 문서를 싫어하는 경우라도 이 문서를 참고합니다**.

## 물류: 지역, 배포, 가용성, VM 크기 및 할당량
### 영역
Microsoft Azure 리소스는 전 세계 여러 지리적 지역에 걸쳐 분산됩니다. "지역"은 동일한 지리적 지역에서 여러 데이터 센터를 나타냅니다. 2016년 1월 1일을 기준으로 아메리카 8곳, 유럽 2곳, 아시아 태평양 6곳, 중국 본토 2곳, 인도 3곳이 포함됩니다. Azure 지역 모두의 전체 목록을 원하는 경우 기존 및 새로 발표된 지역의 목록이 **[여기](https://azure.microsoft.com/regions/)**에 있습니다.

### 배포
Microsoft Azure는 많은 파트너가 제공하고 유지 관리하는 다양하고 인기 있는 Linux 배포를 지원합니다. Azure 마켓플레이스에서 CentOS, Debian, Red Hat Enterprise, Ubuntu, FreeBSD 등과 같은 배포를 찾습니다. 다양한 Linux 커뮤니티와 적극적으로 작업하여 인증 배포 목록에 다양한 옵션을 추가합니다. **[현재 배포 확인](virtual-machines-linux-endorsed-distros.md)** 선택한 기본 Linux 배포가 현재 갤러리에 없는 경우 **[이 페이지에서](virtual-machines-linux-create-upload-generic.md)** 지침에 따라 "직접 Linux" VM을 가져올 수 있습니다.

## 가용성 및 Microsoft Azure SLA
배포가 99.95 VM 서비스 수준 계약에 적합하도록 하기 위해 가용성 집합 내부에서 워크로드를 실행하는 두 개 이상의 VM을 배포해야 합니다. 이렇게 하면 VM이 데이터 센터에서 여러 오류 도메인 간에 분산될 뿐만 아니라 다양한 유지 관리 창이 있는 호스트에 배포됩니다. SLA의 자세한 내용은 **[여기 온라인](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)**에서 볼 수 있습니다.

## VM 크기 및 할당량
Azure에서 VM을 배포할 경우 워크로드에 적합한 크기의 시리즈 중 하나에서 VM 크기를 선택합니다. 크기는 가상 컴퓨터의 처리 성능, 메모리 및 저장소 용량에 영향을 줍니다. VM이 할당된 리소스를 실행하고 소비하는 시간에 따라 청구됩니다. 전체 목록은 [가상 컴퓨터의 크기](virtual-machines-linux-sizes.md)에서 다음 문서를 참조하세요.

다음은 시리즈(A, D, DS, G 및 GS) 중 하나에서 VM 크기를 선택하기 위한 몇 가지 기본 지침입니다.

* A 시리즈 VM은 간단한 워크로드 및 개발/테스트 시나리오에 대한 초급 수준의 VM 가격을 책정한 값입니다. 모든 지역에서 광범위하게 사용할 수 있고 가상 컴퓨터에 사용할 수 있는 모든 표준 리소스를 연결하고 사용할 수 있습니다.
* A 시리즈 크기(A8-A11)는 고성능 계산 클러스터 응용 프로그램에 적합한 특수한 계산 집약적 구성입니다.
* D 시리즈 VM은 높은 계산 능력과 임시 디스크 성능이 필요한 응용 프로그램을 실행하도록 설계되었습니다. D 시리즈 VM은 임시 디스크를 위해 빠른 프로세서, 더 높은 메모리-코어 비율 및 SSD(반도체 드라이브)를 제공합니다. 
* D 시리즈의 최신 버전인 Dv2 시리즈는 더 강력한 CPU 기능을 제공합니다. Dv2 시리즈 CPU는 D 시리즈 CPU보다 약 35% 빠릅니다. 최근 출시된 2.4GHz Intel Xeon® E5-2673 v3(Haswell) 프로세서에 기반하고 Intel Turbo Boost Technology 2.0을 사용하여 최대 3.2GHz까지 올라갈 수 있습니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.
* G 시리즈 VM은 많은 메모리를 제공하고 Intel Xeon E5 V3 제품군 프로세서가 설치된 호스트에서 실행합니다.

참고: DS 시리즈 및 GS 시리즈 VM은 I/O가 많은 워크로드에 SSD가 지원하는 높은 성능과 짧은 대기 시간을 갖는 저장소인 프리미엄 저장소에 대한 권한을 갖습니다. 프리미엄 저장소는 특정 지역에서만 사용할 수 있습니다. 자세한 내용은 **[프리미엄 저장소: Azure 가상 컴퓨터 워크로드를 위한 고성능 저장소](../storage/storage-premium-storage.md)**를 참조하세요.

각 Azure 구독에는 프로젝트에 대해 많은 수의 VM을 배포하는 데 영향을 줄 수 있는 기본 할당량 한도가 있습니다. 구독별 기준으로 현재 제한은 지역당 20대의 VM입니다. 이 할당량은 제한 증가를 요청하는 지원 티켓을 제출하여 증가될 수 있습니다. 할당량 제한에 대한 자세한 내용은 **[Azure 구독 서비스 제한](../azure-subscription-service-limits.md)**을 참조하세요.

## 다음 단계

무료 평가판 계정. **[하나 가져옵니다](https://azure.microsoft.com/pricing/free-trial/)**. 이미 있는 경우 체험하려면 **[Azure CLI를 설치](../xplat-cli-install.md)**합니다. 설치했다면 [이제 Linux VM 만들기로 이동](virtual-machines-linux-quick-create-cli.md)합니다.

<!---HONumber=AcomDC_0518_2016-->