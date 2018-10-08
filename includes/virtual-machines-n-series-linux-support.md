---
title: 포함 파일
description: 포함 파일
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 09/24/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: dc03f49a684bfeb43ffd8bac9f551a67f034a04f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042880"
---
## <a name="supported-distributions-and-drivers"></a>지원되는 배포판 및 버전

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA 드라이버

NCv2, NCv3 및 ND 시리즈 VM(NV 시리즈의 경우는 선택적임)용 NVIDIA CUDA 드라이버는 다음 표에 나와 있는 Linux 배포판에서만 사용할 수 있습니다. CUDA 드라이버 정보는 게시 시점의 최신 정보입니다. 최신 CUDA 드라이버에 대해서는 [NVIDIA](https://developer.nvidia.com/cuda-zone) 웹 사이트를 참조하세요. 배포에 필요한 최신 CUDA 드라이버를 설치 또는 업그레이드해야 합니다. 

> [!TIP]
> Linux VM에서 수동 CUDA 드라이버를 설치하는 대신, Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) 이미지를 배포할 수 있습니다. Ubuntu 16.04 LTS 또는 CentOS 7.4용 DSVM 에디션은 NVIDIA CUDA 드라이버, CUDA 심층 신경망 네트워크 라이브러리 및 기타 도구를 사전 설치합니다.

| 배포 | 드라이버 |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 또는 7.4<br/><br/> CentOS 기반 7.3 또는 7.4, CentOS 기반 7.4 HPC | NVIDIA CUDA 10.0, 드라이버 분기 R410 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 드라이버

Microsoft는 가상 워크스테이션으로 사용되는 NV 및 NVv2 시리즈 VM 또는 가상 응용 프로그램에 대해 NVIDIA GRID 드라이버 설치 관리자를 재배포합니다. Azure NV VM에서 다음 표에 나열된 배포판에만 이러한 GRID 드라이버를 설치합니다. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다.

| 배포 | 드라이버 |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 또는 7.4<br/><br/>CentOS 기반 7.3 또는 7.4 | NVIDIA GRID 6.2, 드라이버 분기 R390|



> [!WARNING] 
> Red Hat 제품에 대한 타사 소프트웨어 설치는 Red Hat 지원 조건에 영향을 줄 수 있습니다. [Red Hat 기술 자료 문서](https://access.redhat.com/articles/1067)를 참조하세요.
>
