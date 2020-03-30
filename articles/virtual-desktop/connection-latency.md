---
title: 윈도우 가상 데스크톱 사용자 연결 대기 시간 - Azure
description: Windows 가상 데스크톱 사용자를 위한 연결 대기 시간입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128184"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Windows 가상 데스크톱에서 사용자 연결 대기 시간 확인

Windows 가상 데스크톱은 전 세계적으로 사용할 수 있습니다. 관리자는 원하는 모든 Azure 지역에서 가상 시스템(VM)을 만들 수 있습니다. 연결 대기 시간은 사용자와 가상 시스템의 위치에 따라 달라집니다. Windows 가상 데스크톱 서비스는 대기 시간을 개선하기 위해 새로운 지역으로 지속적으로 출시됩니다. 
 
[Windows 가상 데스크톱 환경 추정 도구를](https://azure.microsoft.com/services/virtual-desktop/assessment/) 사용하면 VM의 대기 시간을 최적화하는 최적의 위치를 결정할 수 있습니다. Windows 가상 데스크톱이 새 영역으로 출시될 때 최적의 위치가 변경되지 않았는지 확인하기 위해 2~3개월마다 이 도구를 사용하는 것이 좋습니다. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Windows 가상 데스크톱은 사용자의 DNS 서버 위치를 확인하여 가장 가까운 Windows 가상 데스크톱 서비스 인스턴스를 찾는 Azure 트래픽 관리자를 사용합니다. 관리자는 VM의 위치를 선택하기 전에 사용자의 DNS 서버 위치를 검토하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 최적의 대기 시간에 가장 적합한 위치를 확인하려면 [Windows 가상 데스크톱 환경 추정기 도구를](https://azure.microsoft.com/services/virtual-desktop/assessment/)참조하십시오.
- 가격 책정 계획은 [Windows 가상 데스크톱 가격 책정을](https://azure.microsoft.com/pricing/details/virtual-desktop/)참조하십시오.
- Windows 가상 데스크톱 배포를 시작하려면 [자습서를](tenant-setup-azure-active-directory.md)확인하십시오.