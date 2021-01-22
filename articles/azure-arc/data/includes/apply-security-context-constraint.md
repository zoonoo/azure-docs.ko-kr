---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696070"
---
이 섹션에서는 SCC (보안 컨텍스트 제약 조건)를 적용 하는 방법을 설명 합니다. 미리 보기 릴리스의 경우 보안 제약 조건이 완화 됩니다. 

1. 사용자 지정 SCC (보안 컨텍스트 제약 조건)를 다운로드 합니다. 다음 중 하나를 사용합니다. 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Raw](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` 다음 명령은 arc-데이터를 다운로드 합니다.

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