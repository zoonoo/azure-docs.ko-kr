---
title: 일반적인 자동 크기 조정 패턴 개요
description: Azure에서 리소스의 크기를 자동으로 조정하는 일반적인 패턴 중 일부에 대해 알아봅니다.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 8356a8c8c31a043197485b4913b4a67d7d719778
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60534253"
---
# <a name="overview-of-common-autoscale-patterns"></a>일반적인 자동 크기 조정 패턴 개요
이 문서에서는 Azure에서 리소스의 크기를 조정하는 몇 가지 일반적인 패턴에 대해 설명합니다.

Azure Monitor 자동 크기 조정은 [가상 컴퓨터 확장 집합](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/) 및 [API Management 서비스](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)에만 적용됩니다.

## <a name="lets-get-started"></a>시작

이 문서에서는 사용자가 자동 크기 조정에 대해 잘 알고 있다고 가정합니다. [리소스 크기를 조정하려면 여기서 시작][1]할 수 있습니다. 다음은 몇 가지 일반적인 크기 조정 패턴입니다.

## <a name="scale-based-on-cpu"></a>CPU 기준 크기 조정

웹앱(/VMSS/클라우드 서비스 역할)이 있습니다. 그리고

- CPU를 기준으로 규모를 확장/축소하려고 합니다.
- 최소 개수의 인스턴스가 있는지 확인하려고도 합니다.
- 또한 확장할 수 있는 인스턴스 수의 최대 한도를 설정해야 합니다.

![CPU 기준 크기 조정][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>평일과 주말에 대해 다르게 크기 조정

웹앱(/VMSS/클라우드 서비스 역할)이 있습니다. 그리고

- 기본적으로 3개의 인스턴스를 원합니다(평일)
- 주말에 트래픽이 걸리지 않을 것으로 예상하므로 주말에는 1개 인스턴스로 축소할 수 있습니다.

![평일과 주말에 대해 다르게 크기 조정][3]

## <a name="scale-differently-during-holidays"></a>휴일 동안에 대해 다르게 크기 조정

웹앱(/VMSS/클라우드 서비스 역할)이 있습니다. 그리고

- 기본적으로 CPU 사용량을 기준으로 확장/축소하려고 합니다.
- 그러나 연말 연시(또는 비즈니스에 중요한 특정 요일)에는 기본값을 무시하고 더 많은 용량을 원하는 경우가 있습니다.

![휴일에 대해 다르게 크기 조정][4]

## <a name="scale-based-on-custom-metric"></a>사용자 지정 메트릭 기준 크기 조정

웹 프런트 엔드 및 백 엔드와 통신 하는 API 계층을 갖습니다.

- 프런트 엔드의 사용자 지정 이벤트를 기준으로 API 계층을 확장하려고 합니다(예: 장바구니의 항목 수에 따라 체크 아웃 프로세스 크기 조정).

![사용자 지정 메트릭 기준 크기 조정][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

