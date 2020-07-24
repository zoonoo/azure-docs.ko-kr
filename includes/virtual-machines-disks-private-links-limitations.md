---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be014a257a68a266a5946bf5822b094c5a968f6e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535595"
---
- 하나의 가상 네트워크만 디스크 액세스 개체에 연결할 수 있습니다.
- 가상 네트워크를 연결하려면 가상 네트워크가 디스크 액세스 개체와 동일한 구독에 있어야 합니다.
- 동일한 디스크 액세스 개체를 사용하여 최대 10개의 디스크 또는 스냅샷을 동시에 가져오거나 내보낼 수 있습니다.
- 가상 네트워크를 디스크 액세스 개체에 연결하는 수동 승인을 요청할 수 없습니다.
- 디스크 액세스 개체와 연결된 증분 스냅샷에 대해서는 차등 기능이 지원되지 않습니다.