---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574201"
---
> [!IMPORTANT]
> Cosmos DB 인스턴스는 __구독__ 에서 필요한 리소스와 함께 Microsoft에서 관리 하는 리소스 그룹에 만들어집니다. 즉,이 Cosmos DB 인스턴스에 대해 요금이 청구 됩니다. 관리형 리소스 그룹의 이름은 `<AML Workspace Resource Group Name><GUID>` 형식으로 지정됩니다. Azure Machine Learning 작업 영역에서 개인 끝점을 사용 하는 경우 Cosmos DB 인스턴스에 대해 가상 네트워크도 생성 됩니다. 이 VNet은 Cosmos DB와 Azure Machine Learning 간의 통신을 보호 하는 데 사용 됩니다.
> 
> * 이 Cosmos DB 인스턴스 또는이 그룹에 자동으로 생성 된 리소스를 포함 하는 __리소스 그룹을 삭제 하지 마십시오__ . 리소스 그룹, Cosmos DB 인스턴스 등을 삭제 해야 하는 경우이를 사용 하는 Azure Machine Learning 작업 영역을 삭제 해야 합니다. 연결 된 작업 영역을 삭제 하면 리소스 그룹, Cosmos DB 인스턴스 및 자동으로 만들어진 기타 리소스가 삭제 됩니다.
> * Cosmos DB 계정의 기본 [__요청 단위__](../articles/cosmos-db/request-units.md)는 __8000__ 으로 설정되어 있습니다.
> * 만든 __Cosmos DB 인스턴스에 사용할 사용자 고유의 VNet을 제공할 수 없습니다__ . __가상 네트워크를 수정할 수도 없습니다__. 예를 들어 사용 하는 IP 주소 범위를 변경할 수 없습니다.