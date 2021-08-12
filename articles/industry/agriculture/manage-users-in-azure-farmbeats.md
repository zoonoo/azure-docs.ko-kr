---
title: Azure FarmBeats에서 사용자 관리
description: 이 문서에서는 Azure FarmBeats에서 사용자를 관리하는 방법을 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 12/02/2019
ms.author: riyazp
ms.openlocfilehash: cb9a46dcfe3a99717c78e2401b2d5b69702ffdaf
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759728"
---
# <a name="manage-users"></a>사용자 관리

Azure FarmBeats에는 Azure AD(Azure Active Directory) 인스턴스에 속한 사람들을 위한 사용자 관리가 포함되어 있습니다. Azure FarmBeats 인스턴스에 사용자를 추가하여 API에 액세스하고, 생성된 맵을 보고, 팜에서 센서 원격 분석에 액세스할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure FarmBeats 설치가 필요합니다. 자세한 내용은 [Azure FarmBeats 설치](install-azure-farmbeats.md)를 참조하세요.
- Azure FarmBeats 인스턴스에서 추가하거나 제거하려는 사용자의 이메일 ID입니다.

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats 사용자 관리

Azure FarmBeats는 인증, 액세스 제어 및 역할에 Azure AD를 사용합니다. Azure AD 테넌트의 사용자를 Azure FarmBeats의 사용자로 추가할 수 있습니다.

> [!NOTE]
> 사용자가 Azure AD 테넌트 사용자가 아닌 경우 **Azure AD 사용자 추가** 섹션의 지침에 따라 설정을 완료합니다.

Azure FarmBeats는 두 가지 유형의 사용자 역할을 지원합니다.

 - **관리자:** Azure FarmBeats Datahub API에 대한 전체 액세스 권한입니다. 이 역할의 사용자는 모든 Azure FarmBeats Datahub 개체를 쿼리하고 FarmBeats Accelerator에서 모든 작업을 수행할 수 있습니다.
 - **읽기 전용:** FarmBeats Datahub API에 대한 읽기 전용 액세스 권한입니다. 사용자는 Datahub API, Accelerator 대시보드 및 맵을 볼 수 있습니다. 읽기 전용 액세스 권한이 있는 사용자는 맵 생성, 디바이스 연결 또는 팜 만들기와 같은 작업을 수행할 수 없습니다.

## <a name="add-users-to-azure-farmbeats"></a>Azure FarmBeats에 사용자 추가

Azure FarmBeats에 사용자를 추가하려면 다음을 수행합니다.

1. Accelerator에 로그인한 다음, **설정** 아이콘을 선택합니다.
2. **액세스 제어** 를 선택합니다.

    ![팜 설정 창](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. 액세스 권한을 부여하려는 사용자의 이메일 ID를 입력합니다.
4. 원하는 역할, **관리자** 또는 **읽기 전용** 을 선택합니다.
5. **역할 추가** 를 선택합니다.

이제 추가된 사용자가 Azure FarmBeats(Datahub 및 Accelerator 모두)에 액세스할 수 있습니다.

## <a name="delete-users-from-azure-farmbeats"></a>Azure FarmBeats에서 사용자 삭제

Azure FarmBeats 시스템에서 사용자를 제거하려면 다음을 수행합니다.

1. Accelerator에 로그인한 다음, **설정** 아이콘을 선택합니다.
2. **액세스 제어** 를 선택합니다.
3. **삭제** 를 선택합니다.

   사용자가 시스템에서 삭제됩니다. 다음과 같은 확인 메시지가 표시됩니다.

   ![Azure FarmBeats 확인 메시지](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD 사용자 추가

> [!NOTE]
> Azure FarmBeats 사용자는 애플리케이션 및 역할에 할당하기 전에 Azure AD 테넌트에 있어야 합니다. Azure AD 테넌트에서 사용자가 없으면 이 섹션의 지침을 따릅니다. Azure AD 테넌트에 사용자가 이미 있는 경우 지침을 건너뜁니다.

지침에 따라 Azure AD에 사용자를 추가합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 오른쪽 위에서 계정을 선택한 다음, FarmBeats와 연결된 Azure AD 테넌트로 전환합니다.
3. **Azure Active Directory** > **사용자** 를 선택합니다.

    Azure AD 사용자 목록이 표시됩니다.

4. 디렉터리에 사용자를 추가하려면 **새 사용자** 를 선택합니다. 외부 사용자를 추가하려면 **새 게스트 사용자** 를 선택합니다.

    ![“모든 사용자” 창](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. 새 사용자의 이름을 선택하고 해당 사용자의 필수 필드를 작성합니다.
6. **만들기** 를 선택합니다.

Azure AD 사용자 관리에 대한 자세한 내용은 [Azure AD에서 사용자 추가 또는 삭제](../../active-directory/fundamentals/add-users-azure-active-directory.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure FarmBeats 인스턴스에 사용자를 성공적으로 추가했습니다. 이제 [팜을 만들고 관리](manage-farms-in-azure-farmbeats.md#create-farms)하는 방법을 알아봅니다.