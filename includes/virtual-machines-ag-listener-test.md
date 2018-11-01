---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227056"
---
이 단계에서는 동일한 네트워크에서 실행 중인 클라이언트 응용 프로그램을 사용하여 가용성 그룹 수신기를 테스트합니다.

클라이언트 연결에는 다음 요구 사항이 있습니다.

* 수신기에 대한 클라이언트 연결은 AlwaysOn 가용성 복제본을 호스팅하는 클라우드 서비스와 다른 클라우드 서비스에 있는 컴퓨터에서 가져와야 합니다.
* Always On 복제본이 다른 서브넷에 있는 경우 클라이언트는 연결 문자열에 *MultisubnetFailover=True* 를 지정해야 합니다. 이 조건은 다양한 서브넷에 있는 복제본에 대한 병렬 연결을 시도합니다. 이 시나리오에는 지역 간 AlwaysOn 가용성 그룹 배포가 포함됩니다.

하나의 예로, 동일한 Azure 가상 네트워크의 VM(하지만 복제본을 호스팅하지 않는 VM) 중 하나에서 수신기에 연결합니다. 이 테스트를 완료하는 쉬운 방법은 SQL Server Management Studio를 가용성 그룹 수신기에 연결하는 것입니다. 또 다른 간단한 방법은 다음과 같이 [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)를 실행하는 것입니다.

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> EndpointPort 값이 *1433*이면 호출에서 이를 지정하지 않아도 됩니다. 또한 이전 호출에서는 클라이언트 컴퓨터가 동일한 도메인에 조인되어 있고 Windows 인증을 사용하여 호출자에게 데이터베이스에 대한 권한이 부여되어 있다고 가정합니다.
> 
> 

수신기를 테스트할 때 클라이언트에서 장애 조치를 통해 수신기에 연결할 수 있는지 확인하려면 가용성 그룹을 장애 조치해야 합니다.

