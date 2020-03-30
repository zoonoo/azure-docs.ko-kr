---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060436"
---
## <a name="scenario"></a>시나리오

VM에 대한 정적 IP 주소를 구성하는 방법을 더 잘 설명하기 위해 이 문서에서는 다음 시나리오를 사용합니다.

![가상 네트워크 시나리오: 프런트 엔드 서브넷에 대한 정적 IP 주소가 있는 프런트 엔드 및 백 엔드 서브넷](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

이 시나리오에서는 *FrontEnd* 서브넷에서 *DNS01이라는* VM을 만든 다음 *192.168.1.101의*정적 IP 주소를 사용하도록 설정합니다.
