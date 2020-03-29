---
title: 메인프레임 계산을 Azure 가상 컴퓨터로 이동
description: Azure 컴퓨팅 리소스는 메인프레임 용량과 유리하게 비교되므로 IBM z14 응용 프로그램을 마이그레이션하고 현대화할 수 있습니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288934"
---
# <a name="move-mainframe-compute-to-azure"></a>메인프레임 계산을 Azure로 이동

메인프레임은 높은 신뢰성과 가용성으로 명성을 얻고 있으며 많은 기업의 신뢰할 수 있는 중추역할을 계속하고 있습니다. 그들은 종종 거의 무한한 확장성과 컴퓨팅 파워를 가지고 있다고 생각됩니다. 그러나 일부 기업은 사용 가능한 가장 큰 메인프레임의 기능을 능가했습니다. 이 경우 Azure는 민첩성, 도달 범위 및 인프라 절감 효과를 제공합니다.

Microsoft Azure에서 메인프레임 워크로드를 실행하려면 메인프레임의 계산 기능이 Azure와 어떻게 비교되는지 알아야 합니다. IBM z14 메인프레임(이 글을 쓰는 현재 모델)을 기반으로 이 문서에서는 Azure에서 비교 가능한 결과를 얻는 방법을 설명합니다.

시작하려면 환경을 나란히 고려하십시오. 다음 그림에서 응용 프로그램을 실행하기 위한 메인프레임 환경을 Azure 호스팅 환경과 비교합니다.

![Azure 서비스 및 에뮬레이션 환경은 유사한 지원을 제공하고 마이그레이션을 간소화합니다.](media/mainframe-compute-azure.png)

메인프레임의 힘은 종종 수천 명의 사용자에 대한 수백만 개의 업데이트를 처리하는 OLTP(온라인 트랜잭션 처리) 시스템에 사용됩니다. 이러한 응용 프로그램은 트랜잭션 처리, 화면 처리 및 양식 입력에 소프트웨어를 사용하는 경우가 많습니다. 고객 정보 제어 시스템(CICS), 정보 관리 시스템(IMS) 또는 트랜잭션 인터페이스 패키지(TIP)를 사용할 수 있습니다.

그림에서 볼 수 있듯이 Azure의 TPM 에뮬레이터는 CICS 및 IMS 워크로드를 처리할 수 있습니다. Azure의 일괄 처리 시스템 에뮬레이터는 JCL(작업 제어 언어)의 역할을 수행합니다. 메인프레임 데이터는 Azure SQL 데이터베이스와 같은 Azure 데이터베이스로 마이그레이션됩니다. Azure 가상 시스템에서 호스팅되는 Azure 서비스 또는 기타 소프트웨어를 시스템 관리에 사용할 수 있습니다.

## <a name="mainframe-compute-at-a-glance"></a>메인프레임 한눈에 계산

z14 메인프레임에서 프로세서는 최대 *4개의 서랍에*배치되어 있습니다. *서랍은* 단순히 프로세서와 칩셋의 클러스터입니다. 각 서랍에는 6개의 액티브 센트럴 프로세서(CP) 칩이 있으며 각 CP에는 10개의 시스템 컨트롤러(SC) 칩이 있습니다. 인텔 x86 용어에는 서랍당 6개의 소켓, 소켓당 10개의 코어, 4개의 서랍이 있습니다. 이 아키텍처는 z14에 대해 최대 24개의 소켓과 240개의 코어와 대략적인 동등한 코어를 제공합니다.

빠른 z14 CP는 5.2GHz 클럭 속도를 가지고 있습니다. 일반적으로 z14는 상자에 있는 모든 AP와 함께 제공됩니다. 필요에 따라 활성화됩니다. 고객은 일반적으로 실제 사용량에도 불구하고 매월 최소 4시간의 계산 시간에 대한 요금이 부과됩니다.

메인프레임 프로세서는 다음 유형 중 하나로 구성할 수 있습니다.

- 범용(GP) 프로세서
- 시스템 z 통합 정보 프로세서(zIIP)
- 리눅스(IFL) 프로세서용 통합 시설
- 시스템 지원 프로세서(SAP)
- 통합 커플링 기능(ICF) 프로세서

## <a name="scaling-mainframe-compute-up-and-out"></a>메인프레임 확장 계산

IBM 메인프레임은 최대 240개의 코어(단일 시스템의 현재 z14 크기)까지 확장할 수 있는 기능을 제공합니다. 또한 IBM 메인프레임은 커플링 시설(CF)이라는 기능을 통해 확장할 수 있습니다. CF를 사용하면 여러 메인프레임 시스템이 동일한 데이터에 동시에 액세스할 수 있습니다. 메인프레임 병렬 Sysplex 기술은 CF를 사용하여 클러스터의 메인프레임 프로세서를 그룹화합니다. 이 가이드를 작성할 때 병렬 Sysplex 기능은 각각 64개의 프로세서로 구성된 32개의 그룹을 지원했습니다. 이러한 방식으로 최대 2,048개의 프로세서를 그룹화하여 컴퓨팅 용량을 확장할 수 있습니다.

