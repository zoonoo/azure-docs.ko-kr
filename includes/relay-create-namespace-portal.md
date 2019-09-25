---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 3e3617f68d58e51f24affaae7d69812195b72fd0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210286"
---
1. [Azure Portal][Azure portal]에 로그인합니다.
1. **리소스 만들기**를 선택합니다. 그런 다음, **통합** > **릴레이**를 선택합니다. 목록에서 **릴레이**를 찾을 수 없으면 오른쪽 위 모서리에서 **모두 보기**를 선택합니다.
1. **만들기**를 선택 하 고 **이름** 필드에 네임 스페이스 이름을 입력 합니다. Azure Portal은 이름을 사용할 수 있는지 확인 합니다.
1. 네임 스페이스를 만들 Azure 구독을 선택 합니다.
1. [리소스 그룹](../articles/azure-resource-manager/manage-resource-groups-portal.md)에서 네임 스페이스를 추가할 기존 리소스 그룹을 선택 하거나 새 리소스 그룹을 만듭니다.  
1. 네임 스페이스를 호스트할 국가 또는 지역을 선택 합니다.

    ![네임스페이스 만들기][create-namespace]

1. **만들기**를 선택합니다. Azure Portal는 네임 스페이스를 만들고 사용 하도록 설정 합니다. 몇 분 후 시스템이 계정에 대한 리소스를 프로비전합니다.

### <a name="get-management-credentials"></a>관리 자격 증명 가져오기

1. **모든 리소스**를 선택한 다음 새로 만든 네임 스페이스 이름을 선택 합니다.
1. **공유 액세스 정책**을 선택합니다.  
1. **공유 액세스 정책** 아래에서 **RootManageSharedAccessKey**를 선택합니다.
1. SAS **정책에서: RootManageSharedAccessKey** **기본 연결 문자열**옆에 있는 **복사** 단추를 선택 합니다. 이 작업은 나중에 사용할 수 있도록 연결 문자열을 클립보드에 복사 합니다. 메모장이나 기타 다른 위치에 임시로 이 값을 붙여 넣습니다.
1. 앞의 단계를 반복하여 나중에 사용할 **기본 키**의 값을 복사하여 임시 위치에 붙여넣습니다.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
