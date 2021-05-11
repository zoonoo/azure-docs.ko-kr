---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 0f85f2770b98507618d5cbfdec1b55f397c7f514
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101751091"
---
Azure는 디스크 스토리지 IOPS 및 MB/s 성능을 향상시킬 수 있는 기능을 제공합니다. 이를 VM(가상 머신) 및 디스크 모두에서 버스팅이라고 합니다. VM 및 디스크 버스팅을 효과적으로 활용하여 VM과 디스크에서 버스팅 성능을 향상시킬 수 있습니다.

Azure VM에 대한 버스팅과 디스크 리소스에 대한 버스팅은 서로 종속되지 않습니다. 연결된 버스트 가능 디스크를 버스트하기 위해 버스트 가능 VM이 필요하지 않습니다. 마찬가지로, VM을 버스트하기 위해 버스트 가능 VM에 연결된 버스트 디스크가 필요하지 않습니다.