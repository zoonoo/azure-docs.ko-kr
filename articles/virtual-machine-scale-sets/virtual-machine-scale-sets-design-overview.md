---
title: "확장용 가상 컴퓨터 크기 집합 설계 | Microsoft Docs"
description: "확장용 가상 컴퓨터 크기 집합을 설계하는 방법에 대해 알아보십시오."
keywords: "linux 가상 컴퓨터, 가상 컴퓨터 크기 집합"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d4821bf5b7e3527e9d130e24c81d90368f395d30


---
# <a name="designing-vm-scale-sets-for-scale"></a>확장용 VM 크기 집합 설계
이 항목은 가상 컴퓨터 크기 집합을 설계할 때 고려할 사항에 대해 논의합니다. 가상 컴퓨터 크기 집합에 대한 자세한 내용은 [가상 컴퓨터 크기 집합 개요](virtual-machine-scale-sets-overview.md)를 참조하세요.

## <a name="storage"></a>저장소
크기 집합은 저장소 계정을 사용하여 집합에 있는 VM의 OS 디스크를 저장합니다. 저장소 계정당 20개 미만의 VM을 유지하는 것이 좋습니다. 또한 저장소 계정 이름의 시작하는 문자에 알파벳을 고르게 사용하는 것이 좋습니다. 그러면 여러 내부 시스템에서 부하를 분산할 수 있습니다. 예를 들어 다음 템플릿에서는 uniqueString Resource Manager 템플릿 함수를 사용하여 저장소 계정 이름 앞에 추가되는 접두사를 생성합니다. [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).

## <a name="overprovisioning"></a>오버프로비전
"2016-03-30" API 버전부터 VM 크기 집합은 기본적으로 "오버프로비전" VM이 됩니다. 오버프로비전을 켜면 크기 집합은 실제로 사용자가 요청한 것보다 더 많은 VM을 작동한 다음 마지막에 작동된 추가 VM을 삭제합니다. 오버프로비전은 프로비전 성공률을 높입니다. 이러한 추가 VM에 대한 추가 비용은 청구되지 않으며 할당량 한도에 포함되지 않습니다.

오버프로비전을 사용하면 프로비전 성공률이 향상되지만 예고 없이 사라지는 VM을 처리하도록 설계되지 않은 응용 프로그램에서 동작이 혼란스러워질 수 있습니다. 오버프로비전을 해제하려면 템플릿에 "overprovision": "false" 문자열이 있어야 합니다. 자세한 내용은 [VM 크기 집합 REST API 설명서](https://msdn.microsoft.com/library/azure/mt589035.aspx)에서 찾을 수 있습니다.

오버프로비전을 해제할 경우 저장소 계정당 많은 비율의 VM을 유지할 수 있지만 40개를 초과하지 않는 것이 좋습니다.

## <a name="limits"></a>제한
사용자 지정 이미지에 구축한 크기 집합(사용자가 구축한 크기 집합)은 하나의 저장소 계정 안에 모든 OS 디스크 VHD를 만들어야 합니다. 그 결과 사용자 지정 이미지에 구축하는 크기 집합의 최대 추천 VM 수는 20개입니다. 오버프로비전을 해제할 경우 40개를 초과할 수 있습니다.

플랫폼 이미지에 구축된 크기 집합은 현재 100개 VM으로 제한되어 있습니다.(이 크기에는 5개의 저장소 계정을 권장합니다).

이러한 한도보다 더 많은 VM이 있는 경우 [이 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)에 나오는 것처럼 여러 크기 집합을 배포해야 합니다.




<!--HONumber=Nov16_HO3-->


