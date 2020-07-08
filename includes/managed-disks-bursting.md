---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 2af8b429b6addf6da32b34773525c51a36624e78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242089"
---
Azure에서는 Virtual Machines와 디스크 모두에서 버스트로 참조 되는 디스크 저장소 IOPS 및 m b/s 성능을 향상 시키는 기능을 제공 합니다. 버스팅는 예기치 않은 디스크 트래픽 처리 또는 일괄 처리 작업 처리 등과 같은 다양 한 시나리오에서 유용 합니다. Vm 및 디스크 수준 버스트를 효과적으로 활용 하 여 VM과 디스크에서 뛰어난 기준선 및 버스트 성능을 달성할 수 있습니다. 이러한 방식으로 vm과 디스크 모두에서 뛰어난 기본 성능과 버스트 성능을 달성할 수 있습니다. 

디스크 및 Vm에 대 한 버스트는 서로 독립적입니다. 버스트 디스크가 있는 경우 디스크 버스트를 허용 하는 버스트 VM이 필요 하지 않습니다. 버스트 VM이 있는 경우 VM 버스트를 허용 하는 버스트 디스크가 필요 하지 않습니다. 