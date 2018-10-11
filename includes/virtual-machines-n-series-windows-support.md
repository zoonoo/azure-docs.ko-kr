---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/24/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4da90cf636ab2010d7c369f4c13e45190dc6b2db
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48020793"
---
## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla(CUDA) 드라이버

NCv2, NCv3 및 ND 시리즈 VM(NV 시리즈의 경우는 선택적임)용 NVIDIA Tesla(CUDA) 드라이버는 다음 표에 나와 있는 운영 체제에서만 사용할 수 있습니다. 드라이버 다운로드 링크는 발표 시점에 제공된 링크입니다. 최신 드라이버에 대해서는 [NVIDIA](http://www.nvidia.com/) 웹 사이트를 참조하세요.

> [!TIP]
> Windows Server VM에서 수동 CUDA 드라이버를 설치하는 대신, Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) 이미지를 배포할 수 있습니다. Windows Server 2016용 DSVM 에디션은 NVIDIA CUDA 드라이버, CUDA 심층 신경망 네트워크 라이브러리 및 기타 도구를 사전 설치합니다.


| OS | 드라이버 |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe)(.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe)(.exe) |


### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 드라이버

Microsoft는 가상 워크스테이션으로 사용되는 NV 및 NVv2 시리즈 VM 또는 가상 응용 프로그램에 대해 NVIDIA GRID 드라이버 설치 관리자를 재배포합니다. Azure NV VM에서 다음 표에 나열된 운영 체제에만 이러한 GRID 드라이버를 설치합니다. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다.

| OS | 드라이버 |
| -------- |------------- |
| Windows Server 2016<br/><br/>윈도우 10 | [GRID 6.2(391.81)](https://go.microsoft.com/fwlink/?linkid=874181)(.exe) |
| Windows Server 2012 R2 | [GRID 6.2(391.81)](https://go.microsoft.com/fwlink/?linkid=874184)(.exe)  |