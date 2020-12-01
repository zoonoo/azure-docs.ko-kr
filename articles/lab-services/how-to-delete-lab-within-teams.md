---
title: 팀에서 Azure Lab Services 랩 삭제
description: 팀에서 Azure Lab Services 랩을 삭제 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433957"
---
# <a name="delete-labs-within-teams"></a>팀 내에서 랩 삭제

이 문서에서는 **Azure Lab Services** 앱에서 랩을 삭제 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure Portal에서 [Lab Services 계정을 만듭니다](tutorial-setup-lab-account.md#create-a-lab-account) .
* [팀 내에서 랩 서비스 랩을 시작 하 고 만듭니다](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>랩 삭제

[Azure Lab Services에서 실습 관리](how-to-manage-classroom-labs.md)에 설명 된 대로 랩을 직접 삭제 하 여 랩 [서비스 웹 사이트](https://labs.azure.com) 에서 팀 내에서 만든 랩을 삭제할 수 있습니다. 

팀을 삭제 하는 경우에도 랩 삭제가 트리거됩니다. 랩을 만든 팀이 삭제 된 경우 자동 사용자 목록 동기화가 트리거된 후에 lab이 자동으로 24 시간 후에 삭제 됩니다. 

> [!IMPORTANT]
> 탭을 삭제 하거나 앱을 제거 해도 랩을 삭제 되지 않습니다. 

탭이 삭제 된 경우 팀 멤버 자격 목록의 사용자는 웹 사이트에서 랩을 삭제 하거나 팀을 삭제 하 여 랩 삭제가 명시적으로 트리거되는 경우를 제외 하 고는 [랩 서비스 웹 사이트](https://labs.azure.com) 의 vm에 계속 액세스할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [팀에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [팀 내의 랩 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)
- [팀 내에서 랩의 VM 풀 관리](how-to-manage-vm-pool-within-teams.md)
- [팀 내에서 랩 일정 만들기 및 관리](how-to-create-schedules-within-teams.md)
- [팀 내에서 VM 액세스 – 학생 보기](how-to-access-vm-for-students-within-teams.md)

