---
title: Azure IoT Central 응용 프로그램에서 사용자 및 역할 관리 | Microsoft Docs
description: 관리자는 Azure IoT Central 응용 프로그램에서 사용자 및 역할을 관리 하는 방법
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 9729a51c36a520a2c196fb83515c9fa616411cf3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974430"
---
# <a name="manage-users-and-roles-in-your-iot-central-application-preview-features"></a>IoT Central 응용 프로그램에서 사용자 및 역할 관리 (미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

이 문서에서는 관리자가 Azure IoT Central 응용 프로그램에서 사용자를 추가, 편집 및 삭제할 수 있는 방법에 대해 설명 합니다. 이 문서에서는 Azure IoT Central 응용 프로그램에서 역할을 관리 하는 방법에 대해서도 설명 합니다.

**관리** 섹션에 액세스 하 고 사용 하려면 Azure IoT Central 응용 프로그램에 대 한 **관리자** 역할에 있어야 합니다. Azure IoT Central 응용 프로그램을 만드는 경우 해당 응용 프로그램에 대 한 **관리자** 역할에 자동으로 추가 됩니다.

## <a name="add-users"></a>Felhasználók hozzáadása

사용자가 로그인 하 여 Azure IoT Central 응용 프로그램에 액세스 하려면 모든 사용자에 게 사용자 계정이 있어야 합니다. Microsoft 계정 (MSAs) 및 Azure Active Directory (Azure AD) 계정은 Azure IoT Central에서 지원 됩니다. Azure Active Directory 그룹은 현재 Azure IoT Central에서 지원 되지 않습니다.

