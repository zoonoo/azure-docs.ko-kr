---
author: v-amallick
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: v-amallick
ms.openlocfilehash: d20ed4d39921f8000f77f947c4372bd8b10ca642
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294126"
---
이제 [프라이빗 엔드포인트](../articles/private-link/private-endpoint-overview.md)를 사용하여 데이터를 가상 네트워크 내의 서버에서 Recovery Services 자격 증명 모음으로 안전하게 백업할 수 있습니다. 프라이빗 엔드포인트는 VNET 주소 공간의 IP를 자격 증명 모음에 사용합니다. 가상 네트워크 내의 리소스와 자격 증명 모음 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크를 통해 이동합니다. 이렇게 하면 퍼블릭 인터넷에서 공개되지 않습니다. 프라이빗 엔드포인트는 Azure VM 내에서 실행되는 SQL 및 SAP HANA 데이터베이스를 백업하고 복원하는 데 사용할 수 있습니다. 또한 MARS 에이전트를 사용하는 온-프레미스 서버에도 사용할 수 있습니다.

Azure VM 백업은 인터넷에 연결할 필요가 없으므로 네트워크 격리를 허용하는 프라이빗 엔드포인트가 필요하지 않습니다.

Azure Backup의 프라이빗 엔드포인트에 대한 자세한 내용은 [여기](../articles/backup/private-endpoints.md)를 참조하세요.