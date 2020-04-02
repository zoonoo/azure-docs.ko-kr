---
title: Azure 스팟 VM 및 스케일 세트 인스턴스에 대한 오류 코드
description: 스팟 VM 및 스케일 세트 인스턴스를 사용할 때 볼 수 있는 오류 코드에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547809"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>스팟 VM 및 배율 집합에 대한 오류 메시지

스팟 VM 및 배율 집합을 사용할 때 받을 수 있는 몇 가지 오류 코드는 다음과 같습니다.


| Key | 메시지 | Description |
|-----|---------|-------------|
| SkuNotAvailable | 요청된 계층의\<리소스\>' 리소스 ' 현재\<\>사용할 수\<없습니다\>위치 ' 위치 ' 구독ID에 대 한. 다른 계층을 시도하거나 다른 위치에 배포하십시오. | 이 위치에 VM 또는 배율 집합 인스턴스를 만들 수 있는 Azure 스팟 용량이 부족합니다. |
| 퇴거 정책CanbesetOnly온Azure스팟가상머신  |  제거 정책은 Azure 스팟 가상 컴퓨터에서만 설정할 수 있습니다. | 이 VM은 스팟 VM이 아니므로 제거 정책을 설정할 수 없습니다. |
| AzureSpotVMNot지원되는사용률 집합  |  Azure 스팟 가상 컴퓨터는 가용성 집합에서 지원되지 않습니다. | 스팟 VM을 사용하거나 가용성 집합에서 VM을 사용하도록 선택해야 하며 둘 다 선택할 수 없습니다. |
| AzureSpot특징NotEnabledFor구독  |  Azure 스팟 기능으로 구독을 사용할 수 없습니다. | 스팟 VM을 지원하는 구독을 사용합니다. |
| VM우선 순위 적용 할 수 없습니다  |  지정된 우선 순위{0}값 ' ' 가상{1}컴퓨터를 만들 때 우선 순위를 지정 하지 않았기 때문에 ' 가상 컴퓨터에 적용할 수 없습니다. | VM을 만들 때 우선 순위를 지정합니다. |
| 스팟프라이스그레이터보다제공맥스프라이스  |  동작을 수행할{0}수 없는 경우 '{1} ' 제공 된 최대 가격{2} ' USD' Azure 현물 VM 크기에 대 한 현재 현물 가격 'USD' 보다 낮은 '{3}' . | 더 높은 최대 가격을 선택합니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 또는 [Windows의](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)가격 정보를 참조하십시오.|
| 맥스프라이스밸류유효  |  유효하지 않은 최대 가격 값입니다. 최대 가격에 대해 지원되는 유일한 값은 -1 또는 소수점보다 0보다 큽니다. 최대 가격 값이 -1은 Azure Spot 가상 시스템이 가격 상의 이유로 제거되지 않음을 나타냅니다. | 유효한 최대 가격을 입력합니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 또는 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)가격 책정을 참조하십시오. |
| 맥스프라이스체인지허용되지허용된VM | VM '이{0}현재 할당된 경우 최대 가격 변경은 허용되지 않습니다. 거래 할당 을 다시 시도하십시오. | 최대 가격을 변경할 수 있도록 VM을 거래 할당 을 중지합니다. |
| 최대가격변경허용되지 않음 | 최대 가격 변경은 허용되지 않습니다. | 이 VM의 최대 가격은 변경할 수 없습니다. |
| AzureSpotIsNot지원포이API버전  |  Azure 스팟은 이 API 버전에서 지원되지 않습니다. | API 버전은 2019-03-01이어야 합니다. |
| AzureSpotNot지원포이VMSize  |  Azure 스팟은 이 VM {0}크기에 대해 지원되지 않습니다. | 다른 VM 크기를 선택합니다. 자세한 내용은 [스팟 가상 컴퓨터를](./linux/spot-vms.md)참조하십시오. |
| 맥스프라이스서포트전용포아스팟가상머신  |  최대 가격은 Azure 스팟 가상 시스템에 대해서만 지원됩니다. | 자세한 내용은 [스팟 가상 컴퓨터를](./linux/spot-vms.md)참조하십시오. |
| 이동자원와 AzureSpotVMNot지원되지 않음  |  이동 리소스 요청에는 Azure Spot 가상 시스템이 포함되어 있습니다. 현재는 지원되지 않습니다. 가상 시스템 ID에 대한 오류 세부 정보를 확인하십시오. | 스팟 VM은 이동할 수 없습니다. |
| 이동자원와AzureSpotVmss지원되지 않음  |  이동 리소스 요청에는 Azure Spot 가상 시스템 규모 집합이 포함되어 있습니다. 현재는 지원되지 않습니다. 가상 시스템 스케일 세트 ID에 대한 오류 세부 정보를 확인하십시오. | 스팟 축척 세트 인스턴스를 이동할 수 없습니다. |
| 에이치메랄OS디스크Not지원포스팟VM | 스팟 VM에는 임시 OS 디스크가 지원되지 않습니다. | 스팟 VM에 일반 OS 디스크를 사용합니다. |
| AzureSpotVMNot지원인VmswithVMOrchestrationmode | Azure 스팟 가상 컴퓨터는 VM 오케스트레이션 모드를 통해 가상 시스템 규모 집합에서 지원되지 않습니다. | 스팟 인스턴스를 사용하려면 오케스트레이션 모드를 가상 시스템 축척 집합으로 설정합니다. |


**다음 단계** 자세한 내용은 [스팟 가상 컴퓨터를](./linux/spot-vms.md)참조하십시오.