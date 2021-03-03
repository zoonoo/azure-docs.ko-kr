---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 03/02/2021
ms.author: mikeray
ms.openlocfilehash: 0fca43f76b24a08ca96be749f7f2a822b0be2418
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687596"
---
이 섹션에서는 SCC (보안 컨텍스트 제약 조건)를 적용 하는 방법을 설명 합니다. 미리 보기 릴리스의 경우 보안 제약 조건이 완화 됩니다. 

1. 사용자 지정 SCC (보안 컨텍스트 제약 조건)를 다운로드 합니다. 다음 중 하나를 사용합니다. 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [원시](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      다음 명령은 arc-데이터를 다운로드 합니다.

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. SCC를 만듭니다.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. 서비스 계정에 SCC를 적용 합니다.

   > [!NOTE]
   > 아래 명령에서 같은 네임 스페이스를 사용 `azdata arc dc create` 합니다. 예를 들면 `arc` 입니다.

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

   ```console
   oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   ```
