---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 03/02/2021
ms.author: mikeray
ms.openlocfilehash: 0fca43f76b24a08ca96be749f7f2a822b0be2418
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687596"
---
이 섹션에서는 SCC(보안 컨텍스트 제약 조건)를 적용하는 방법을 설명합니다. 미리 보기 릴리스의 경우 이를 통해 보안 제약 조건이 완화됩니다. 

1. 사용자 지정 SCC(보안 컨텍스트 제약)를 다운로드합니다. 다음 중 하나를 사용합니다. 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [원시](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      다음 명령은 arc-data-scc.yaml을 다운로드합니다.

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. SCC를 만듭니다.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. 서비스 계정에 SCC를 적용합니다.

   > [!NOTE]
   > 아래 `azdata arc dc create` 명령에서도 여기와 동일한 네임스페이스를 사용합니다. 예제는 `arc`입니다.

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

   ```console
   oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   ```
