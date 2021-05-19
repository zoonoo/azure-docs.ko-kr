---
title: 메인프레임 컴퓨팅을 Azure Virtual Machines로 이동
description: Azure Compute 리소스는 메인프레임 용량에 비해 유리하므로 IBM z14 애플리케이션을 마이그레이션하고 현대화할 수 있습니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 84682514d83de9d6a613581d68301efe33cd44ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954397"
---
# <a name="move-mainframe-compute-to-azure"></a>메인프레임 컴퓨팅을 Azure로 이동

메인프레임은 높은 신뢰성과 가용성으로 평판을 가지고 있으며 계속해서 많은 기업에서 신뢰할 수 있는 백본으로 유지됩니다. 메인프레임의 확장성 및 컴퓨팅 능력은 거의 무제한이라고 생각해 왔습니다. 그러나 일부 엔터프라이즈는 사용 가능한 가장 큰 메인프레임의 기능을 능가합니다. 이를 통해 Azure는 민첩성, 접근 및 인프라 절감 효과를 제공합니다.

Microsoft Azure에서 메인프레임 워크로드를 실행하려면 메인프레임 컴퓨팅 기능이 Azure와 어떻게 비교되는지 알아야 합니다. 이 문서에서는 IBM z14 메인프레임(이 문서 작성 시점의 최신 모델)을 기반으로 하여 Azure에서 비슷한 결과를 얻는 방법을 설명합니다.

시작하려면 여러 환경을 각각 고려합니다. 다음 그림은 애플리케이션을 실행하는 메인프레임 환경을 Azure 호스팅 환경과 비교합니다.

![Azure 서비스 및 에뮬레이션 환경에서는 비슷한 지원을 제공하고 마이그레이션을 간소화합니다.](media/mainframe-compute-azure.png)

메인프레임의 강력한 기능은 수천 명의 사용자에 대해 수백만 개의 업데이트를 처리하는 OLTP(온라인 트랜잭션 처리) 시스템에 자주 사용됩니다. 이러한 애플리케이션은 종종 트랜잭션 처리, 화면 처리 및 양식 입력을 위해 소프트웨어를 사용합니다. CICS(고객 정보 컨트롤 시스템), IMS(정보 관리 시스템) 또는 TIP(트랜잭션 인터페이스 패키지)를 사용할 수 있습니다.

그림에 표시된 것처럼 Azure의 TPM 에뮬레이터는 CICS 및 IMS 워크로드를 처리할 수 있습니다. Azure의 일괄 처리 시스템 에뮬레이터는 JCL(작업 제어 언어)의 역할을 수행합니다. 메인프레임 데이터는 Azure SQL 데이터베이스와 같은 Azure 데이터베이스로 마이그레이션됩니다. Azure Virtual Machines에서 호스트되는 Azure 서비스 또는 기타 소프트웨어를 시스템 관리에 사용할 수 있습니다.

## <a name="mainframe-compute-at-a-glance"></a>메인프레임 컴퓨팅 개요

z14 메인프레임에서 프로세서는 최대 4개의 *서랍* 으로 정렬됩니다. *서랍* 은 단순히 프로세서 및 칩셋의 클러스터입니다. 각 서랍에는 6개의 액티브 CP(중앙 프로세서) 칩이 있고 각 CP에는 10개의 SC(시스템 컨트롤러) 칩이 있습니다. Intel x86 용어에는 서랍당 6개의 소켓, 소켓당 10개의 코어 및 4개의 서랍이 있습니다. 이 아키텍처는 z14에 대한 24개의 소켓과 240개의 코어와 동일한 대략적인 값을 제공합니다.

빠른 z14 CP의 클럭 속도는 5.2GHz입니다. 일반적으로 z14는 상자에 모든 CP와 함께 제공됩니다. 필요에 따라 활성화됩니다. 고객은 일반적으로 실제 사용량에도 불구하고 매월 최소 4시간의 컴퓨팅 시간에 대해 요금이 부과됩니다.

