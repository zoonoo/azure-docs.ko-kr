---
title: Windows 가상 데스크톱 사용자 연결 대기 시간-Azure
description: Windows 가상 데스크톱 사용자의 연결 대기 시간입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb0ee52d37fe901a610723d5864240b8778d8b17
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574597"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Windows 가상 데스크톱에서 사용자 연결 대기 시간 확인

Windows 가상 데스크톱은 전 세계에서 사용할 수 있습니다. 관리자는 원하는 모든 Azure 지역에서 Vm (가상 머신)을 만들 수 있습니다. 연결 대기 시간은 사용자 및 가상 컴퓨터의 위치에 따라 달라 집니다. Windows 가상 데스크톱 서비스는 대기 시간을 개선 하기 위해 새 지역에 지속적으로 롤아웃 됩니다.

[Windows Virtual Desktop Experience 평가기 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/) 를 통해 vm의 대기 시간을 최적화 하는 데 가장 적합 한 위치를 결정할 수 있습니다. Windows 가상 데스크톱이 새로운 영역으로 롤아웃 됨에 따라 최적의 위치가 변경 되지 않도록 하기 위해 2-3 개월 마다 도구를 사용 하는 것이 좋습니다.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Windows Virtual Desktop Experience 평가기 tool에서 결과 해석

Windows 가상 데스크톱에서 최대 150 밀리초의 대기 시간은 렌더링 또는 비디오를 포함 하지 않는 사용자 환경에 영향을 주지 않습니다. 텍스트를 처리 하는 데 150 밀리초와 200 밀리초 사이의 대기 시간이 적절 해야 합니다. 200 밀리초를 초과 하는 대기 시간은 사용자 환경에 영향을 줄 수 있습니다. 

또한 Windows 가상 데스크톱 연결은 사용자가 서비스를 사용 하는 컴퓨터의 인터넷 연결에 따라 달라 집니다. 사용자는 다음과 같은 상황 중 하나에서 연결이 끊어지거나 입력 지연이 발생할 수 있습니다.

 - 사용자에 게 안정적인 로컬 인터넷 연결이 없고 대기 시간이 200 밀리초를 초과 합니다.
 - 네트워크의 포화 또는 비율이 제한 됩니다.

가능한 한 사용자에 가까운 Vm 위치를 선택 하는 것이 좋습니다. 예를 들어 사용자가 인도에 있지만 VM이 미국에 있는 경우 전반적인 사용자 환경에 영향을 주는 대기 시간이 있게 됩니다. 

## <a name="azure-front-door"></a>Azure Front Door

Windows 가상 데스크톱은 [Azure Front 도어](https://azure.microsoft.com/services/frontdoor/) 를 사용 하 여 원본 IP 주소에 따라 가장 가까운 Windows 가상 데스크톱 게이트웨이로 사용자 연결을 리디렉션합니다. Windows 가상 데스크톱은 클라이언트에서 선택 하는 Windows 가상 데스크톱 게이트웨이를 항상 사용 합니다.

## <a name="next-steps"></a>다음 단계

- 최적 대기 시간을 확인 하려면 [Windows 가상 데스크톱 환경 평가기 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 참조 하세요.
- 가격 책정 계획은 [Windows 가상 데스크톱 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)을 참조 하세요.
- Windows 가상 데스크톱 배포를 시작 하려면 [자습서](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)를 확인 하세요.