CF를 사용하면 계산 클러스터가 직접 액세스하여 데이터를 공유할 수 있습니다. 정보, 캐시 정보 및 공유 데이터 리소스 목록을 잠그는 데 사용됩니다. 하나 이상의 CF를 사용하는 병렬 Sysplex는 "모든 것을 공유하는" 확장 계산 클러스터로 생각할 수 있습니다. 이러한 기능에 대한 자세한 내용은 IBM 웹 사이트에서 [IBM Z의 병렬 Sysplex를](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) 참조하십시오.

응용 프로그램은 이러한 기능을 사용하여 확장 성능과 고가용성을 모두 제공할 수 있습니다. CICS가 CF와 병렬 Sysplex를 사용하는 방법에 대한 자세한 내용은 [IBM CICS 및 커플링 기능: 기본](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) 레드북 을 넘어 다운로드하십시오.

## <a name="azure-compute-at-a-glance"></a>Azure 계산 한 눈에

어떤 사람들은 인텔 기반 서버가 메인프레임만큼 강력하지 않다고 잘못 생각합니다. 그러나 새로운 코어 밀도가 높은 인텔 기반 시스템은 메인프레임만큼 의 컴퓨팅 용량을 가지고 있습니다. 이 섹션에서는 컴퓨팅 및 저장소에 대한 서비스로서의 Azure 인프라(IaaS) 옵션에 대해 설명합니다. Azure는 PaaS(서비스형 플랫폼) 옵션도 제공하지만 이 문서에서는 유사한 메인프레임 용량을 제공하는 IaaS 선택에 중점을 둡니다.

Azure 가상 컴퓨터는 다양한 크기와 유형에서 계산 성능을 제공합니다. Azure에서 가상 CPU(vCPU)는 메인프레임의 코어와 거의 동일합니다.

현재 Azure 가상 시스템 크기 범위는 1에서 128vCPU까지 제공합니다. 가상 컴퓨터(VM) 유형은 특정 워크로드에 최적화되어 있습니다. 예를 들어 다음 목록은 VM 유형(이 작성 현재)과 권장 용도를 보여 주었습니다.

| 크기     | 유형 및 설명                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 시리즈 | 64vCPU및 최대 3.5GHz 클럭 속도의 범용                           |
| E 시리즈 | 최대 64vCPU로 최적화된 메모리                                                 |
| F 시리즈 | 최대 64vCPU 및 3..7GHz 클럭 속도로 최적화된 컴퓨팅                       |
| H 시리즈 | 고성능 컴퓨팅(HPC) 애플리케이션에 최적화                          |
| L 시리즈 | NoSQL과 같은 데이터베이스에서 지원하는 고처리량 애플리케이션에 최적화된 스토리지 |
| M 시리즈 | 최대 128vCPU로 VM을 최적화한 최대 컴퓨팅 및 메모리                        |