메인프레임 프로세서는 다음 유형 중 하나로 구성할 수 있습니다.

- GP(일반 용도) 프로세서
- System zIIP(z 통합 정보 프로세서)
- IFL(Linux용 통합 기능) 프로세서
- SAP(시스템 지원 프로세서)
- ICF(통합 결합 기능) 프로세서

## <a name="scaling-mainframe-compute-up-and-out"></a>메인프레임 컴퓨팅 스케일 업 및 스케일 아웃

IBM 메인프레임은 최대 240개의 코어(단일 시스템의 현재 z14 크기)까지 스케일 업할 수 있는 기능을 제공합니다. 또한 IBM 메인프레임은 CF(결합 기능)라는 기능을 통해 스케일 아웃할 수 있습니다. CF를 사용하면 여러 메인프레임 시스템이 동일한 데이터에 동시에 액세스할 수 있습니다. CF를 사용하여 메인프레임 병렬 Sysplex 기술은 메인프레임 프로세서를 클러스터로 그룹화합니다. 이 가이드가 작성되었을 때 병렬 Sysplex 기능은 32개 그룹 각각에 64개 프로세서를 지원했습니다. 이러한 방식으로 최대 2,048개의 프로세서를 그룹화하여 컴퓨팅 용량을 스케일 아웃할 수 있습니다.

CF를 사용하면 컴퓨팅 클러스터에서 직접 액세스와 데이터를 공유할 수 있습니다. 이는 잠금 정보, 캐시 정보 및 공유 데이터 리소스 목록에 사용됩니다. 하나 이상의 CF를 사용하는 병렬 Sysplex는 "모두 공유" 스케일 아웃 컴퓨팅 클러스터로 생각할 수 있습니다. 이러한 기능에 대한 자세한 내용은 IBM 웹 사이트에서 [IBM Z의 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)를 참조합니다.

애플리케이션은 이러한 기능을 사용하여 스케일 아웃 성능과 고가용성을 모두 제공할 수 있습니다. CICS가 CF를 통해 병렬 Sysplex를 사용하는 방법에 대한 정보를 알아보려면 [BM CICS 및 결합 기능: 기본 이상](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) 레드북을 다운로드합니다.

## <a name="azure-compute-at-a-glance"></a>Azure Compute 개요

Intel 기반 서버가 메인프레임만큼 강력하지 않다고 잘못 생각하는 사람들이 있습니다. 그러나 새 코어 집약의 Intel 기반 시스템에는 메인프레임만큼 많은 컴퓨팅 용량이 있습니다. 이 섹션에서는 컴퓨팅 및 스토리지에 대한 Azure IaaS(서비스 제공 인프라) 옵션을 설명합니다. Azure가 PaaS(Platform as a Service) 옵션도 제공하지만 이 문서에서는 유사한 메인프레임 용량을 제공하는 IaaS 선택에 중점을 두고 설명합니다.

Azure Virtual Machines는 다양한 크기와 유형의 컴퓨팅 능력을 제공합니다. Azure에서 vCPU(가상 CPU)는 대략 메인프레임의 코어와 동일합니다.

현재 Azure Virtual Machines 크기 범위는 1~128개의 vCPU를 제공합니다. VM(가상 머신) 유형은 특정 워크로드에 대해 최적화됩니다. 예를 들어 다음 목록에는 VM 유형(이 문서 작성 시점)과 권장 사용이 나와 있습니다.

| 크기     | 형식 및 설명                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 시리즈 | 64개 vCPU 및 최대 3.5GHz 클럭 속도의 일반 용도                           |
| E 시리즈 | 최대 64개의 vCPU로 최적화된 메모리                                                 |
| F 시리즈 | 최대 64개 vCPU 및 3.7GHz 클럭 속도의 최적화된 컴퓨팅                       |
| H 시리즈 | HPC(고성능 컴퓨팅) 애플리케이션에 최적화                          |
| L 시리즈 | NoSQL과 같은 데이터베이스가 지원하는 높은 처리량 애플리케이션에 최적화된 스토리지 |
| M 시리즈 | 최대 128개 vCPU가 포함된 가장 큰 컴퓨팅 및 메모리 최적화 VM                        |

