---
title: "Azure 스택에 대 한 게스트 운영 체제 지원 | Microsoft Docs"
description: "Azure 스택 이러한 게스트 운영 체제를 사용할 수 있습니다."
services: azure-stack
documentationcenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: JeffGoldner
ms.openlocfilehash: af524b5f40fb79648d91deb54186826902aa8cd2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2017
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure 스택 지 원하는 게스트 운영 체제

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

## <a name="windows"></a> Windows
Azure 스택 다음 Windows 게스트 운영 체제를 지원합니다. 마켓플레이스의 이미지는 Azure 스택을 다운로드할 수입니다. Windows 클라이언트 이미지 시장에서 제공 되지 않습니다.

배포 하는 동안 Azure 스택은 적합 한 버전의 게스트 에이전트 이미지 삽입 있는지 확인 합니다.

| 운영 체제 | 설명 | 게시자 | OS 종류 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64비트 | Microsoft |  Windows | 데이터 센터 |
| Windows Server 2012 | 64비트 | Microsoft |  Windows | 데이터 센터 |
| Windows Server 2012 R2 | 64비트 | Microsoft |  Windows | 데이터 센터 |
| Windows Server 2016 | 64비트 | Microsoft |  Windows | Datacenter, 데이터 센터 코어, 컨테이너와 데이터 센터 |
| Windows 7 | 64 비트, Pro 및 Enterprise | Microsoft |  Windows | 아니요 |
| Windows 8.1 | 64 비트, Pro 및 Enterprise | Microsoft |  Windows | 아니요 |
| 윈도우 10 | 64 비트, Pro 및 Enterprise | Microsoft |  Windows | 아니요 |

## <a name="linux"></a>Linux

여기에 나열 된 Linux 배포판의 필요한 Windows Azure Linux 에이전트 (WALA)를 포함 합니다. 

> [!NOTE]
> 2.2.3 보다 오래 된 WALA 버전으로 빌드된 이미지 *하지* 지원 하 고 배포 되지 않습니다.

| 배포 | 설명 | 게시자 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| 컨테이너 Linux |  64비트 | CoreOS | Stable |
| CentOS 기반 6.9 | 64비트 | Rogue 웨이브 | 예 |
| CentOS 기반 7.3 | 64비트 | Rogue 웨이브 | 예 |
| 7.4 centOS 기반 | 64비트 | Rogue 웨이브 | 예 |
| Debian 8 "Jessie" | 64비트 | credativ |  예 |
| Debian 9 "Stretch" | 64비트 | credativ | 예 |
| Oracle Linux | 64비트 | Oracle | 아니요 |
| Red Hat Enterprise Linux 7.x | 64비트 | Red Hat | 아니요 |
| SLES 11SP4 | 64비트 | SUSE | 예 |
| SLES 12SP3 | 64비트 | SUSE | 예 |
| Ubuntu 14.04 LTS | 64비트 | Canonical | 예 |
| Ubuntu 16.04 LTS | 64비트 | Canonical | 예 |

다른 Linux 배포판을 나중에 지원할 수 있습니다.