자세한 내용은 [Microsoft 계정 도움말](https://support.microsoft.com/products/microsoft-account?category=manage-account) 및 [빠른 시작: Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)를 참조 하세요.

1. IoT Central 응용 프로그램에 사용자를 추가 하려면 **관리** 섹션의 **사용자** 페이지로 이동 합니다.
    
    > [!div class="mx-imgBorder"]
    >![Felhasználók kezelése](media/howto-manage-users-roles/manage-users-pnp.png)

1. 사용자를 추가 하려면 **사용자 페이지에서** **+ 사용자 추가**를 선택 합니다.

1. **역할** 드롭다운 메뉴에서 사용자에 대 한 역할을 선택 합니다. 역할에 대 한 자세한 내용은이 문서의 [역할 관리](#manage-roles) 섹션을 참조 하세요.

    > [!div class="mx-imgBorder"]
    >사용자를 추가 하 고 역할을 선택 ![](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > 사용자에 게 다른 사용자를 추가할 수 있는 권한을 부여 하는 사용자 지정 역할의 사용자는 자신의 역할과 동일 하거나 더 낮은 권한이 있는 역할에만 사용자를 추가할 수 있습니다.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>사용자에 게 할당 된 역할 편집

역할은 할당 된 후에는 변경할 수 없습니다. 사용자에 게 할당 된 역할을 변경 하려면 사용자를 삭제 한 다음 다른 역할을 사용 하 여 사용자를 다시 추가 합니다.

> [!NOTE]
> 할당 된 역할은 IoT Central 응용 프로그램에만 적용 되며 Azure Portal에서 관리할 수 없습니다.

## <a name="delete-users"></a>사용자 삭제

사용자를 삭제 하려면 **사용자** 페이지에서 하나 이상의 확인란을 선택 합니다. Ezután válassza a **Törlés** elemet.

## <a name="manage-roles"></a>역할 관리

역할을 사용 하면 조직 내에서 IoT Central 다양 한 작업을 수행할 수 있는 사용자를 제어할 수 있습니다. 응용 프로그램의 사용자에 게 할당할 수 있는 세 가지 기본 제공 역할이 있습니다. 더 세밀 하 게 제어 해야 하는 경우 [사용자 지정 역할을 만들](#create-a-custom-role) 수도 있습니다.

> [!div class="mx-imgBorder"]
> 역할 선택 ![관리](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Rendszergazda

**관리자** 역할의 사용자는 청구를 포함 하 여 응용 프로그램의 모든 부분을 관리 하 고 제어할 수 있습니다.

응용 프로그램을 만드는 사용자는 **관리자** 역할에 자동으로 할당 됩니다. 항상 **관리자** 역할에는 사용자가 한 명 이상 있어야 합니다.

### <a name="builder"></a>Szerkesztő

**작성기** 역할의 사용자는 앱의 모든 부분을 관리할 수 있지만 관리 또는 연속 데이터 내보내기 탭은 변경할 수 없습니다.

### <a name="operator"></a>Művelet

**운영자** 역할의 사용자는 장치 상태 및 상태를 모니터링할 수 있습니다. 장치 템플릿을 변경 하거나 응용 프로그램을 관리 하는 것은 허용 되지 않습니다. 운영자는 장치를 추가 및 삭제 하 고, 장치 집합을 관리 하 고, 분석과 작업을 실행할 수 있습니다. 

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

솔루션에 보다 세분화 된 액세스 제어를 요구 하는 경우 사용자 지정 권한 집합을 사용 하 여 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할을 만들려면 응용 프로그램의 **관리** 섹션에서 **역할** 페이지로 이동 합니다. 그런 다음 **+ 새 역할**을 선택 하 고 역할에 대 한 이름 및 설명을 추가 합니다. 역할에 필요한 사용 권한을 선택 하 고 **저장**을 선택 합니다.

기본 제공 역할에 사용자를 추가 하는 것과 동일한 방식으로 사용자 지정 역할에 사용자를 추가할 수 있습니다.

> [!div class="mx-imgBorder"]
> 사용자 지정 역할을 빌드하 ![](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>사용자 지정 역할 옵션

사용자 지정 역할을 정의할 때 역할의 멤버인 경우 사용자에 게 부여 되는 권한 집합을 선택 합니다. 일부 권한은 다른 사용 권한에 따라 달라 집니다. 예를 들어 역할에 **응용 프로그램 대시보드 업데이트** 권한을 추가 하면 **응용 프로그램 대시보드 보기** 권한이 자동으로 추가 됩니다. 다음 표에는 사용자 지정 역할을 만들 때 사용할 수 있는 사용 권한 및 해당 종속성이 요약 되어 있습니다.

#### <a name="managing-devices"></a>Eszközök kezelése

**장치 템플릿 사용 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Kezelés | Megtekintés <br/> 기타 종속성: 장치 인스턴스 보기  |
| 모든 권한 | 보기, 관리 <br/> 기타 종속성: 장치 인스턴스 보기 |

**장치 인스턴스 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> 기타 종속성: 장치 템플릿 및 장치 그룹 보기 |
| Frissítés | Megtekintés <br/> 기타 종속성: 장치 템플릿 및 장치 그룹 보기  |
| Létrehozás | Megtekintés <br/> 기타 종속성: 장치 템플릿 및 장치 그룹 보기  |
| Törlés | Megtekintés <br/> 기타 종속성: 장치 템플릿 및 장치 그룹 보기  |
| 명령 실행 | 업데이트, 보기 <br/> 기타 종속성: 장치 템플릿 및 장치 그룹 보기  |
| 모든 권한 | 명령 보기, 업데이트, 만들기, 삭제, 실행 <br/> 기타 종속성: 장치 템플릿 및 장치 그룹 보기  |

**장치 그룹 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> 기타 종속성: 장치 템플릿 및 장치 인스턴스 보기 |
| Frissítés | Megtekintés <br/> 기타 종속성: 장치 템플릿 및 장치 인스턴스 보기   |
| Létrehozás | 보기, 업데이트 <br/> 기타 종속성: 장치 템플릿 및 장치 인스턴스 보기   |
| Törlés | Megtekintés <br/> 기타 종속성: 장치 템플릿 및 장치 인스턴스 보기   |
| 모든 권한 | 보기, 업데이트, 만들기, 삭제 <br/> 기타 종속성: 장치 템플릿 및 장치 인스턴스 보기 |

**장치 연결 관리 권한**

| Név | Függőségek |
| ---- | -------- |
| 인스턴스 읽기 | None <br/> 기타 종속성: 장치 템플릿, 장치 그룹, 장치 인스턴스 보기 |
| 에서 관리 되는 nace | None |
| 전역 읽기 | None   |
| 전역 관리 | 전역 읽기 |
| 모든 권한 | 인스턴스 읽기, 인스턴스 관리, 전역 읽기, 글로벌 관리를 참조 하세요. <br/> 기타 종속성: 장치 템플릿, 장치 그룹, 장치 인스턴스 보기 |

**작업 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> 기타 종속성: 장치 템플릿, 장치 인스턴스 및 장치 그룹 보기 |
| Frissítés | Megtekintés <br/> 기타 종속성: 장치 템플릿, 장치 인스턴스 및 장치 그룹 보기 |
| Létrehozás | 보기, 업데이트 <br/> 기타 종속성: 장치 템플릿, 장치 인스턴스 및 장치 그룹 보기 |
| Törlés | Megtekintés <br/> 기타 종속성: 장치 템플릿, 장치 인스턴스 및 장치 그룹 보기 |
| Végrehajtás | Megtekintés <br/> 기타 종속성: 장치 템플릿, 장치 인스턴스 및 장치 그룹 보기 장치 인스턴스 업데이트 장치 인스턴스에서 명령 실행 |
| 모든 권한 | 보기, 업데이트, 만들기, 삭제, 실행 <br/> 기타 종속성: 장치 템플릿, 장치 인스턴스 및 장치 그룹 보기 장치 인스턴스 업데이트 장치 인스턴스에서 명령 실행 |

**규칙 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> 기타 종속성: 장치 템플릿 보기 |
| Frissítés | Megtekintés <br/> 기타 종속성: 장치 템플릿 보기 |
| Létrehozás | 보기, 업데이트 <br/> 기타 종속성: 장치 템플릿 보기 |
| Törlés | Megtekintés <br/> 기타 종속성: 장치 템플릿 보기 |
| 모든 권한 | 보기, 업데이트, 만들기, 삭제 <br/> 기타 종속성: 장치 템플릿 보기 |

#### <a name="managing-the-app"></a>앱 관리

**응용 프로그램 설정 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Másolás | Megtekintés <br/> 기타 종속성: 장치 템플릿, 장치 인스턴스, 장치 그룹, 대시보드, 데이터 내보내기, 브랜딩, 도움말 링크, 사용자 지정 역할, 규칙 보기 |
| Törlés | Megtekintés   |
| 모든 권한 | 보기, 업데이트, 복사, 삭제 <br/> 기타 종속성: 장치 템플릿, 장치 그룹, 응용 프로그램 대시보드, 데이터 내보내기, 브랜딩, 도움말 링크, 사용자 지정 역할, 규칙 보기 |

**응용 프로그램 템플릿 내보내기 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Exportálás | Megtekintés <br/> 기타 종속성: 장치 템플릿, 장치 인스턴스, 장치 그룹, 대시보드, 데이터 내보내기, 브랜딩, 도움말 링크, 사용자 지정 역할, 규칙 보기 |
| 모든 권한 | 보기, 내보내기 <br/> 기타 종속성: 장치 템플릿, 장치 그룹, 응용 프로그램 대시보드, 데이터 내보내기, 브랜딩, 도움말 링크, 사용자 지정 역할, 규칙 보기 |

**청구 권한**

| Név | Függőségek |
| ---- | -------- |
| Kezelés | None     |
| 모든 권한 | Kezelés |

#### <a name="managing-users-and-roles"></a>사용자 및 역할 관리

**사용자 지정 역할 사용 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None |
| Frissítés | Megtekintés |
| Létrehozás | 보기, 업데이트 |
| Törlés | Megtekintés |
| 모든 권한 | 보기, 업데이트, 만들기, 삭제 |

**사용자 관리 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None <br/> 기타 종속성: 사용자 지정 역할 보기 |
| Hozzáadás | Megtekintés <br/> 기타 종속성: 사용자 지정 역할 보기 |
| Törlés | Megtekintés <br/> 기타 종속성: 사용자 지정 역할 보기 |
| 모든 권한 | 보기, 추가, 삭제 <br/> 기타 종속성: 사용자 지정 역할 보기 |

> [!NOTE]
> 사용자에 게 다른 사용자를 추가할 수 있는 권한을 부여 하는 사용자 지정 역할의 사용자는 자신의 역할과 동일 하거나 더 낮은 권한이 있는 역할에만 사용자를 추가할 수 있습니다.

#### <a name="customizing-the-app"></a>앱 사용자 지정

**응용 프로그램 대시보드 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Létrehozás | 보기, 업데이트 |
| Törlés | Megtekintés   |
| 모든 권한 | 보기, 업데이트, 만들기, 삭제 |

**개인 대시보드 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Létrehozás | 보기, 업데이트   |
| Törlés | Megtekintés   |
| 모든 권한 | 보기, 업데이트, 만들기, 삭제 |

**브랜딩, favicon 및 colors 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| 모든 권한 | 보기, 업데이트 |

**도움말 링크 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| 모든 권한 | 보기, 업데이트 |

#### <a name="extending-the-app"></a>앱 확장

**데이터 내보내기 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Frissítés | Megtekintés   |
| Létrehozás | 보기, 업데이트  |
| Törlés | Megtekintés   |
| 모든 권한 | 보기, 업데이트, 만들기, 삭제 |

**API 토큰 권한**

| Név | Függőségek |
| ---- | -------- |
| Megtekintés | None     |
| Létrehozás | Megtekintés   |
| Törlés | Megtekintés   |
| 모든 권한 | 보기, 만들기, 삭제 |

## <a name="next-steps"></a>Következő lépések

이제 Azure IoT Central 응용 프로그램에서 사용자와 역할을 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 [청구서를 관리](howto-view-bill.md)하는 방법을 배우는 것입니다.
