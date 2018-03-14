---
title: "포함 파일"
description: "포함 파일"
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>지원되는 배포판 및 버전

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC, NCv2, NCv3 및 ND 시리즈 - NVIDIA CUDA 드라이버
| 배포 | 드라이버 |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 또는 7.4<br/><br/> CentOS 7.3 또는 7.4 | NVIDIA CUDA 9.1, 드라이버 분기 R390 |

> [!IMPORTANT]
> 배포에 필요한 최신 CUDA 드라이버를 설치 또는 업그레이드해야 합니다. 버전 R390보다 오래된 드라이버의 업데이트된 Linux 커널에 문제가 있을 수 있습니다.
>

### <a name="nv-series---nvidia-grid-drivers"></a>NV 시리즈 - NVIDIA GRID 드라이버

| 배포 | 드라이버 |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS 기반 7.3 | NVIDIA GRID 5.2, 드라이버 분기 R384|

> [!NOTE]
> Microsoft는 NV VM용 NVIDIA GRID 드라이버 설치 관리자를 재배포합니다. Azure NV VM에 이 GRID 드라이버만 설치하십시오. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다.
>

> [!WARNING] 
> Red Hat 제품에 대한 타사 소프트웨어 설치는 Red Hat 지원 조건에 영향을 줄 수 있습니다. [Red Hat 기술 자료 문서](https://access.redhat.com/articles/1067)를 참조하세요.
>
