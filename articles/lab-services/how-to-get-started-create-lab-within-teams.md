---
title: 팀에서 시작 및 Azure Lab Services 랩 만들기
description: 팀에서 Azure Lab Services 랩을 시작 하 고 만드는 방법을 알아봅니다.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946740"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>팀에서 랩 서비스 랩 시작 및 만들기

이 문서에서는 팀에 Azure Lab Services 앱을 추가 하는 방법을 보여 줍니다. 그런 다음 팀에서 랩을 만드는 방법을 설명 합니다.

## <a name="add-a-lab-services-app-to-teams"></a>팀에 랩 서비스 앱 추가

팀 채널에서 직접 랩 서비스를 추가할 수 있습니다. 그러면 팀의 모든 사용자가 앱을 사용할 수 있습니다. 다음 세 가지 단계를 따르세요.

1. 앱을 추가 하려는 팀 채널로 이동 하 고 **+** "..."를 클릭 하 여 탭을 추가 하도록 선택 합니다. 오른쪽 창의 위쪽에 있습니다. 
1. 탭 옵션에서 **Azure Lab Services** 을 검색 하 고이 앱을 추가 합니다. 

    > [!NOTE]
    > 팀 **소유자** 만이 팀을 위한 랩을 만들 수 있습니다.
1. 이 팀에서 교실 랩을 만드는 데 사용할 랩 서비스 계정을 선택 합니다. 

    Azure Lab Services [Azure Lab Services 웹 사이트](https://labs.azure.com) 에 Single Sign-On를 사용 하 고 액세스 권한이 있는 모든 랩 계정을 가져옵니다. 

    팀과 같은 테 넌 트에 있는 계정과 **소유자**, **참가자**또는 **작성자** 액세스 권한이 있는 계정입니다. 

   ![ALS 시작](./media/integrate-with-teams/welcome.png) 
1. **저장** 을 누르면 앱이 팀에 추가 되 고 탭이 채널에 추가 됩니다. 

    이제 채널에서 **Azure Lab Services** 탭을 선택 하 고 다음 단계에 설명 된 대로 실습 관리를 시작할 수 있습니다.

    팀의 한 멤버가 탭을 추가하면 채널의 모든 사용자에게 표시됩니다. 앱에 대한 액세스 권한이 있는 모든 사용자에게는 Microsoft Teams에 사용하는 자격 증명으로 Single Sign-On 액세스가 제공됩니다. 앱에 대한 액세스 권한이 없는 사용자는 Teams에서 탭을 볼 수 있지만 온-프레미스 앱 및 Azure Portal 게시 버전 앱에 대한 권한이 부여될 때까지 액세스가 차단됩니다.

## <a name="create-a-classroom-lab"></a>클래스룸 랩 만들기

랩 계정이 선택 되 면 팀 소유자는 팀을 위한 랩을 만들 수 있습니다. 전체 랩 생성 프로세스와 랩 수준의 모든 태스크는 팀 내에서 수행할 수 있습니다. 사용자는 동일한 팀 내에서 여러 랩을 만들 수 있으며 랩 계정 수준에서 적절 한 액세스 권한으로 팀 소유자는 특정 팀과 연결 된 랩을 볼 수 있습니다.

## <a name="giving-access-to-users-of-the-lab-account"></a>랩 계정의 사용자에 게 액세스 권한 부여

랩 계정 수준에서 사용자에 대 한 프로 비전 액세스는 [Azure](https://ms.portal.azure.com/) portal에서 수행 해야 합니다.

1. Azure Portal에서 Azure Lab Services 계정으로 이동 합니다. 
1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고, 도구 모음에서 **+ 추가**, **+ 역할 할당 추가**를 차례로 선택합니다. 

    ![액세스 제어 -> 역할 할당 추가 단추](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **역할 할당 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 추가](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>교실 랩 만들기

팀 또는 [랩 서비스 웹 사이트](https://labs.azure.com)에서 랩을 만드는 경우에도 교실 labs 생성 프로세스는 동일 합니다. 

이 문서를 설정 하는 방법에 대 한 자세한 내용은 랩 만들기 프로세스 개요 [Azure Lab Services에서 교실 Labs 관리](how-to-manage-classroom-labs.md)를 참조 하세요.

## <a name="deleting-classroom-labs"></a>교실 랩 삭제

팀 내에서 만든 랩은 [Azure Lab Services에서 교실 실습 관리](how-to-manage-classroom-labs.md)에 설명 된 대로 랩을 직접 삭제 하 여 [lab Services 웹 사이트](https://labs.azure.com) 에서 삭제할 수 있습니다. 

팀을 삭제 하는 경우에도 랩 삭제가 트리거됩니다. 랩을 만든 팀이 삭제 된 경우 자동 사용자 목록 동기화가 트리거된 후에 lab이 자동으로 24 시간 후에 삭제 됩니다. 

탭을 삭제 하거나 앱을 제거 해도 랩을 삭제 되지 않습니다. 탭이 삭제 된 경우 팀 멤버 자격 목록의 사용자는 웹 사이트에서 랩을 삭제 하거나 팀을 삭제 하 여 랩 삭제가 명시적으로 트리거되는 경우를 제외 하 고는 [랩 서비스 웹 사이트](https://labs.azure.com) 의 vm에 계속 액세스할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

팀 내에서 랩을 만들면 랩 사용자 목록이 자동으로 채워지고 팀 멤버 자격과 동기화 됩니다. 소유자, 멤버 및 게스트를 포함 하 여 팀의 모든 사용자가 랩 사용자 목록에 자동으로 추가 됩니다. Azure lab Services는 팀 멤버 자격과의 동기화를 유지 하 고 자동 동기화는 24 시간 마다 트리거됩니다. 자세한 내용은 다음을 참조하세요.

[팀에서 Lab Services 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>추가 정보

다음 문서도 참조합니다.

- [팀에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [팀의 랩 서비스에서 VM 풀 관리](how-to-manage-vm-pool-within-teams.md)
- [팀에서 Lab Services 일정 만들기](how-to-create-schedules-within-teams.md)
- [팀의 랩 서비스에서 VM (학생 보기)에 액세스](how-to-access-vm-for-students-within-teams.md)

