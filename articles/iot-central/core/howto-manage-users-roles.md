---
title: Azure IoT Central 응용 프로그램에서 사용자 및 역할 관리 | Microsoft Docs
description: 관리자는 Azure IoT Central 응용 프로그램에서 사용자 및 역할을 관리 하는 방법
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 937c4a4dbf976564cbf8dc562bdec3b328fc2bc0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950094"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>IoT Central 응용 프로그램에서 사용자 및 역할 관리

이 문서에서는 관리자가 Azure IoT Central 응용 프로그램에서 사용자를 추가, 편집 및 삭제 하는 방법과 Azure IoT Central 응용 프로그램에서 역할을 관리 하는 방법을 설명 합니다.

**관리** 섹션에 액세스하여 사용하려면 Azure IoT Central 애플리케이션에서 **관리자** 역할이어야 합니다. Azure IoT Central 애플리케이션을 만드는 사용자에게는 자동으로 해당 애플리케이션의 **관리자** 역할이 할당됩니다.


## <a name="add-users"></a>사용자 추가

모든 사용자는 Azure IoT Central 애플리케이션에 로그인하고 액세스하려면 사용자 계정이 있어야 합니다. Azure IoT Central에서 지원되는 계정은 MSA(Microsoft 계정) 및 Azure AD(Azure Active Directory) 계정입니다. Azure Active Directory 그룹은 현재 Azure IoT Central에서 지원되지 않습니다.

자세한 내용은 [Microsoft 계정 도움말](https://support.microsoft.com/products/microsoft-account?category=manage-account) 및 [빠른 시작: Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)를 참조하세요.

1. IoT Central 애플리케이션에 사용자를 추가하려면 **관리** 섹션에 있는 **사용자** 페이지로 이동합니다.

    ![사용자 목록](media/howto-administer/image1.png)

1. 사용자를 추가하려면 **사용자** 페이지에서 **+ 사용자 추가**를 선택합니다.

1. **역할** 드롭다운 메뉴에서 사용자의 역할을 선택합니다. 이 문서의 [역할 관리](#manage-roles) 섹션에서 역할에 대해 자세히 알아보세요.

    ![역할 선택](media/howto-administer/image3.png)

    > [!NOTE]
    >  사용자를 대량으로 추가하려면 세미콜론을 구분 기호로 사용하여 추가할 모든 사용자의 사용자 ID를 입력합니다. **역할** 드롭다운 메뉴에서 역할을 선택합니다. 그런 다음 **저장**을 선택합니다.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>사용자에게 지정된 역할 편집

역할이 할당된 후에는 변경할 수 없습니다. 사용자에게 할당된 역할을 변경하려면 사용자를 삭제하고 다른 역할의 사용자를 추가해야 합니다.

> [!NOTE]
> 할당 된 역할은 IoT Central 응용 프로그램에만 적용 되며 Azure Portal에서 관리할 수 없습니다.

## <a name="delete-users"></a>사용자 삭제

사용자를 삭제하려면 **사용자** 페이지에서 하나 이상의 확인란을 선택합니다. 그런 다음, **삭제**를 선택합니다.

## <a name="manage-roles"></a>역할 관리

역할을 통해 조직 내에서 다양한 IoT Central 작업을 수행할 수 있는 사용자를 제어할 수 있습니다. 애플리케이션 사용자에게 세 가지 역할을 할당할 수 있습니다.

### <a name="administrator"></a>관리자

**관리자** 역할의 사용자는 애플리케이션의 모든 기능에 액세스할 수 있습니다.

애플리케이션을 만드는 사용자에게는 자동으로 **관리자** 역할이 할당됩니다. **관리자** 역할의 사용자가 항상 1명 이상 있어야 합니다.

### <a name="application-builder"></a>애플리케이션 작성자

**애플리케이션 작성자** 역할의 사용자는 애플리케이션에서 애플리케이션 관리를 제외한 모든 일을 할 수 있습니다. 빌더는 장치 템플릿 및 장치를 생성, 편집 및 삭제 하 고, 장치 집합을 관리 하 고, 분석과 작업을 실행할 수 있습니다. 작성자는 애플리케이션의 **관리** 섹션에 액세스할 수 없습니다.

### <a name="application-operator"></a>애플리케이션 운영자

**애플리케이션 운영자** 역할의 사용자는 디바이스 템플릿을 변경할 수 없으며 애플리케이션을 관리할 수 없습니다. 운영자는 장치를 추가 및 삭제 하 고, 장치 집합을 관리 하 고, 분석과 작업을 실행할 수 있습니다. 운영자는 **애플리케이션 작성자** 및 **관리** 페이지에 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central에서 사용자 및 역할을 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 Azure IoT Central에서 [청구서 보기](howto-view-bill.md) 에 대해 알아보는 것입니다.
