---
title: "Azure에서 Windows VM에 대한 VM 보존 유지 관리 | Microsoft Docs"
description: "메모리 보존 업데이트를 위한 전체 VM 마이그레이션"
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 08da5407cc5ddceeba21a558dc0de1008a566bab
ms.lasthandoff: 04/03/2017


---



# <a name="vm-preserving-maintenance-in-place-vm-migration"></a>VM 보존 유지 관리(전체 VM 마이그레이션)

대부분이 업데이트는 호스트된 VM에 영향을 미치지 않지만 구성 요소 또는 서비스에 대한 업데이트가 실행 중인 VM에 최소한의 간섭을 유발하는 경우가 있습니다(가상 컴퓨터의 전체 다시 부팅 없음).

이러한 업데이트는 원위치 실시간 마이그레이션을 가능하게 하는 기술(“메모리 보존” 업데이트)를 통해 완수됩니다. 호스트 업데이트 시 호스팅 환경(예: 기본 운영 체제)이 필요한 업데이트 및 패치를 적용하는 동안 가상 컴퓨터는 "일시 중지" 상태로 들어가 RAM의 메모리를 유지합니다.
그런 후 가상 컴퓨터는 일시 중지 후 30초 내에 다시 시작됩니다.
다시 시작된 후 가상 컴퓨터의 시계가 자동으로 동기화됩니다.

이 메커니즘을 사용하여 모든 업데이트를 배포할 수 있는 것은 아니지만 짧은 일시 중지 시간을 지정한 경우 이 방식으로 업데이트를 배포하면 가상 컴퓨터에 미치는 영향을 크게 줄일 수 있습니다.

다중 인스턴스 업데이트(가용성 집합의 VM)는 한 번에 하나의 업데이트 도메인에 적용됩니다.

가상 컴퓨터에서 실행 중인 응용 프로그램은 메타데이터 서비스 예정 이벤트를 호출하여 예정된 업데이트에 대해 알아볼 수 있습니다. 예약된 이벤트에 대한 자세한 내용은 [Azure 메타데이터 서비스 - 예약된 이벤트](../virtual-machines-scheduled-events.md)를 참조하세요.
