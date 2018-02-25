---
title: "Azure 스택에 대 한 지원 되는 게스트 운영 체제 | Microsoft Docs"
description: "Azure 스택 이러한 게스트 운영 체제를 사용할 수 있습니다."
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 3eceb740b8115d2eaca517017f6158744d6e8e58
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure 스택 지 원하는 게스트 운영 체제

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

## <a name="windows"></a>Windows
Azure 스택은 다음 표에 나열 된 Windows 게스트 운영 체제 지원: 마켓플레이스의 이미지는 Azure 스택을 다운로드할 수 있습니다. Windows 클라이언트 이미지 시장에서 제공 되지 않습니다.

배포 하는 동안 Azure 스택 이미지에 적합 한 버전의 게스트 에이전트를 삽입합니다.

| 운영 체제 | 설명 | 게시자 | OS 종류 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64비트 | Microsoft | Windows | 데이터 센터 |
| Windows Server 2012 | 64비트 | Microsoft | Windows | 데이터 센터 |
| Windows Server 2012 R2 | 64비트 | Microsoft | Windows | 데이터 센터 |
| Windows Server 2016 | 64비트 | Microsoft | Windows | Datacenter, 데이터 센터 코어, 컨테이너와 데이터 센터 |
| Windows 10 *(참고 1 참조)* | 64 비트, Pro 및 Enterprise | Microsoft | Windows | 아니요 |

***참고 1:****Azure 스택에 Windows 10 클라이언트 운영 체제를 배포 하려면 있어야 [사용자 라이선스 Windows](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) 또는 정규화 된 다중 테 넌 트 호스팅 서비스 공급자를 통해 구매 ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).* 


## <a name="linux"></a>Linux

여기에 나열 된 Linux 배포판의 필요한 Windows Azure Linux 에이전트 (WALA)를 포함 합니다.

> [!NOTE]   
> 2.2.3 보다 오래 된 WALA 버전으로 빌드된 이미지 *하지* 지원 하 고 배포 되지 않습니다. 일부 WALA 에이전트 버전 2.2.12 및 2.2.13 버전을 포함 하는 Azure 스택 Vm에서 작동 하지 않을 알려져 있습니다.
>
> [클라우드 init](https://cloud-init.io/) Azure 스택에 Ubuntu 배포 에서만 지원 됩니다.

| 배포 | 설명 | 게시자 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| 컨테이너 Linux |  64비트 | CoreOS | Stable |
| CentOS 기반 6.9 | 64비트 | Rogue 웨이브 | 예 |
| 7.4 centOS 기반 | 64비트 | Rogue 웨이브 | 예 |
| Debian 8 "Jessie" | 64비트 | credativ |  예 |
| Debian 9 "Stretch" | 64비트 | credativ | 예 |
| Red Hat Enterprise Linux (보류 중) 7.x | 64비트 | Red Hat | 아니요 |
| SLES 11SP4 | 64비트 | SUSE | 예 |
| SLES 12SP3 | 64비트 | SUSE | 예 |
| Ubuntu 14.04-LTS | 64비트 | Canonical | 예 |
| Ubuntu 16.04-LTS | 64비트 | Canonical | 예 |

다른 Linux 배포판을 나중에 지원할 수 있습니다.
