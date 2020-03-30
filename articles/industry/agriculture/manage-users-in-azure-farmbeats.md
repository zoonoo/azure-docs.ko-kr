---
title: Azure FarmBeats에서 사용자 관리
description: 이 문서에서는 Azure FarmBeats에서 사용자를 관리하는 방법을 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502054"
---
# <a name="manage-users"></a>사용자 관리

Azure FarmBeats에는 Azure Active Directory(Azure AD) 인스턴스에 속한 사용자를 위한 사용자 관리가 포함됩니다. Azure FarmBeats 인스턴스에 사용자를 추가하여 API에 액세스하고, 생성된 맵을 보고, 팜에서 센서 원격 분석을 액세스할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure FarmBeats 설치가 필요합니다. 자세한 내용은 [Azure FarmBeats 설치를](install-azure-farmbeats.md)참조하십시오.
- Azure FarmBeats 인스턴스에서 추가하거나 제거할 사용자의 전자 메일 번호입니다.

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats 사용자 관리

Azure FarmBeats는 인증, 액세스 제어 및 역할에 Azure AD를 사용합니다. Azure AD 테넌트에 사용자를 Azure FarmBeats의 사용자로 추가할 수 있습니다.

> [!NOTE]
> 사용자가 Azure AD 테넌트 사용자가 아닌 경우 **Azure AD 사용자 추가** 섹션의 지침에 따라 설정을 완료합니다.

Azure FarmBeats는 두 가지 유형의 사용자 역할을 지원합니다.

 - **관리자**: Azure FarmBeats 데이터 허브 API에 대한 완전한 액세스. 이 역할의 사용자는 모든 Azure FarmBeats Datahub 개체를 쿼리하고 FarmBeats 가속기에서 모든 작업을 수행할 수 있습니다.
 - **읽기 전용**: FarmBeats Datahub API에 대한 읽기 전용 액세스. 사용자는 Datahub API, 가속기 대시보드 및 맵을 볼 수 있습니다. 읽기 전용 액세스 권한이 있는 사용자는 맵 생성, 장치 연결 또는 팜 만들기와 같은 작업을 수행할 수 없습니다.

## <a name="add-users-to-azure-farmbeats"></a>Azure 팜비츠에 사용자 추가

Azure FarmBeats에 사용자를 추가하려면 다음을 수행합니다.

1. 액셀러레이터에 로그인한 다음 **설정** 아이콘을 선택합니다.
2. 액세스 제어 를 **선택합니다.**

    ![농장 설정 창](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. 액세스 권한을 부여하려는 사용자의 전자 메일 ID를 입력합니다.
4. 원하는 역할, **관리자** 또는 **읽기 전용**을 선택합니다.
5. **역할 추가를**선택합니다.

이제 추가된 사용자는 Azure FarmBeats(데이터 허브와 액셀러레이터 모두에 모두)에 액세스할 수 있습니다.

## <a name="delete-users-from-azure-farmbeats"></a>Azure FarmBeats에서 사용자 삭제

Azure FarmBeats 시스템에서 사용자를 제거하려면 다음을 수행하십시오.

1. 액셀러레이터에 로그인한 다음 **설정** 아이콘을 선택합니다.
2. 액세스 제어 를 **선택합니다.**
3. **삭제를 선택합니다.**

   사용자가 시스템에서 삭제됩니다. 다음과 같은 확인 메시지가 표시됩니다.

   ![Azure FarmBeats 확인 메시지](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD 사용자 추가

> [!NOTE]
> Azure FarmBeats 사용자는 응용 프로그램 및 역할에 할당하기 전에 Azure AD 테넌트에 있어야 합니다. 사용자가 Azure AD 테넌트에 없는 경우 이 섹션의 지침을 따르십시오. 사용자가 Azure AD 테넌트에 이미 있는 경우 지침을 건너뜁니다.

Azure AD에 사용자를 추가하는 단계를 따릅니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
2. 오른쪽 상단에서 계정을 선택한 다음 FarmBeats와 연결된 Azure AD 테넌트로 전환합니다.
3. **Azure Active 디렉터리** > **사용자를**선택합니다.

    Azure AD 사용자 목록이 표시됩니다.

4. 디렉터리에 사용자를 추가하려면 **새 사용자를**선택합니다. 외부 사용자를 추가하려면 **새 게스트 사용자를**선택합니다.

    !["모든 사용자" 창](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. 새 사용자의 이름을 선택한 다음 해당 사용자에 필요한 필드를 완료합니다.
6. **만들기**를 선택합니다.

Azure AD 사용자 관리에 대한 자세한 내용은 Azure AD 의 [사용자 추가 또는 삭제를](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/)참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure FarmBeats 인스턴스에 사용자를 성공적으로 추가했습니다. 이제 [팜을 만들고 관리하는](manage-farms-in-azure-farmbeats.md#create-farms)방법을 알아봅니다.
