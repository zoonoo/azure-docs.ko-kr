---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80420973"
---
이 문서에서는 AzCopy를 사용하여 로컬 컴퓨터에서 Azure 관리 디스크로 VHD를 업로드하거나 관리 디스크를 다른 지역으로 복사하는 방법을 설명합니다. 직접 업로드 프로세스를 사용하면 최대 32TiB 크기의 VHD를 관리 디스크에 직접 업로드할 수 있습니다. 현재 직접 업로드는 표준 HDD, 표준 SSD 및 프리미엄 SSD 관리 디스크에 대해 지원됩니다. 하지만 울트라 디스크에서는 지원되지 않습니다.

Azure에서 IaaS VM에 대한 백업 솔루션을 제공하는 경우 직접 업로드를 사용하여 관리 디스크로 고객 백업을 복원하는 것이 좋습니다. Azure 외부의 원본에서 VHD를 업로드하는 경우 속도는 로컬 대역폭에 따라 다릅니다. Azure VM에서 업로드하거나 복사할 때 대역폭은 표준 HDD와 동일합니다.