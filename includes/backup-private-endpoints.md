---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81539616"
---
이제 [개인 끝점](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 을 사용 하 여 가상 네트워크 내의 서버에서 Recovery Services 자격 증명 모음으로 데이터를 안전 하 게 백업할 수 있습니다. 개인 끝점은 자격 증명 모음에 대 한 VNET 주소 공간의 IP를 사용 합니다. 가상 네트워크 및 자격 증명 모음 내 리소스 간의 네트워크 트래픽은 가상 네트워크 및 Microsoft 백본 네트워크의 개인 링크를 통해 이동 합니다. 이렇게 하면 공용 인터넷에서 노출이 제거 됩니다. 개인 끝점은 Azure Vm 내에서 실행 되는 SQL 및 SAP HANA 데이터베이스를 백업 및 복원 하는 데 사용할 수 있습니다. MARS 에이전트를 사용 하 여 온-프레미스 서버에도 사용할 수 있습니다.

Azure VM 백업은 인터넷 연결이 필요 하지 않으므로 네트워크 격리를 허용 하는 개인 끝점이 필요 하지 않습니다.

>[!NOTE]
> 이 기능은 현재 제한적 이며 미국 서 부, 미국 동부, 미국 서 부 2 및 미국 동부 (다른 Azure 지역에서 사용할 수 있음)에서 사용할 수 있습니다. Azure Backup에 대 한 개인 끝점을 사용 하려는 azbackupnetsec@microsoft.com 경우 [이 설문 조사](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) 를 작성 하 고에 전자 메일을 보내 주시기 바랍니다. 이 기능을 사용 하는 기능에는 Azure Backup 서비스의 승인이 적용 됩니다.
