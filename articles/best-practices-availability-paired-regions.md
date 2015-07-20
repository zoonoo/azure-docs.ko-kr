<properties
	pageTitle="Azure 지역 쌍으로 비즈니스 연속성 개선"
	description="지역 쌍을 사용하여 데이터 센터 오류 중 응용 프로그램의 복원성을 유지할 수 있습니다."
	services="multiple"
	documentationCenter=""
	authors="rboucher"
	manager="jwhit"
	editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2015"
    ms.author="robb"/>

# Azure 지역 쌍을 사용하여 가용성 향상

## Azure 쌍을 이루는 지역 설명

Azure는 전 세계 여러 지역에서 작동합니다. Azure 지리적 위치는 하나 이상의 Azure 지역을 포함하는 전 세계의 정의된 영역입니다. Azure 지역은 하나 이상의 데이터 센터를 포함하는 지리적 위치 내 영역입니다.

각 Azure 지역은 같은 지리적 위치 내에 있는 다른 지역과 쌍을 이루어(해당 지리적 위치 외부에 있는 지역과 쌍을 이루는 브라질 남부 제외) 지역 쌍을 구성합니다.


![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

그림 1 - Azure 지역 쌍 다이어그램



| Geography | 쌍을 이루는 지역 | |
| :-------------| :-------------   | :-------------   |
| 북아메리카 | 미국 중북부 | 미국 중남부 |
| 북아메리카 | 미국 동부 | 미국 서부 |
| 북아메리카 | 미국 동부 2 | 미국 중부 |
| 유럽 | 북유럽 | 서유럽 |
| 아시아 | 동남아시아 | 동아시아 |
| 중국 | 중국 동부 | 중국 북부 |
| 일본 | 일본 동부 | 일본 서부 |
| 브라질 | 브라질 남부(1) | 미국 중남부 |
| 오스트레일리아 | 오스트레일리아 동부 | 오스트레일리아 남동부|
| 미국 정부 | 미국 정부 아이오와 | 미국 정부 버지니아 |

표 1 - Azure 지역 쌍 매핑

> (1) 브라질 남부는 해당 지리적 위치 외부에 있는 지역과 쌍을 이루기 때문에 고유합니다. 브라질 남부의 보조 지역은 미국 중남부이지만 미국 중남부의 보조 지역은 브라질 남부가 아닙니다.

Azure의 격리 및 가용성 정책을 활용하려면 지역 쌍 간에 작업을 복제하는 것이 좋습니다. 예를 덜어 계획된 Azure 시스템 업데이트는 쌍을 이루는 지역 간에 순차적으로 배포됩니다. 즉, 흔하지 않은 업데이트 오류가 발생한 경우에도 두 지역이 동시에 영향을 않습니다. 또한 거의 발생할 가능성이 없는 광범위한 중단 시 모든 쌍에서 하나 이상의 지역에 대한 복구 우선 순위가 지정됩니다.

## 지역 쌍 예제
아래 그림 2에서는 가상의 지역 쌍을 보여 줍니다. 녹색 숫자는 세 개의 Azure 서비스(Azure 계산, 저장소 및 데이터베이스)에 대한 지역 간 활동 및 이러한 서비스가 지역 간에 복제되도록 구성된 방식을 강조합니다. 쌍을 이루는 지역에 걸친 배포의 고유한 이점은 주황색 숫자로 강조 표시되어 있습니다.


![쌍을 이루는 지역의 이점 개요](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

그림 2 – 가상의 Azure 지역 쌍

![1 녹색](./media/best-practices-availability-paired-regions/1Green.png) **Azure 계산** – 재해 중에 다른 지역의 리소스를 사용할 수 있도록 사전에 추가 계산 리소스를 프로비전해야 합니다. 재해 복구를 위해 쌍을 이루는 지역에 이러한 리소스를 배포하는 것이 좋습니다.

![2 녹색](./media/best-practices-availability-paired-regions/2Green.png) **Azure 저장소** - GRS(지역 중복 저장소)는 Azure 저장소 계정을 만들 때 기본적으로 구성됩니다. GRS를 사용하면 주 지역 및 쌍을 이루는 지역에서 각각 세 번씩 데이터가 복제됩니다. GRS는 쌍을 이루는 지역으로의 복제만 허용하며 서로 다르게 구성될 수 없습니다. 자세한 내용은 [Azure 저장소 중복 옵션](../storage/storage-redundancy.md)을 참조하세요.


![3 녹색](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL 데이터베이스** – Azure SQL 지리에서 복제를 사용하면 다른 Microsoft Azure 데이터베이스 서버로의 비동기 트랜잭션 복제를 구성할 수 있습니다. Standard 지역에서 복제는 쌍을 이루는 영역으로의 비동기 복제만 허용합니다. Premium 지리에서 복제를 사용하면 전 세계 모든 지역으로의 복제를 구성할 수 있습니다. 그러나 재해 복구를 위해 쌍을 이루는 지역에 이러한 리소스를 배포하는 것이 좋습니다. 자세한 내용은 [Azure SQL 데이터베이스의 지역에서 복제](https://msdn.microsoft.com/library/azure/dn783447.aspx)를 참조하세요.

![4 녹색](./media/best-practices-availability-paired-regions/4Green.png) **독립적인 관리 서비스** - ARM(Azure 리소스 관리자)은 기본적으로 지역 간 서비스 관리 구성 요소의 논리적 격리를 제공하므로 별도 구성이 필요하지 않습니다. 따라서 하나의 지역에서 발생한 논리적 오류가 다른 지역에 영향을 줄 가능성이 거의 없습니다.

## 쌍을 이루는 지역의 이점

그림 2 참조

![5 주황색](./media/best-practices-availability-paired-regions/5Orange.png) **물리적 격리** – 가능한 경우 Azure는 지역 쌍 내 데이터 센터 간에 약 483Km(300마일) 이상 격리하는 것이 좋습니다. 물론 모든 지리적 위치에서 이러한 격리가 가능한 것은 아닙니다. 물리적 데이터 센터 격리는 자연 재해, 내전, 정전 또는 물리적 네트워크 중단으로 인해 두 지역 모두가 동시에 영향을 받을 수 있는 가능성을 줄여 줍니다. 격리는 지리적 위치 내의 제약 조건(지리적 크기, 전력/네트워크 인프라 가용성, 규정 등)에 따라 달라집니다.

![6 주황색](./media/best-practices-availability-paired-regions/6Orange.png) **일관된 응용 프로그램 상태** - 쌍을 이루는 지역으로의 복제만 허용하는 서비스(예: 지역 중복 저장소)를 사용하는 경우 다른 모든 서비스도 쌍을 이루는 지역을 복제 대상으로 지정해야 합니다. 이렇게 하면 장애 조치(failover) 중에 응용 프로그램 상태가 일관적으로 유지됩니다.

![7 주황색](./media/best-practices-availability-paired-regions/7Orange.png) **지역 복구 순서** – 광범위한 중단 시 모든 쌍에서 하나 지역에 대한 복구 우선 순위가 지정됩니다. 쌍을 이루는 지역에 배포된 응용 프로그램은 지역 중 하나가 우선 순위에 따라 복구되도록 해줍니다. 쌍을 이루지 않는 지역에 응용 프로그램이 배포된 경우에는 복구가 지연될 수 있으며, 최악의 경우 선택한 지역이 마지막에 복구되는 두 지역이 될 수 있습니다.

![8 주황색](./media/best-practices-availability-paired-regions/8Orange.png) **순차적 업데이트** – 계획된 Azure 시스템 업데이트는 가동 중지 시간, 버그 영향 및 흔치 않은 불량 업데이트 시의 논리적 오류를 최소화하기 위해 쌍을 이루는 지역에 순차적으로(동시가 아님) 롤아웃됩니다.


![9 주황색](./media/best-practices-availability-paired-regions/9Orange.png) **데이터 상주** – 지역은 세금 및 법률 적용 목적의 데이터 상주 요구 사항을 충족하기 위해 동일한 지리적 위치 내에 쌍으로(브라질 남부 제외) 상주합니다.

<!---HONumber=July15_HO2-->