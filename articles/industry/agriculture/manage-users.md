---
title: 사용자 관리
description: 사용자를 관리 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 37dacf0adfc9e3dcbab963cef50e2ee5209df314
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852355"
---
# <a name="manage-users"></a>사용자 관리

Azure FarmBeats에는 Azure Active Directory의 일부인 사용자를 위한 사용자 관리 (Azure AD)가 포함 되어 있습니다. Azure FarmBeats 인스턴스에 사용자를 추가 하 여 Api에 액세스 하 고, 팜에서 생성 된 맵과 센서 원격 분석을 볼 수 있습니다.

## <a name="prerequisites"></a>전제 조건

- Azure FarmBeats 배포가 필요 합니다. Azure FarmBeats를 설정 하는 방법에 대 한 자세한 내용은 [FarmBeats 배포](prepare-for-deployment.md) 를 참조 하세요.
- Azure FarmBeats 인스턴스에서 추가 하거나 제거할 사용자의 전자 메일 ID입니다.

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats 사용자 관리

Azure FarmBeats는 인증, 액세스 제어 및 역할에 Azure AD를 사용 합니다. Azure AD 테 넌 트의 사용자는 Azure FarmBeats의 사용자로 추가할 수 있습니다.

> [!NOTE]
> 추가 하려는 사용자가 Azure AD 테 넌 트에 없는 경우 azure FarmBeats 사용자로 설정 하기 전에 **AZURE ad 사용자 추가** 섹션의 지침에 따라 설치를 완료 합니다.

**역할**

현재 Azure FarmBeats에서 지원 되는 두 가지 종류의 사용자 역할이 있습니다.

 - **관리** -Azure FarmBeats 데이터 허브 api에 대 한 모든 액세스 이 역할의 사용자는 모든 Azure FarmBeats Data hub 개체를 쿼리하여 FarmBeats Accelerator의 모든 작업을 수행할 수 있습니다.
 - **읽기 전용** -FarmBeats 데이터 허브 api에 대 한 읽기 전용 액세스입니다. 사용자는 데이터 허브 Api, 액셀러레이터 대시보드 및 맵을 볼 수 있습니다. "읽기 전용" 역할이 있는 사용자는 맵 생성, 장치 연결 또는 팜 만들기와 같은 작업을 수행할 수 없습니다.


## <a name="add-user-to-azure-farmbeats"></a>Azure FarmBeats에 사용자 추가

Azure FarmBeats에 사용자를 추가 하려면 
1.  액셀러레이터에 로그인 하 고 설정 아이콘을 클릭 합니다.
2.  Access Control 클릭

    ![프로젝트 팜 비트](./media/create-farms/settings-users-1.png)

3.  액세스 권한을 부여 하려는 사용자의 전자 메일 ID를 입력 합니다.
4.  원하는 역할 선택 – 관리자 또는 읽기 전용
5.  역할 추가를 클릭 합니다.

이제 추가 된 사용자가 Azure FarmBeats (데이터 허브 및 가속기 모두)에 액세스할 수 있습니다.

## <a name="delete-user-from-azure-farmbeats"></a>Azure FarmBeats에서 사용자 삭제

Azure FarmBeats 시스템에서 사용자를 제거 하려면 다음을 수행할 수 있습니다.
1.  액셀러레이터에 로그인 하 고 설정 아이콘을 클릭 합니다.
2.  Access Control 클릭
3.  제거 하려는 사용자의 전자 메일 ID 옆에 있는 삭제 아이콘을 클릭 합니다.

사용자가 시스템에서 제거 됩니다. 성공적인 작업을 확인 하는 다음 메시지가 표시 됩니다.


![프로젝트 팜 비트](./media/create-farms/manage-users-2.png)


## <a name="add-azure-ad-users"></a>Azure AD 사용자 추가

> [!NOTE]
> Azure FarmBeats에 대 한 액세스를 제공 하려는 사용자가 Azure AD 테 넌 트에 없는 경우에만 다음 단계를 수행 해야 합니다. 사용자가 이미 있는 경우 아래 단계를 수행 하지 않아도 됩니다.

FarmBeats 사용자는 Azure AD 테 넌 트에 있어야 응용 프로그램 및 역할에 할당할 수 있습니다. Azure AD에 사용자를 추가하려면 다음 단계를 사용합니다.
1.  [Azure portal](https://portal.azure.com/)에 로그인합니다.
2.  오른쪽 위 모서리에서 계정을 선택 하 고 FarmBeats에 연결 된 Azure AD 테 넌 트로 전환 합니다.
3.  **Azure Active Directory > 사용자**를 선택합니다. 디렉터리에 사용자의 목록이 표시됩니다.
4.  디렉터리에 사용자를 추가하려면 **새 사용자**를 선택합니다. 외부 사용자의 경우 **새 게스트 사용자**를 선택합니다.

    ![프로젝트 팜 비트](./media/create-farms/manage-users-3.png)

5.  새 사용자에 대한 필수 필드를 입력합니다. **만들기**를 선택합니다.

[Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) 문서에서 Azure AD 내에서 사용자를 관리하는 방법에 대한 자세한 내용을 알아보세요.

## <a name="next-steps"></a>다음 단계

Azure FarmBeats를 배포 했습니다. 이제 [팜을 만드는](manage-farms.md#create-farms)방법을 알아봅니다.

