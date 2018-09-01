---
title: Azure Stack에 대 한 지원 되는 게스트 운영 체제 | Microsoft Docs
description: 이러한 게스트 운영 체제는 Azure Stack에서 사용할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: d6e9531edf8915e4b6c2636372b756002fca10fd
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382402"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure Stack에서 지 원하는 게스트 운영 체제

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

## <a name="windows"></a>Windows

Azure Stack은 다음 표에 나열 된 Windows 게스트 운영 체제를 지원 합니다.

| 운영 체제 | 설명 | Marketplace에서 사용할 수 있습니다. |
| --- | --- | --- | --- | --- | --- |
| Windows Server 버전 1709 | 64비트 | 컨테이너를 사용 하 여 코어 |
| Windows Server 2016 | 64비트 |  데이터 센터, 데이터 센터 Core 컨테이너를 사용 하 여 데이터 센터 |
| Windows Server 2012 R2 | 64비트 |  데이터 센터 |
| Windows Server 2012 | 64비트 |  데이터 센터 |
| Windows Server 2008 R2 SP1 | 64비트 |  데이터 센터 |
| Windows Server 2008 SP2 | 64비트 |  사용자 고유의 이미지를 표시 합니다. |
| Windows 10 *(참고 1 참조)* | 64 비트, Pro 및 Enterprise | 사용자 고유의 이미지를 표시 합니다. |

***참고 1:*** *Azure Stack에 Windows 10 클라이언트 운영 체제를 배포 하려면 있어야 [Windows 사용자 라이선스로](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) 정규화 된 다중 테 넌 트 호스팅 서비스 공급자를 통해 구입 하거나 ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).*

Marketplace 이미지-수-종 또는 BYOL (EA/SPLA) 라이선스에 대해 사용할 수 있습니다. 단일 Azure Stack 인스턴스에서 모두 사용 하 여 지원 되지 않습니다. Azure Stack을 배포 하는 동안 이미지에 적합 한 버전의 게스트 에이전트를 삽입합니다.

 Datacenter edition 다운로드;에 대 한 marketplace에서 사용할 수 있습니다. 고객은 다른 버전을 포함 하 여 자체 서버 이미지를 가져올 수 있습니다. Windows 클라이언트 이미지 Marketplace에서 사용할 수 없습니다.

## <a name="linux"></a>Linux

Marketplace에서 사용할 수 있는 것으로 나열 하는 Linux 배포는 필요한 Windows Azure Linux 에이전트 (WALA)를 포함 합니다. Azure Stack에 사용자 고유의 이미지를 가져오는 경우의 지침을 따르세요 [Azure Stack에 추가 Linux 이미지](azure-stack-linux.md)합니다.

> [!NOTE]
> 최신 공개 WALA 버전을 사용 하 여 사용자 지정 이미지를 빌드해야 합니다. Azure Stack 2.2.18 보다 오래 된 버전 제대로 작동 하지 않을 수 있습니다.
>
> [에서 cloud-init](https://cloud-init.io/) 이 이번에 Azure Stack에서 지원 되지 않습니다.

| 배포 | 설명 | 게시자 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Centos 6.9 | 64비트 | Rogue Wave | 예 |
| CentOS 기반 7.4 | 64비트 | Rogue Wave | 예 |
| ClearLinux | 64비트 | ClearLinux.org | 예 |
| Linux 컨테이너 |  64비트 | CoreOS | Stable |
| Debian 8 "Jessie" | 64비트 | credativ |  예 |
| Debian 9 "Stretch" | 64비트 | credativ | 예 |
| Red Hat Enterprise Linux 7.x | 64비트 | Red Hat |사용자 고유의 이미지를 표시 합니다. |
| SLES 11SP4 | 64비트 | SUSE | 예 |
| SLES 12SP3 | 64비트 | SUSE | 예 |
| Ubuntu 14.04 LTS | 64비트 | Canonical | 예 |
| Ubuntu 16.04-LTS | 64비트 | Canonical | 예 |
| Ubuntu 18.04-LTS | 64비트 | Canonical | 예 |

기타 Linux 배포는 나중에 지원할 수 있습니다.

Red Hat Enterprise Linux 지원 정보를 참조 하세요 [Red Hat 및 Azure Stack: Frequently Asked Questions](https://access.redhat.com/articles/3413531)합니다.
