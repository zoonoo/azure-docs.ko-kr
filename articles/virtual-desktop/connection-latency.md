---
title: Windows 가상 데스크톱 사용자 연결 대기 시간-Azure
description: Windows 가상 데스크톱 사용자의 연결 대기 시간입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a60779fb045aa612a6ba0988c4635752f973f60
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607404"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Windows 가상 데스크톱에서 사용자 연결 대기 시간 확인

Windows 가상 데스크톱은 전 세계에서 사용할 수 있습니다. 관리자는 원하는 모든 Azure 지역에서 Vm (가상 머신)을 만들 수 있습니다. 연결 대기 시간은 사용자 및 가상 컴퓨터의 위치에 따라 달라 집니다. Windows 가상 데스크톱 서비스는 대기 시간을 개선 하기 위해 새 지역에 지속적으로 롤아웃 됩니다. 
 
[Windows Virtual Desktop Experience 평가기 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/) 를 통해 vm의 대기 시간을 최적화 하는 데 가장 적합 한 위치를 결정할 수 있습니다. Windows 가상 데스크톱이 새로운 영역으로 롤아웃 됨에 따라 최적의 위치가 변경 되지 않도록 하기 위해 2-3 개월 마다 도구를 사용 하는 것이 좋습니다. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Windows 가상 데스크톱은 사용자의 DNS 서버 위치를 확인 하는 Azure Traffic Manager를 사용 하 여 가장 가까운 Windows 가상 데스크톱 서비스 인스턴스를 찾습니다. 관리자는 Vm의 위치를 선택 하기 전에 사용자의 DNS 서버 위치를 검토 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 최적 대기 시간을 확인 하려면 [Windows 가상 데스크톱 환경 평가기 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 참조 하세요.
- 가격 책정 계획은 [Windows 가상 데스크톱 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)을 참조 하세요.
- Windows 가상 데스크톱 배포를 시작 하려면 [자습서](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)를 확인 하세요.