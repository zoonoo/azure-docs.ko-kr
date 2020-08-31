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
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420291"
---
- 하나의 가상 네트워크만 디스크 액세스 개체에 연결할 수 있습니다.
- 가상 네트워크를 연결하려면 가상 네트워크가 디스크 액세스 개체와 동일한 구독에 있어야 합니다.
- 동일한 디스크 액세스 개체를 사용하여 최대 10개의 디스크 또는 스냅샷을 동시에 가져오거나 내보낼 수 있습니다.
- 가상 네트워크를 디스크 액세스 개체에 연결하는 수동 승인을 요청할 수 없습니다.
- 디스크 액세스 개체와 연결된 증분 스냅샷에 대해서는 차등 기능이 지원되지 않습니다.
- AzCopy를 사용하여 프라이빗 링크로 보호되는 디스크/스냅샷의 VHD를 스토리지 계정에 다운로드할 수 없습니다. 그러나 AzCopy를 사용하여 VHD를 VM에 다운로드할 수는 있습니다.
