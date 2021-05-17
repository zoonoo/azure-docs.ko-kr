---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94574201"
---
> [!IMPORTANT]
> Cosmos DB 인스턴스는 필요한 리소스와 함께 __구독__ 의 Microsoft 관리형 리소스 그룹에 생성됩니다. 즉, 이 Cosmos DB 인스턴스에 대해 요금이 청구됩니다. 관리형 리소스 그룹의 이름은 `<AML Workspace Resource Group Name><GUID>` 형식으로 지정됩니다. Azure Machine Learning 작업 영역에서 프라이빗 엔드포인트를 사용하는 경우 Cosmos DB 인스턴스에 대한 가상 네트워크도 생성됩니다. 이 VNet은 Cosmos DB와 Azure Machine Learning 간의 통신을 보호하는 데 사용됩니다.
> 
> * 이 Cosmos DB 인스턴스 또는 이 그룹에서 자동으로 생성된 리소스를 포함하는 __리소스 그룹을 삭제하지 마세요__. 리소스 그룹, Cosmos DB 인스턴스 등을 삭제해야 하는 경우에는 이를 사용하는 Azure Machine Learning 작업 영역을 삭제해야 합니다. 연결된 작업 영역이 삭제되면 리소스 그룹, Cosmos DB 인스턴스 및 자동으로 만들어진 기타 리소스가 삭제됩니다.
> * Cosmos DB 계정의 기본 [__요청 단위__](../articles/cosmos-db/request-units.md)는 __8000__ 으로 설정되어 있습니다.
> * 생성된 __Cosmos DB 인스턴스에서 사용할 자체 VNet을 제공할 수 없습니다__. __가상 네트워크를 수정할 수도 없습니다__. 예를 들어 사용하는 IP 주소 범위를 변경할 수 없습니다.