사용 가능한 VM에 대한 자세한 내용은 [가상 머신 시리즈](https://azure.microsoft.com/pricing/details/virtual-machines/series/)를 참조하세요.

z14 메인프레임은 최대 240개 코어를 포함할 수 있습니다. 그러나 z14 메인프레임은 단일 애플리케이션 또는 워크로드에 대한 모든 코어를 거의 사용하지 않습니다. 대신, 메인프레임은 워크로드를 논리 파티션(LPAR)으로 분리하며, LPAR에는 MIPS(초당 백만 개의 명령) 또는 MSU(백만 서비스 단위) 등급이 있습니다. Azure에서 메인프레임 워크로드를 실행하는 데 필요한 동급의 VM 크기를 결정할 때 MIPS(또는 MSU) 등급을 고려해야 합니다.

일반적인 추정치는 다음과 같습니다.

-   vCPU당 150MIPS

-   프로세서당 1,000MIPS

LPAR의 지정된 워크로드에 대한 올바른 VM 크기를 결정하려면 먼저 워크로드에 대한 VM을 최적화합니다. 그런 다음 필요한 vCPU 수를 결정합니다. 보수적인 추정치는 vCPU당 150MIPS입니다. 예를 들어,이 추정치를 기반으로 16개의 vCPU가 있는 F 시리즈 VM은 2,400MIPS의 LPAR에서 들어오는 IBM Db2 워크로드를 쉽게 지원할 수 있습니다.

## <a name="azure-compute-scale-up"></a>Azure Compute 스케일 업

M 시리즈 VM은 최대 128개의 vCPU로 스케일 업할 수 있습니다(이 문서 작성 시점). vCPU당 150MIPS라는 보수적인 추정치를 사용하면 M 시리즈 VM은 약 19,000MIPS에 해당합니다. 메인프레임에 대한 MIPS를 추정하는 일반적인 규칙은 프로세서당 1,000MIPS입니다. z14 메인프레임은 최대 24개의 프로세서를 포함할 수 있으며 단일 메인프레임 시스템에 대해 약 24,000MIPS를 제공합니다.

가장 큰 단일 z14 메인프레임은 Azure에서 사용할 수 있는 가장 큰 VM보다 약 5,000MIPS 더 많습니다. 그러나 워크로드를 배포하는 방법을 비교하는 것이 중요합니다. 메인프레임 시스템에 애플리케이션과 관계형 데이터베이스가 모두 있는 경우 일반적으로 동일한 물리적 메인프레임(각각 자체 LPAR에 있음)에 배포됩니다. Azure의 동일한 솔루션은 종종 애플리케이션용 VM 하나와 적절한 크기의 데이터베이스용 별도 VM을 사용하여 배포됩니다.

예를 들어 M64 vCPU 시스템이 애플리케이션을 지원하고 M96 vCPU가 데이터베이스에 사용되는 경우 약 150개의 vCPU가 필요하거나 다음 그림과 같이 약 24,000MIPS가 필요합니다.

![24,000MIPS의 워크로드 배포 비교](media/mainframe-compute-mips.png)

이 방법은 LPAR을 개별 VM으로 마이그레이션하는 것입니다. 그런 다음 Azure는 단일 메인프레임 시스템에 배포된 대부분의 애플리케이션에 필요한 크기로 쉽게 스케일 업됩니다.

## <a name="azure-compute-scale-out"></a>Azure Compute 스케일 아웃

Azure 기반 솔루션의 장점 중 하나는 스케일 아웃 기능입니다. 스케일 아웃을 통해 애플리케이션에서 거의 무제한의 컴퓨팅 용량을 사용할 수 있습니다. Azure는 컴퓨팅 능력을 스케일 아웃하는 여러 방법을 지원합니다.

- **클러스터를 통한 부하 분산.** 이 시나리오에서 애플리케이션은 [부하 분산 장치](../../../../load-balancer/load-balancer-overview.md) 또는 리소스 관리자를 사용하여 클러스터에 있는 여러 VM간에 워크로드를 분산할 수 있습니다. 더 많은 컴퓨팅 용량이 필요한 경우 추가 VM이 클러스터에 추가됩니다.

- **Virtual Machine Scale Sets** 이 버스트 시나리오에서 애플리케이션은 VM 사용량에 따라 추가 [컴퓨팅 리소스](../../../../virtual-machine-scale-sets/overview.md)로 확장할 수 있습니다. 수요가 감소하면 확장 집합의 VM 수도 줄어들어 컴퓨팅 능력을 효율적으로 사용할 수 있습니다.

- **PaaS 크기 조정.** Azure PaaS 서비스를 제공하여 컴퓨팅 리소스의 크기를 조정합니다. 예를 들어 [Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md)은 요청 볼륨 증가를 충족하기 위해 컴퓨팅 리소스를 할당합니다.

- **Kubernetes 클러스터** Azure의 애플리케이션은 지정된 리소스에 대한 컴퓨팅 서비스에 [Kubernetes 클러스터](../../../../aks/concepts-clusters-workloads.md)를 사용할 수 있습니다. AKS(Azure Kubernetes Service)는 Azure에서 Kubernetes 노드, 풀 및 클러스터를 오케스트레이션하는 관리되는 서비스입니다.

컴퓨팅 리소스를 확장하는 데 적합한 방법을 선택하려면 Azure와 메인프레임이 어떻게 다른지 이해하는 것이 중요합니다. 핵심은 컴퓨팅 리소스에서 데이터를 공유하는지 여부와 그 방법을 이해하는 것입니다. Azure에서 기본적으로 데이터는 여러 VM에서 공유되지 않습니다. 스케일 아웃 컴퓨팅 클러스터의 여러 VM에 데이터 공유가 필요한 경우 공유 데이터는 이 기능을 지원하는 리소스에 있어야 합니다. Azure에서 데이터 공유에는 다음 섹션에서 설명하는 것처럼 스토리지가 포함됩니다.

## <a name="azure-compute-optimization"></a>Azure Compute 최적화

Azure 아키텍처에서 각 처리 계층을 최적화할 수 있습니다. 각 환경에 가장 적합한 유형의 VM 및 기능을 사용합니다. 다음 그림은 Db2를 사용하는 CICS 애플리케이션을 지원하기 위해 Azure에서 VM을 배포하는 한 가지 잠재적 패턴을 보여 줍니다. 기본 사이트에서는 가용성이 높은 프로덕션, 사전 프로덕션 및 테스트 VM이 배포됩니다. 보조 사이트는 백업 및 재해 복구용입니다.

각 계층에서 적절한 재해 복구 서비스를 제공할 수도 있습니다. 프로덕션 VM과 데이터베이스 VM에는 핫 복구나 웜 복구가 필요한 반면 개발 및 테스트 VM은 콜드 복구를 지원하는 경우를 예로 들 수 있습니다.

![재해 복구를 지원하는 고가용성 배포](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>다음 단계

- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure Virtual Machines에서 메인프레임 다시 호스팅](../overview.md)
- [메인프레임 스토리지를 Azure로 이동](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 리소스

- [IBM Z의 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 및 결합 기능: 기본 이상](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 pureScale 기능 설치에 대한 필수 사용자 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 인스턴스 명령 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 클러스터 데이터베이스 솔루션](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [메인프레임 애플리케이션의 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 및 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>추가 마이그레이션 리소스

- [Azure Virtual Data Center 리프트 앤 시프트 가이드](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
