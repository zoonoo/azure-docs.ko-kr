<properties
	pageTitle="여러 가상 컴퓨터 만들기 | Microsoft Azure"
	description="Windows에서 여러 가상 컴퓨터 만들기에 대한 옵션"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="guybo"/>

# 여러 Azure 가상 컴퓨터 만들기

HPC(고성능 컴퓨팅), 대규모 데이터 분석, 확장 가능하며 보통 상태 정보를 저장하지 않는 중간 계층 또는 백 엔드 서버(예: webserver), 분산 데이터베이스와 같이 많은 수의 유사한 가상 컴퓨터를 만들어야 할 다양한 시나리오가 있습니다.

이 문서는 일련의 VM을 수동으로 만드는 단순한 경우(십여 개 이상의 VM을 만들어야 할 경우 원활한 확장이 어려움)와 달리 Azure에서 여러 VM을 만들 때 사용 가능한 옵션에 대해 설명합니다.

여러 개의 유사한 VM 만들기를 최적화하는 한 가지 방법은 _리소스 루프_의 Azure Resource Manager 구조를 사용하는 것입니다.

## 리소스 루프

리소스 루프는 루프에 유사하게 구성된 리소스 집합을 만들 수 있는 Azure Resource Manager 템플릿의 단축 구문입니다. 리소스 루프를 사용하면 여러 개의 저장소 계정, 네트워크 인터페이스, 가상 컴퓨터 등을 만들 수 있습니다. 리소스 루프에 대한 자세한 내용은 [리소스 루프를 사용하여 가용성 집합에 VM 만들기](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/)를 참조하세요.

## 규모의 과제

리소스 루프를 사용하면 대규모 클라우드 인프라를 손쉽게 만들고 더욱 간결한 템플릿을 사용할 수 있지만 특정 과제를 해결해야 합니다. 예를 들어 리소스 루프를 사용하여 100대의 가상 컴퓨터를 만들 경우나 VM을 정의할 경우 NIC와 VM 및 저장소 계정 간 상관 관계를 만들어야 합니다. 저장소 계정 수에 대해 VM 수가 달라질 수 있으며, 그에 따라 리소스 루프 크기도 달라집니다. 이러한 문제는 해결할 수 있지만 규모에 따라 복잡성이 크게 증가합니다.

인프라를 탄력적으로 확장해야 할 경우(예: 워크로드에 대응하여 VM 수를 자동으로 증가 또는 감소하는 자동 크기 조정)에도 또 다른 문제가 발생합니다. VM은 수 변경(규모 확장 및 규모 감축)에 대한 통합 메커니즘을 제공하지 않습니다. VM을 제거하여 규모를 감축할 경우 업데이트 및 장애 도메인에서 VM의 균형을 유지하여 고가용성을 보장하는 것도 어려운 작업입니다.

마지막으로, 리소스 루프가 단축 구문이지만 리소스 루프를 사용하면 리소스를 만들기 위한 여러 호출이 기본 패브릭으로 전달됩니다. 여러 호출이 유사한 리소스를 만들 경우 Azure가 이 디자인을 개선하고 최적화하여 배포 안정성 및 성능을 개선할 암시적 기회가 있습니다. 여기에 _가상 컴퓨터 크기 집합_이 유용합니다.

## 가상 컴퓨터 크기 집합

가상 컴퓨터 크기 집합은 동일한 VM 집합을 배포하고 관리하기 위한 Azure 계산 리소스입니다. 모든 VM을 동일하게 구성한 경우 집합의 VM 수를 변경하기만 하여 VM 크기 집합을 간단히 감축 및 확장할 수 있으며 워크로드 요구 사항에 따라 자동으로 크기를 조정하도록 구성할 수 있습니다.

계산 리소스 크기를 조정해야 하는 응용 프로그램의 경우 크기 조정 작업은 장애 도메인 및 업데이트 도메인 간에 암시적으로 균형이 조정됩니다.

크기 집합에서는 IC, VM과 같은 여러 리소스 간 상관 관계를 구성하지 않고 네트워크, 저장소, 가상 컴퓨터, 확장 속성을 중앙 집중적으로 구성할 수 있습니다.

VM 크기 집합에 대한 소개는 [제품 페이지](https://azure.microsoft.com/services/virtual-machine-scale-sets/)를 참조하세요. 자세한 내용은 [설명서](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)를 참조하세요.

<!---HONumber=AcomDC_0504_2016-->