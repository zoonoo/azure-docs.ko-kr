---
title: 메인프레임 계산을 Azure로 이동 Virtual Machines
description: Azure 계산 리소스는 잘를 메인프레임 용량과 비교 하 여 IBM z14 응용 프로그램을 마이그레이션하고 현대화 수 있습니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288934"
---
# <a name="move-mainframe-compute-to-azure"></a>메인프레임 계산을 Azure로 이동

메인프레임은 높은 안정성과 가용성을 위해 평판을가지고 있으며 많은 엔터프라이즈의 신뢰할 수 있는 백본으로 계속 유지 됩니다. 자주 발생 하는 확장성 및 컴퓨팅 전력이 거의 무제한 이라고 생각 하 고 있습니다. 그러나 일부 기업은 사용 가능한 가장 큰 메인프레임의 기능을 outgrown 합니다. 이와 같은 경우 Azure는 민첩성, 접근 및 인프라 절감 액을 제공 합니다.

Microsoft Azure에서 메인프레임 워크 로드를 실행 하려면 메인프레임 계산 기능이 Azure와 어떻게 비교 되는지 알아야 합니다. 이 문서는 IBM z14 메인프레임 (이 문서를 작성 하는 최신 모델)을 기반으로 하 여 Azure에서 비슷한 결과를 얻는 방법을 설명 합니다.

시작 하려면 환경을 나란히 고려 합니다. 다음 그림은 응용 프로그램을 실행 하는 메인프레임 환경을 Azure 호스팅 환경으로 비교 합니다.

![Azure 서비스 및 에뮬레이션 환경에서는 비슷한 지원을 제공 하 고 마이그레이션을 간소화 합니다.](media/mainframe-compute-azure.png)

메인프레임의 강력한 기능은 수천 명의 사용자에 대해 수백만 개의 업데이트를 처리 하는 OLTP (온라인 트랜잭션 처리) 시스템에 자주 사용 됩니다. 이러한 응용 프로그램은 종종 트랜잭션 처리, 화면 처리 및 양식 입력을 위해 소프트웨어를 사용 합니다. 이러한 사용자는 CICS (고객 정보 컨트롤 시스템), IMS (정보 관리 시스템) 또는 트랜잭션 인터페이스 패키지 (TIP)를 사용할 수 있습니다.

그림에 표시 된 것 처럼 Azure의 TPM 에뮬레이터는 CICS 및 IMS 작업을 처리할 수 있습니다. Azure의 batch 시스템 에뮬레이터는 JCL (작업 제어 언어)의 역할을 수행 합니다. 메인프레임 데이터는 Azure SQL Database와 같은 Azure 데이터베이스로 마이그레이션됩니다. Azure Virtual Machines에서 호스트 되는 azure 서비스 또는 기타 소프트웨어를 시스템 관리에 사용할 수 있습니다.

## <a name="mainframe-compute-at-a-glance"></a>메인프레임 컴퓨팅 한 눈에 보기

Z14 메인프레임에서 프로세서는 최대 4 개의 *서랍*으로 정렬 됩니다. *서랍* 은 단순히 프로세서 및 칩셋의 클러스터입니다. 각 서랍에는 6 개의 액티브 중앙 프로세서 (CP) 칩이 있고 각 CP에는 10 개의 SC (시스템 컨트롤러) 칩이 있습니다. Intel x86 용어에는 서랍 당 6 개의 소켓, 소켓 당 10 개 코어 및 4 개의 서랍이 있습니다. 이 아키텍처는 z14에 대 한 24 소켓과 240 코어의 대략적인 값을 제공 합니다.

Fast z14 CP의 클록 속도는 5.2입니다. 일반적으로 z14는 모든 CPs와 함께 상자에 제공 됩니다. 필요에 따라 활성화 됩니다. 고객은 일반적으로 실제 사용량에도 불구 하 고 매달 4 시간 이상의 계산 시간에 대 한 요금이 부과 됩니다.

