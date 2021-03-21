---
title: IoT Hub 인증 및 권한 부여에 대 한 장치 업데이트 이해 | Microsoft Docs
description: IoT Hub의 장치 업데이트에서 Azure RBAC를 사용 하 여 사용자 및 서비스 Api에 대 한 인증 및 권한 부여를 제공 하는 방법을 이해 합니다.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 07310a5b6f275d4a35a3649c22aeea68045dde8b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101662842"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Azure 역할 기반 access control (RBAC) 및 장치 업데이트

장치 업데이트는 Azure RBAC를 사용 하 여 사용자 및 서비스 Api에 대 한 인증 및 권한 부여를 제공 합니다.

## <a name="configure-access-control-roles"></a>액세스 제어 역할 구성

다른 사용자 및 응용 프로그램이 장치 업데이트에 액세스할 수 있도록 하려면 사용자 또는 응용 프로그램에이 리소스에 대 한 액세스 권한이 있어야 합니다. 장치 업데이트에서 지 원하는 역할은 다음과 같습니다.

|   역할 이름   | Description  |
| :--------- | :---- |
|  장치 업데이트 관리자 | 모든 장치 업데이트 리소스에 대 한 액세스 권한이 있음  |
|  장치 업데이트 판독기| 모든 업데이트 및 배포를 볼 수 있습니다. |
|  장치 업데이트 콘텐츠 관리자 | 업데이트를 확인, 가져오기 및 삭제할 수 있습니다.  |
|  장치 업데이트 콘텐츠 판독기 | 업데이트를 볼 수 있음  |
|  장치 업데이트 배포 관리자 | 장치에 대 한 업데이트 배포를 관리할 수 있음|
|  장치 업데이트 배포 판독기| 장치에 대 한 업데이트 배포를 볼 수 있습니다. |

역할의 조합을 사용 하 여 올바른 수준의 액세스를 제공할 수 있습니다. 예를 들어 개발자는 장치 업데이트 콘텐츠 관리자 역할을 사용 하 여 업데이트를 가져오고 관리할 수 있지만 업데이트의 진행률을 보려면 장치 업데이트 배포 읽기 권한자 역할이 필요 합니다. 반대로, 장치 업데이트 판독기 역할이 있는 솔루션 운영자는 모든 업데이트를 볼 수 있지만 장치 업데이트 배포 관리자 역할을 사용 하 여 특정 업데이트를 장치에 배포 해야 합니다.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>게시 및 관리를 위해 장치 업데이트 REST Api에 대 한 인증

또한 장치 업데이트는 인증을 위해 Azure AD를 사용 하 여 서비스 Api를 통해 콘텐츠를 게시 및 관리 합니다. 시작 하려면 클라이언트 응용 프로그램을 만들고 구성 해야 합니다.

### <a name="create-client-azure-ad-app"></a>클라이언트 Azure AD 앱 만들기

응용 프로그램 또는 서비스를 Azure AD와 통합 하려면 [먼저](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) azure ad에 응용 프로그램을 등록 합니다. 클라이언트 응용 프로그램 설치는 사용 된 권한 부여 흐름에 따라 다릅니다.  아래 구성은 장치 업데이트 REST Api를 사용 하는 경우에 대 한 지침을 제공 합니다.

* 클라이언트 인증 설정: ' 네이티브 또는 웹 클라이언트에 대 한 리디렉션 Uri '.
* API 권한 설정-IoT Hub에서 제공 하는 장치 업데이트:
  * 위임 된 권한: ' user_impersonation '
  * **선택 사항**, 관리자 동의 부여

[다음 단계: 장치 업데이트 리소스 만들기 및 액세스 제어 역할 구성](./create-device-update-account.md)
