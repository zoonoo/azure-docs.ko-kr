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
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420973"
---
이 문서에서는 로컬 컴퓨터에서 Azure 관리 디스크에 VHD를 업로드하거나 AzCopy를 사용하여 관리디스크를 다른 지역으로 복사하는 방법을 설명합니다. 이 프로세스, 직접 업로드, 또한 관리 되는 디스크에 직접 크기 32 TiB까지 VHD를 업로드할 수 있습니다. 현재 표준 HDD, 표준 SSD 및 프리미엄 SSD 관리 디스크에 대해 직접 업로드가 지원됩니다. 아직 울트라 디스크에 대 한 지원 되지 않습니다.

Azure에서 IaaS VM에 대한 백업 솔루션을 제공하는 경우 직접 업로드를 사용하여 관리되는 디스크에 대한 고객 백업을 복원하는 것이 좋습니다. Azure 외부의 소스에서 VHD를 업로드할 때 속도는 로컬 대역폭에 따라 달라집니다. Azure VM에서 업로드하거나 복사할 때 대역폭은 표준 HDD와 동일합니다.