메인프레임 프로세서는 다음 유형 중 하나로 구성할 수 있습니다.

- 일반 용도 (GP) 프로세서
- 시스템 z zIIP (Integrated Information Processor)
- Linux (IFL) 프로세서용 통합 기능
- SAP (시스템 지원 프로세서)
- ICF (통합 결합 기능) 프로세서

## <a name="scaling-mainframe-compute-up-and-out"></a>메인프레임 계산 확장 및 축소

IBM 메인프레임은 최대 240 코어 (단일 시스템의 현재 z14 크기)를 확장할 수 있는 기능을 제공 합니다. 또한 IBM 메인프레임은 CF (결합 기능) 이라는 기능을 통해 확장할 수 있습니다. CF를 사용 하면 여러 메인프레임 시스템이 동일한 데이터에 동시에 액세스할 수 있습니다. CF를 사용 하 여 메인프레임 병렬 Sysplex 기술은 클러스터의 메인프레임 프로세서를 그룹화 합니다. 이 가이드를 작성 했을 때 병렬 Sysplex 기능은 각각 64 프로세서의 32을 그룹화 하 여 지원 합니다. 계산 용량을 확장 하기 위해 최대 2048 프로세서를 이러한 방식으로 그룹화 할 수 있습니다.

CF를 사용 하면 계산 클러스터에서 직접 액세스와 데이터를 공유할 수 있습니다. 잠금 정보, 캐시 정보 및 공유 데이터 리소스 목록에 사용 됩니다. 하나 이상의 CFs를 사용 하는 병렬 Sysplex은 "모두 공유" 규모 확장 계산 클러스터로 간주할 수 있습니다. 이러한 기능에 대 한 자세한 내용은 IBM 웹 사이트에서 [Ibm Z의 Parallel Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) 을 참조 하십시오.

응용 프로그램은 이러한 기능을 사용 하 여 확장 성능 및 고가용성을 제공할 수 있습니다. CICS가 CF를 사용 하 여 병렬 Sysplex를 사용 하는 방법에 대 한 자세한 내용은 [IBM cics 및 결합 기능 (기본 레드북 이상](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) )을 다운로드 하세요.

## <a name="azure-compute-at-a-glance"></a>Azure compute 한 눈에 보기

일부 사람들은 실수로 Intel 기반 서버를 메인프레임으로 강력 하 게 생각 하지 않는다고 생각 합니다. 그러나 새로운 코어 집적형 Intel 기반 시스템에는 메인프레임 만큼의 계산 용량이 있습니다. 이 섹션에서는 컴퓨팅 및 저장소에 대 한 Azure IaaS (infrastructure as a service) 옵션을 설명 합니다. Azure는 PaaS (platform as a service) 옵션도 제공 하지만이 문서에서는 유사한 메인프레임 용량을 제공 하는 IaaS 선택에 중점을 둘 수 있습니다.

Azure Virtual Machines 다양 한 크기와 형식으로 계산 능력을 제공 합니다. Azure에서 vCPU (가상 CPU)는 대략 메인프레임의 코어와 동일 합니다.

현재 Azure 가상 머신 크기 범위는 1 ~ 128 vCPUs를 제공 합니다. VM (가상 머신) 유형은 특정 워크 로드에 대해 최적화 됩니다. 예를 들어 다음 목록에는 VM 유형 (이 문서를 작성할 때 현재)과 권장 사용이 나와 있습니다.

| 크기     | 유형 및 설명                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 시리즈 | 64 vCPU 및 최대 3.5 GHz 클럭 속도를 사용 하는 일반적인 용도                           |
| E 시리즈 | 최대 64 vCPUs로 최적화 된 메모리                                                 |
| F 시리즈 | 최대 64 vCPUs 및 3 ... 7ghz 클록 속도를 사용 하 여 최적화 된 계산                       |
| H 시리즈 | HPC (고성능 컴퓨팅) 응용 프로그램에 최적화                          |
| L 시리즈 | NoSQL과 같은 데이터베이스에서 지원 되는 처리량이 높은 응용 프로그램을 위해 최적화 된 저장소 |
| M 시리즈 | 최대 128 vCPUs가 포함 된 가장 큰 계산 및 메모리 최적화 Vm                        |

