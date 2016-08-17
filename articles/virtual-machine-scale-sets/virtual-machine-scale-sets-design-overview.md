<properties
	pageTitle="확장용 가상 컴퓨터 크기 집합 설계 | Microsoft Azure"
	description="확장용 가상 컴퓨터 크기 집합을 설계하는 방법에 대해 알아보십시오."
	keywords="linux 가상 컴퓨터, 가상 컴퓨터 크기 집합" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="gatneil"/>

# 확장용 VM 크기 집합 설계

이 항목은 가상 컴퓨터 크기 집합을 설계할 때 고려할 사항에 대해 논의합니다. 가상 컴퓨터 크기 집합에 대한 자세한 내용은 [가상 컴퓨터 크기 집합 개요](virtual-machine-scale-sets-overview.md)를 참조하십시오.


## 저장소

크기 집합은 저장소 계정을 사용하여 집합에 있는 VM의 OS 디스크를 저장합니다. 저장소 계정당 20개 미만의 VM을 유지하는 것이 좋습니다. 또한 저장소 계정 이름의 시작하는 문자에 알파벳을 고르게 사용하는 것이 좋습니다. 그러면 여러 내부 시스템에서 부하를 분산할 수 있습니다. 예를 들어 다음 템플릿에서는 uniqueString ARM 템플릿 함수를 사용하여 저장소 계정 이름 앞에 추가되는 접두사를 생성합니다. [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## 오버프로비전

2016년 3월 30일 API 버전부터 VM 크기 집합은 기본적으로 "오버프로비전" VM이 됩니다. 즉, 크기 집합이 실제로 사용자가 요청한 것보다 많은 VM을 스핀업한 다음 마지막으로 스핀업된 불필요한 VM을 삭제합니다. 이때 하나의 VM이라도 성공적으로 프로비전되지 않을 경우에도 Azure Resource Manager에서 전체 배포가 "실패"한 것으로 간주하기 때문에 프로비전 성공률이 향상됩니다. 이러한 추가 VM에 대한 추가 비용은 청구되지 않으며 할당량 한도에 포함되지 않습니다.

이 경우 프로비전 성공률은 향상되지만 예고 없이 사라지는 VM을 처리하도록 설계되지 않은 응용 프로그램에서 동작이 혼란스러워질 수 있습니다. 오버프로비전을 해제하려면 템플릿에 "overprovision": "false" 문자열이 있어야 합니다.

오버프로비전을 해제할 경우 저장소 계정당 많은 비율의 VM을 유지할 수 있지만 40개를 초과하지 않는 것이 좋습니다.


## 제한
사용자 지정 이미지에 구축한 크기 집합(사용자가 구축한 크기 집합)은 하나의 저장소 계정 안에 모든 OS 디스크 VHD를 만들어야 합니다. 그 결과 사용자 지정 이미지에 구축하는 크기 집합의 최대 추천 VM 수는 20개입니다. 오버프로비전을 해제할 경우 40개를 초과할 수 있습니다.

플랫폼 이미지에 구축된 크기 집합은 현재 100개 VM으로 제한되어 있습니다.(이 크기에는 5개의 저장소 계정을 권장합니다).

이러한 한도보다 더 많은 Vm이 있는 경우 여러 규모 집합을 배포해야 합니다. [방법에 대한 예를 보려면 템플릿을 참조하십시오.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)

<!---HONumber=AcomDC_0803_2016-->