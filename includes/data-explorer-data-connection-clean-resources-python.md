---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 10/07/2019
ms.author: orspodek
ms.openlocfilehash: 81d26462d65f947c2b4b8cf49eb2998b0cc1d048
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031501"
---
## <a name="clean-up-resources"></a>리소스 정리

데이터 연결을 삭제 하려면 다음 명령을 사용 합니다.

```python
kusto_management_client.data_connections.delete(resource_group_name=resource_group_name, cluster_name=kusto_cluster_name, database_name=kusto_database_name, data_connection_name=kusto_data_connection_name)
```