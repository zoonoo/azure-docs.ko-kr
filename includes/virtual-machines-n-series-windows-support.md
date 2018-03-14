---
title: "포함 파일"
description: "포함 파일"
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 506c2a4cf675a347dc4c45c9ccf8bce95de2f6fc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-drivers"></a>NC, NCv2, NCv3 및 ND 시리즈 - NVIDIA Tesla 드라이버

| OS | 드라이버 |
| -------- |------------- |
| Windows Server 2016 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2016-international.exe)(.exe) |
| Windows Server 2012 R2 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2008-2012r2-64bit-international.exe)(.exe) |

> [!NOTE]
> Tesla 드라이버 다운로드 링크는 발표 시점에 제공된 링크입니다. 최신 드라이버에 대해서는 [NVIDIA](http://www.nvidia.com/) 웹 사이트를 참조하세요.
>

### <a name="nv-series---nvidia-grid-drivers"></a>NV 시리즈 - NVIDIA GRID 드라이버

| OS | 드라이버 |
| -------- |------------- |
| Windows Server 2016 | [GRID 5.2(386.09)](https://go.microsoft.com/fwlink/?linkid=836843)(.exe) |
| Windows Server 2012 R2 | [GRID 5.2(386.09)](https://go.microsoft.com/fwlink/?linkid=836844)(.exe)  |

> [!NOTE]
> Microsoft는 NV VM용 NVIDIA GRID 드라이버 설치 관리자를 재배포합니다. Azure NV VM에 이 GRID 드라이버만 설치하십시오. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다.
>