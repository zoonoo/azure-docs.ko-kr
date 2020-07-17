---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80420973"
---
이 문서에서는 AzCopy를 사용 하 여 로컬 컴퓨터에서 Azure 관리 디스크로 VHD를 업로드 하거나 관리 디스크를 다른 지역에 복사 하는 방법을 설명 합니다. 직접 업로드 프로세스를 사용 하면 최대 32 TiB 크기의 VHD를 관리 되는 디스크에 직접 업로드할 수 있습니다. 현재 직접 업로드는 표준 HDD, 표준 SSD 및 프리미엄 SSD 관리 디스크에 대해 지원 됩니다. 그러나 ultra disks에는 지원 되지 않습니다.

Azure에서 IaaS Vm에 대 한 백업 솔루션을 제공 하는 경우 직접 업로드를 사용 하 여 관리 디스크에 고객 백업을 복원 하는 것이 좋습니다. Azure 외부의 원본에서 VHD를 업로드 하는 경우 속도는 로컬 대역폭에 따라 달라 집니다. Azure VM에서 업로드 하거나 복사할 때 대역폭은 표준 Hdd와 동일 합니다.