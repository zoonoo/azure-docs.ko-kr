---
title: Windows Virtual Desktop 사용자 연결 대기 시간 - Azure
description: Windows Virtual Desktop 사용자의 연결 대기 시간입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 18fc276ec9d95a52c5bb5890cda313fc14fc7c5f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144334"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Windows Virtual Desktop에서 사용자 연결 대기 시간 확인

Windows Virtual Desktop은 전역적으로 사용할 수 있습니다. 관리자는 원하는 모든 Azure 지역에서 VM(가상 머신)을 만들 수 있습니다. 연결 대기 시간은 사용자 및 가상 머신의 위치에 따라 달라집니다. Windows Virtual Desktop 서비스는 대기 시간을 개선하기 위해 새로운 지역에 지속적으로 롤아웃됩니다.

[Windows Virtual Desktop Experience Estimator 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 사용하면 VM의 대기 시간을 최적화하는 데 가장 적합한 위치를 결정할 수 있습니다. Windows Virtual Desktop이 새로운 영역으로 롤아웃됨에 따라 최적의 위치가 변경되지 않도록 2~3개월마다 도구를 사용하는 것이 좋습니다.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Windows Virtual Desktop Experience Estimator 도구의 결과 해석

Windows Virtual Desktop에서 최대 150밀리초의 대기 시간은 렌더링이나 비디오를 포함하지 않는 사용자 환경에 영향을 주지 않습니다. 텍스트를 처리하는 데는 150밀리초와 200밀리초 사이의 대기 시간이 적절합니다. 200밀리초를 초과하는 대기 시간은 사용자 환경에 영향을 줄 수 있습니다. 

또한 Windows Virtual Desktop 연결은 사용자가 서비스를 사용하는 머신의 인터넷 연결에 따라 달라집니다. 사용자는 다음 상황 중 하나에서 연결이 끊어지거나 입력 지연이 발생할 수 있습니다.

 - 사용자에게 안정적인 로컬 인터넷 연결이 없고 대기 시간이 200밀리초를 초과합니다.
 - 네트워크가 포화 상태이거나 속도가 제한되어 있습니다.

가능한 한 사용자에 가까운 VM 위치를 선택하는 것이 좋습니다. 예를 들어 사용자가 인도에 있지만 VM이 미국에 있는 경우 전반적인 사용자 환경에 영향을 주는 대기 시간이 있게 됩니다. 

## <a name="azure-front-door"></a>Azure Front Door

Windows Virtual Desktop은 [Azure Front Door](https://azure.microsoft.com/services/frontdoor/)를 사용하여 원본 IP 주소에 따라 가장 가까운 Windows Virtual Desktop 게이트웨이로 사용자 연결을 리디렉션합니다. Windows Virtual Desktop은 항상 클라이언트에서 선택한 Windows Virtual Desktop 게이트웨이를 사용합니다.

## <a name="next-steps"></a>다음 단계

- 최적의 대기 시간을 위한 최적의 위치를 확인하려면 [Windows Virtual Desktop Experience Estimator 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/)를 참조하세요.
- 가격 책정 계획은 [Windows Virtual Desktop 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)을 참조하세요.
- Windows Virtual Desktop 배포를 시작하려면 [자습서](./create-host-pools-azure-marketplace.md)를 확인하세요.