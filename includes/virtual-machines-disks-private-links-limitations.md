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
ms.openlocfilehash: 28717deadd8d842a68ab6ae6b52093f0117ad2c7
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630032"
---
- 하나의 가상 네트워크만 디스크 액세스 개체에 연결할 수 있습니다.
- 가상 네트워크를 연결하려면 가상 네트워크가 디스크 액세스 개체와 동일한 구독에 있어야 합니다.
- 동일한 디스크 액세스 개체를 사용하여 최대 10개의 디스크 또는 스냅샷을 동시에 가져오거나 내보낼 수 있습니다.
- 가상 네트워크를 디스크 액세스 개체에 연결하는 수동 승인을 요청할 수 없습니다.
- 디스크 액세스 개체와 연결된 경우에는 증분 스냅샷을 내보낼 수 없습니다.

