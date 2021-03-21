---
title: 파일 포함
description: 파일 포함
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 0f85f2770b98507618d5cbfdec1b55f397c7f514
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101751091"
---
Azure는 디스크 저장소 IOPS 및 m b/초 성능을 향상 시킬 수 있는 기능을 제공 합니다 .이를 VM (가상 머신) 및 디스크 모두에 대해 버스트 라고 합니다. VM 및 디스크 버스트를 효과적으로 활용 하 여 Vm과 디스크에서 버스트 성능을 향상 시킬 수 있습니다.

Azure Vm 및 디스크 리소스에 대 한 버스트는 서로 종속 되지 않습니다. 연결 된 버스트 가능 디스크를 버스트 하기 위해 버스트 가능 VM이 필요 하지 않습니다. 마찬가지로, VM을 버스트 하기 위해 버스트 가능 VM에 연결 된 버스트 디스크가 필요 하지 않습니다.