---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137027"
---
이제 [프라이빗 엔드포인트](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)를 사용하여 데이터를 가상 네트워크 내의 서버에서 Recovery Services 자격 증명 모음으로 안전하게 백업할 수 있습니다. 프라이빗 엔드포인트는 VNET 주소 공간의 IP를 자격 증명 모음에 사용합니다. 가상 네트워크 내의 리소스와 자격 증명 모음 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크를 통해 이동합니다. 이렇게 하면 퍼블릭 인터넷에서 공개되지 않습니다. 프라이빗 엔드포인트는 Azure VM 내에서 실행되는 SQL 및 SAP HANA 데이터베이스를 백업하고 복원하는 데 사용할 수 있습니다. 또한 MARS 에이전트를 사용하는 온-프레미스 서버에도 사용할 수 있습니다.

Azure VM 백업은 인터넷에 연결할 필요가 없으므로 네트워크 격리를 허용하는 프라이빗 엔드포인트가 필요하지 않습니다.

>[!NOTE]
> 이 기능은 현재 초기 사용 중입니다. 프라이빗 엔드포인트를 Azure Backup에 사용하려면 [이 설문 조사](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)를 작성하여 azbackupnetsec@microsoft.com으로 이메일을 보내주세요. 이 기능을 사용하는 기능은 Azure Backup 서비스의 승인을 받아야 합니다.