사용 가능한 VM에 대한 자세한 내용은 [가상 컴퓨터 시리즈를](https://azure.microsoft.com/pricing/details/virtual-machines/series/)참조하십시오.

z14 메인프레임에는 최대 240개의 코어가 있을 수 있습니다. 그러나 z14 메인프레임은 단일 응용 프로그램 이나 워크로드에 대 한 모든 코어를 사용 하지 않습니다. 대신 메인프레임은 워크로드를 논리적 파티션(LPA)으로 분리하고, LpA는 MIPS(초당 수백만 개의 명령) 또는 MSU(백만 서비스 단위)의 등급을 갖습니다. Azure에서 메인프레임 워크로드를 실행하는 데 필요한 비교 가능한 VM 크기를 결정할 때 MIPS(또는 MSU) 등급을 고려합니다.

다음은 일반적인 추정치입니다.

-   vCPU당 150MIPS

-   프로세서당 1,000MIPS

LPAR에서 지정된 워크로드에 대한 올바른 VM 크기를 확인하려면 먼저 워크로드에 대한 VM을 최적화합니다. 그런 다음 필요한 vCPU 수를 확인합니다. 보수적인 추정치는 vCPU당 150 MIPS입니다. 예를 들어 이 추정치에 따르면 16개의 vCPU가 있는 F 시리즈 VM은 2,400MIPS의 LPAR에서 오는 IBM Db2 워크로드를 쉽게 지원할 수 있습니다.

## <a name="azure-compute-scale-up"></a>Azure 계산 확장

M 시리즈 VM은 최대 128개의 vCPU까지 확장할 수 있습니다(이 문서가 작성될 당시). VCPU당 150MIPS의 보수적인 추정치를 사용하는 M 시리즈 VM은 약 19,000MIPS에 해당합니다. 메인프레임에 대한 MIPS를 추정하는 일반적인 규칙은 프로세서당 1,000 MIPS입니다. z14 메인프레임에는 최대 24개의 프로세서가 있으며 단일 메인프레임 시스템에 대해 약 24,000MIPS를 제공할 수 있습니다.

가장 큰 단일 z14 메인프레임에는 Azure에서 사용할 수 있는 가장 큰 VM보다 약 5,000MIPS가 있습니다. 그러나 워크로드가 배포되는 방식을 비교하는 것이 중요합니다. 메인프레임 시스템에 응용 프로그램과 관계형 데이터베이스가 모두 있는 경우 일반적으로 동일한 물리적 메인프레임에 배포됩니다. Azure에서 동일한 솔루션은 응용 프로그램에 대해 하나의 VM과 데이터베이스에 적합한 크기의 별도의 VM을 사용하여 배포되는 경우가 많습니다.

예를 들어 M64 vCPU 시스템이 응용 프로그램을 지원하고 M96 vCPU가 데이터베이스에 사용되는 경우 다음 그림과 같이 약 150개의 vCPU가 필요하거나 약 24,000개의 MiPS가 필요합니다.

![24,000MIPS의 워크로드 배포 비교](media/mainframe-compute-mips.png)

이 방법은 개별 VM으로 LpA를 마이그레이션하는 것입니다. 그런 다음 Azure는 단일 메인프레임 시스템에 배포되는 대부분의 응용 프로그램에 필요한 크기로 쉽게 확장할 수 있습니다.

## <a name="azure-compute-scale-out"></a>Azure 계산 확장

Azure 기반 솔루션의 장점 중 하나는 확장할 수 있다는 것입니다. 확장을 통해 응용 프로그램에서 거의 무한한 컴퓨팅 용량을 사용할 수 있습니다. Azure는 계산 능력을 확장하기 위한 여러 메서드를 지원합니다.

- **클러스터 간에 부하 분산.** 이 시나리오에서 응용 프로그램은 [로드 밸런서](/azure/load-balancer/load-balancer-overview) 또는 리소스 관리자를 사용하여 클러스터의 여러 VM 간에 워크로드를 분산할 수 있습니다. 더 많은 컴퓨팅 용량이 필요한 경우 클러스터에 추가 VM이 추가됩니다.

- **가상 시스템 스케일 집합입니다.** 이 버스트 시나리오에서 응용 프로그램은 VM 사용량을 기반으로 추가 [계산 리소스로](/azure/virtual-machine-scale-sets/overview) 확장할 수 있습니다. 수요가 감소하면 스케일 세트의 VM 수도 감소하여 컴퓨팅 파워를 효율적으로 사용할 수 있습니다.

- **PaaS 크기 조정.** Azure PaaS 오퍼링은 계산 리소스를 확장합니다. 예를 들어 [Azure Service Fabric은](/azure/service-fabric/service-fabric-overview) 요청 볼륨의 증가를 충족하기 위해 계산 리소스를 할당합니다.

- **쿠버네츠 클러스터.** Azure의 응용 프로그램은 지정된 리소스에 대한 계산 서비스에 [Kubernetes 클러스터를](/azure/aks/concepts-clusters-workloads) 사용할 수 있습니다. AZURE Kubernetes 서비스(AKS)는 Azure에서 Kubernetes 노드, 풀 및 클러스터를 오케스트레이션하는 관리형 서비스입니다.

계산 리소스를 확장하는 데 적합한 방법을 선택하려면 Azure와 메인프레임의 차이점을 이해하는 것이 중요합니다. 핵심은 계산 리소스에서 데이터를 공유하는 방법 또는 if-입니다. Azure에서 데이터는 일반적으로 여러 VM에서 공유되지 않습니다. 확장 계산 클러스터의 여러 VM에서 데이터 공유가 필요한 경우 공유 데이터는 이 기능을 지원하는 리소스에 있어야 합니다. Azure에서 데이터 공유에는 다음 섹션에서 설명하는 대로 저장소가 포함됩니다.

## <a name="azure-compute-optimization"></a>Azure 계산 최적화

Azure 아키텍처에서 각 처리 계층을 최적화할 수 있습니다. 각 환경에 가장 적합한 유형의 VM 및 기능을 사용합니다. 다음 그림은 Db2를 사용하는 CICS 응용 프로그램을 지원하기 위해 Azure에 VM을 배포하는 하나의 잠재적패턴을 보여 주며 있습니다. 기본 사이트에서는 가용성이 높은 프로덕션, 사전 프로덕션 및 테스트 VM이 배포됩니다. 보조 사이트는 백업 및 재해 복구용입니다.

각 계층은 적절한 재해 복구 서비스를 제공할 수도 있습니다. 프로덕션 VM과 데이터베이스 VM에는 핫 복구나 웜 복구가 필요한 반면 개발 및 테스트 VM은 콜드 복구를 지원하는 경우를 예로 들 수 있습니다.

![재해 복구를 지원하는 고가용성 배포](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>다음 단계

- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 가상 머신에서 메인프레임 다시 호스팅](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [메인프레임 저장소를 Azure로 이동](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 리소스

- [IBM Z의 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 및 커플링 기능: 기본 사항 이상](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 순수 규모 기능 설치에 필요한 사용자 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 인스턴스 명령 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 퓨어스케일 클러스터된 데이터베이스 솔루션](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [메인프레임 응용 프로그램에 대한 마이크로 소프트 Azure 정부 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [마이크로소프트와 페드램](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>더 많은 마이그레이션 리소스

- [Azure Virtual Data Center 리프트 앤 시프트 가이드](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
