---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165496"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>가상 컴퓨터용 TCP 엔드포인트 만들기
인터넷에서 SQL 서버에 연결하려면, 가상 머신에 들어오는 TCP 통신을 수신하는 엔드포인트가 있어야 합니다. 이 Azure 구성 단계에서는 들어오는 TCP 포트 트래픽을 가상 머신에 액세스 가능한 TCP 포트로 보냅니다.

> [!NOTE]
> 동일한 클라우드 서버 또는 가상 네트워크 내에서 연결하는 경우, 공개적으로 액세스할 수 있는 엔드포인트를 만들 필요가 없습니다. 이 경우, 다음 단계를 계속 할 수 있습니다. 자세한 내용은 [연결 시나리오](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios)를 참조하세요.
> 
> 

1. Azure Portal에서 **가상 머신(클래식)** 를 선택합니다.
2. 그런 후 SQL Server 가상 머신을 선택합니다.
3. **엔드포인트**를 선택하고 엔드포인트 블레이드 맨 위에 있는 **추가** 단추를 클릭합니다.
   
    ![포털의 엔드포인트 만들기 단계](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. **엔드포인트 추가** 블레이드에서 **이름**(예: SQLEndpoint)을 지정합니다.
5. **프로토콜**로 **TCP**를 선택합니다.
6. **공용 포트**에 대해 포트 번호(예: **57500**)를 지정합니다.
7. **개인 포트**에 대해 기본값이 **1433**인 SQL Server의 수신 대기 포트를 지정합니다.
8. **확인** 을 클릭하여 엔드포인트를 만듭니다.

