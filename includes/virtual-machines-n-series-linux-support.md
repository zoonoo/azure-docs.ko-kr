---
title: 포함 파일
description: 포함 파일
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9f0d694badaa6f4484a13364c6a56aee2ad1dcfb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123941"
---
## <a name="supported-distributions-and-drivers"></a>지원되는 배포판 및 버전

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA 드라이버

NC, NCv2, NCv3, ND 및 NDv2 시리즈 VM(NV 시리즈의 경우 선택적임)용 NVIDIA CUDA 드라이버는 다음 표에 나와 있는 Linux 배포판에서만 사용할 수 있습니다. CUDA 드라이버 정보는 게시 시점의 최신 정보입니다. 최신 CUDA 드라이버에 대해서는 [NVIDIA](https://developer.nvidia.com/cuda-zone) 웹 사이트를 참조하세요. 배포에 필요한 최신 CUDA 드라이버를 설치 또는 업그레이드해야 합니다. 

> [!TIP]
> Linux VM에서 수동 CUDA 드라이버를 설치하는 대신, Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) 이미지를 배포할 수 있습니다. Ubuntu 16.04 LTS 또는 CentOS 7.4용 DSVM 에디션은 NVIDIA CUDA 드라이버, CUDA 심층 신경망 네트워크 라이브러리 및 기타 도구를 사전 설치합니다.

| 배포 | 드라이버 |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> CentOS 기반 7.3, 7.4, 7.5, 7.6, CentOS 기반 7.4 HPC | NVIDIA CUDA 10.1, 드라이버 분기 R418 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 드라이버

Microsoft는 가상 워크스테이션으로 사용되는 NV 및 NVv2 시리즈 VM 또는 가상 애플리케이션에 대해 NVIDIA GRID 드라이버 설치 관리자를 재배포합니다. Azure NV VM에서 다음 표에 나열된 운영 체제에만 이러한 GRID 드라이버를 설치합니다. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다. NVIDIA vGPU 소프트웨어 라이선스 서버를 설정할 필요는 없습니다.

| 배포 | 드라이버 |
| --- | -- |
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/>CentOS 기반 7.3, 7.4, 7.5, 7.6 | NVIDIA GRID 8.0, 드라이버 분기 R418|

> [!WARNING] 
> Red Hat 제품에 대한 타사 소프트웨어 설치는 Red Hat 지원 조건에 영향을 줄 수 있습니다. [Red Hat 기술 자료 문서](https://access.redhat.com/articles/1067)를 참조하세요.
>
