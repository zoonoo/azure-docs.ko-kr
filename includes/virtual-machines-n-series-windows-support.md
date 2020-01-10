---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ab68fc6533be5e3241de2e49652251fea5fe2f7d
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780635"
---
## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla(CUDA) 드라이버

NC, NCv2, NCv3, ND 및 NDv2 시리즈 VM(NV 시리즈의 경우는 선택적임)용 NVIDIA Tesla(CUDA) 드라이버는 다음 표에 나와 있는 운영 체제에서만 사용할 수 있습니다. 드라이버 다운로드 링크는 발표 시점에 제공된 링크입니다. 최신 드라이버에 대해서는 [NVIDIA](https://www.nvidia.com/) 웹 사이트를 참조하세요.

> [!TIP]
> Windows Server VM에서 수동 CUDA 드라이버를 설치하는 대신, Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) 이미지를 배포할 수 있습니다. Windows Server 2016용 DSVM 에디션은 NVIDIA CUDA 드라이버, CUDA 심층 신경망 네트워크 라이브러리 및 기타 도구를 사전 설치합니다.


| OS | 드라이버 |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe)(.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe)(.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 드라이버

Microsoft는 가상 워크스테이션 또는 가상 응용 프로그램에 사용 되는 NV 및 NVv3 시리즈 Vm에 대 한 NVIDIA GRID 드라이버 설치 관리자를 재배포 합니다. 다음 표에 나열 된 운영 체제에만 Azure NV 시리즈 Vm에 이러한 그리드 드라이버를 설치 합니다. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다. NVIDIA vGPU 소프트웨어 라이선스 서버를 설정할 필요는 없습니다.

Nvidia 확장은 항상 최신 드라이버를 설치 합니다. 이전 버전에 대 한 종속성이 있는 고객을 위해 여기에서 이전 버전에 대 한 링크를 제공 합니다.

Windows Server 2019, Windows Server 2016 및 Windows 10 (최대 빌드 1909):
- [표 10.0 (441.66)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [표 9.0 (431.02)](https://download.microsoft.com/download/8/C/C/8CC88D54-EB07-44D3-8FA9-B797B173ED04/431.02_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Windows Server 2012 R2, Windows Server 2008 R2, Windows 8 및 Windows 7: 
- [표 10.0 (441.66)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [표 9.1 (431.79)](https://download.microsoft.com/download/8/6/e/86ef2daa-b31e-43ad-90f2-bd795384b71e/431.79_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (.exe)  
