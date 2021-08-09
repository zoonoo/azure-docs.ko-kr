---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 05/03/2021
ms.openlocfilehash: 7b35a3c4b26640b872b161bd8130db728645f1d0
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748298"
---
[서비스 연결](/azure/devops/pipelines/library/service-endpoints)을 통해 Azure DevOps 프로젝트에서 Azure 구독의 리소스에 액세스할 수 있습니다.

1. Azure DevOps에서 대상 파이프라인이 포함된 프로젝트로 이동합니다. 왼쪽 하단에서 **프로젝트 설정** 을 선택합니다.
1. **Pipelines** 아래에서 **서비스 연결** 을 선택합니다. 오른쪽 상단에서 **새 서비스 연결** 을 선택합니다.
1. **새 서비스 연결** 에서 **Azure Resource Manager** 를 선택합니다.

    :::image type="content" source="./media/azure-app-configuration-service-connection/new-service-connection.png" alt-text="새 서비스 연결 드롭다운 목록에서 Azure Resource Manager를 선택하는 방법을 보여 주는 스크린샷":::
1. **인증 방법** 대화 상자에서 **서비스 주체(자동)** 를 선택하여 새 서비스 주체를 만들거나 **서비스 주체(수동)** 를 선택하여 [기존 서비스 주체를 사용합니다](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#use-spn&preserve-view=true).
1. 구독 및 리소스와 서비스 연결 이름을 입력합니다.

새 서비스 주체를 만든 경우 서비스 연결에 할당된 서비스 주체의 이름을 찾습니다. 다음 단계에서 이 서비스 주체에 새 역할 할당을 추가합니다.

1. **프로젝트 설정** > **서비스 연결** 로 이동합니다.
1. 새 서비스 연결을 선택합니다.
1. **서비스 주체 관리** 를 선택합니다.
1. **표시 이름** 의 값을 확인합니다.

    :::image type="content" source="./media/azure-app-configuration-service-connection/service-principal-display-name.png" alt-text="서비스 주체 표시 이름을 보여 주는 스크린샷":::