사용 가능한 Vm에 대 한 자세한 내용은 [가상 머신 시리즈](https://azure.microsoft.com/pricing/details/virtual-machines/series/)를 참조 하세요.

Z14 메인프레임은 최대 240 코어를 포함할 수 있습니다. 그러나 z14 메인프레임은 단일 응용 프로그램 또는 워크 로드에 대 한 모든 코어를 거의 사용 하지 않습니다. 대신, 메인프레임은 워크 로드를 논리 파티션 (LPARs)으로 분리, LPARs에는 수 억 (초당 수백만 개의 명령) 또는 MSU (서비스 단위)의 등급이 있습니다. Azure에서 메인프레임 워크 로드를 실행 하는 데 필요한 동급의 VM 크기를 결정할 때 MIPS (또는 MSU) 등급을 고려해 야 합니다.

일반적인 예측은 다음과 같습니다.

-   vCPU 당 150 MIPS

-   1000 MIPS

지정 된 워크 로드에 대 한 올바른 VM 크기를 지정 하려면 먼저 작업에 대해 VM을 최적화 합니다. 그런 다음 필요한 vCPUs 수를 결정 합니다. 보수적인 추정치는 vCPU 당 150입니다. 예를 들어이 예측을 기반으로 16 개 vCPUs가 포함 된 F 시리즈 VM은 2400 MIPS와 L-A에서 들어오는 IBM Db2 워크 로드를 쉽게 지원할 수 있습니다.

## <a name="azure-compute-scale-up"></a>Azure 계산 확장

M 시리즈 Vm은 최대 128 vCPUs (이 문서가 작성 된 시간)까지 확장 될 수 있습니다. VCPU 당 150 MIPS의 보수적인 추정치를 사용 하는 경우 M 시리즈 VM은 약 19000 MIPS와 동일 합니다. 메인프레임의 MIPS를 추정 하는 일반적인 규칙은 프로세서 당 1000입니다. Z14 메인프레임은 최대 24 개의 프로세서를 가질 수 있으며 단일 메인프레임 시스템에 대해 약 24000 MIPS를 제공할 수 있습니다.

가장 큰 단일 z14 메인프레임은 Azure에서 사용할 수 있는 가장 큰 VM 보다 약 5000입니다. 그러나 작업을 배포 하는 방법을 비교 하는 것이 중요 합니다. 메인프레임 시스템에 응용 프로그램과 관계형 데이터베이스가 모두 있는 경우 일반적으로 동일한 물리적 메인프레임에 배포 됩니다. Azure의 동일한 솔루션은 응용 프로그램에 대해 하나의 VM을 사용 하 여 배포 되는 경우가 많으며, 데이터베이스에 대해 적절 한 크기의 별도의 VM이 있습니다.

예를 들어 M64 vCPU 시스템이 응용 프로그램을 지원 하 고 데이터베이스에 M96 vCPU가 사용 되는 경우 다음 그림에 나와 있는 것 처럼 약 150 Vcpu가 24000 필요 합니다.

![24000 MIPS의 작업 배포 비교](media/mainframe-compute-mips.png)

이 방법은 LPARs를 개별 Vm으로 마이그레이션하는 것입니다. 그런 다음 Azure는 단일 메인프레임 시스템에 배포 되는 대부분의 응용 프로그램에 필요한 크기로 쉽게 확장할 수 있습니다.

## <a name="azure-compute-scale-out"></a>Azure 계산 확장

Azure 기반 솔루션의 장점 중 하나는 규모 확장 하는 기능입니다. 크기를 조정 하면 응용 프로그램에서 거의 무제한 계산 용량을 사용할 수 있습니다. Azure는 계산 능력을 확장 하는 여러 방법을 지원 합니다.

- **클러스터 간 부하 분산.** 이 시나리오에서 응용 프로그램은 [부하 분산 장치](/azure/load-balancer/load-balancer-overview) 또는 리소스 관리자를 사용 하 여 클러스터의 여러 vm 간에 작업을 분산할 수 있습니다. 더 많은 계산 용량이 필요한 경우 추가 Vm이 클러스터에 추가 됩니다.

- **가상 머신 확장 집합입니다.** 이 버스트 시나리오에서 응용 프로그램은 VM 사용량을 기준으로 추가 [계산 리소스로](/azure/virtual-machine-scale-sets/overview) 확장할 수 있습니다. 요구가 떨어지면 확장 집합의 Vm 수를 아래로 이동 하 여 계산 능력을 효율적으로 사용할 수도 있습니다.

- **PaaS 크기 조정.** Azure PaaS 서비스는 계산 리소스를 확장 합니다. 예를 들어 [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) 는 요청 볼륨의 증가량을 충족 하기 위해 계산 리소스를 할당 합니다.

- **Kubernetes 클러스터.** Azure의 응용 프로그램은 지정 된 리소스에 대해 계산 서비스에 [Kubernetes 클러스터](/azure/aks/concepts-clusters-workloads) 를 사용할 수 있습니다. AKS (azure Kubernetes Service)는 Azure에서 Kubernetes 노드, 풀 및 클러스터를 오케스트레이션 하는 관리 되는 서비스입니다.

계산 리소스를 확장 하는 올바른 방법을 선택 하려면 Azure와 메인프레임이 어떻게 다른 지 이해 하는 것이 중요 합니다. 키는 계산 리소스에서 데이터를 공유 하는 방법 (또는의 경우)입니다. Azure에서 기본적으로 데이터는 여러 Vm에서 공유 되지 않습니다. 스케일 아웃 계산 클러스터의 여러 Vm에서 데이터 공유를 필요로 하는 경우 공유 데이터는이 기능을 지 원하는 리소스에 있어야 합니다. Azure에서 데이터 공유는 다음 섹션에 설명 된 대로 저장소를 포함 합니다.

## <a name="azure-compute-optimization"></a>Azure 계산 최적화

Azure 아키텍처에서 각 처리 계층을 최적화할 수 있습니다. 각 환경에 가장 적합 한 유형의 Vm 및 기능을 사용 합니다. 다음 그림은 Db2를 사용 하는 CICS 응용 프로그램을 지원 하기 위해 Azure에서 Vm을 배포 하는 한 가지 잠재적 패턴을 보여 줍니다. 기본 사이트에서는 가용성이 높은 프로덕션, 사전 프로덕션 및 테스트 VM이 배포됩니다. 보조 사이트는 백업 및 재해 복구용입니다.

또한 각 계층은 적절 한 재해 복구 서비스를 제공할 수 있습니다. 프로덕션 VM과 데이터베이스 VM에는 핫 복구나 웜 복구가 필요한 반면 개발 및 테스트 VM은 콜드 복구를 지원하는 경우를 예로 들 수 있습니다.

![재해 복구를 지 원하는 항상 사용 가능한 배포](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>다음 단계

- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure Virtual Machines의 메인프레임 재호스팅](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [메인프레임 저장소를 Azure로 이동](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 리소스

- [IBM Z의 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 및 결합 기능: 기본 사항 이외](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 pureScale 기능 설치에 대한 필수 사용자 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 인스턴스 명령 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 클러스터형 데이터베이스 솔루션](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [메인프레임 응용 프로그램용 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 및 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>추가 마이그레이션 리소스

- [Azure Virtual Data Center 리프트 앤 시프트 가이드](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
