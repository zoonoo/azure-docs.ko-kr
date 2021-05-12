---
title: Device Update for IoT Hub 인증 및 권한 부여에 대한 이해 | Microsoft Docs
description: Device Update for IoT Hub에서 Azure RBAC를 사용하여 사용자 및 서비스 API에 대한 인증 및 권한 부여를 제공하는 방법에 대해 알아봅니다.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ca55b1df347b47a6eb82557658d59a3de666b703
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558400"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Azure RBAC(역할 기반 액세스 제어) 및 디바이스 업데이트

디바이스 업데이트에서 Azure RBAC를 사용하여 사용자 및 서비스 API에 대한 인증 및 권한 부여를 제공할 수 있습니다.

## <a name="configure-access-control-roles"></a>액세스 제어 역할 구성

다른 사용자 및 애플리케이션이 디바이스 업데이트에 액세스할 수 있도록 하려면 사용자 또는 애플리케이션이 이 리소스에 액세스 권한을 받아야 합니다. 디바이스 업데이트에서 지원하는 역할은 다음과 같습니다.

|   역할 이름   | Description  |
| :--------- | :---- |
|  디바이스 업데이트 관리자 | 모든 디바이스 업데이트 리소스에 대한 액세스 권한이 있음  |
|  디바이스 업데이트 읽기 권한자| 모든 업데이트 및 배포를 볼 수 있음 |
|  디바이스 업데이트 콘텐츠 관리자 | 업데이트를 보기, 가져오기 및 삭제할 수 있음  |
|  디바이스 업데이트 콘텐츠 읽기 권한자 | 업데이트를 볼 수 있음  |
|  디바이스 업데이트 배포 관리자 | 디바이스에 대한 업데이트 배포를 관리할 수 있음|
|  디바이스 업데이트 배포 읽기 권한자| 디바이스에 대한 업데이트 배포를 볼 수 있음 |

역할의 조합을 사용하여 올바른 수준의 액세스를 제공할 수 있습니다. 예를 들어, 개발자는 디바이스 업데이트 콘텐츠 관리자 역할을 사용하여 업데이트를 가져오고 관리할 수 있지만 업데이트의 진행률을 보려면 디바이스 업데이트 배포 읽기 권한자 역할이 필요합니다. 반대로, 디바이스 업데이트 읽기 권한자 역할이 있는 솔루션 운영자는 모든 업데이트를 볼 수 있지만 디바이스 업데이트 배포 관리자 역할을 사용하여 특정 업데이트를 디바이스에 배포해야 합니다.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>게시 및 관리를 위해 디바이스 업데이트 REST API 인증

또한 디바이스 업데이트는 인증을 위해 Azure AD를 사용하여 서비스 API를 통해 콘텐츠를 게시 및 관리합니다. 시작하려면 클라이언트 애플리케이션을 만들고 구성해야 합니다.

### <a name="create-client-azure-ad-app"></a>클라이언트 Azure AD 앱 만들기

애플리케이션 또는 서비스를 Azure AD와 통합하려면 Azure AD에 애플리케이션을 [먼저 등록](../active-directory/develop/quickstart-register-app.md)해야 합니다. 클라이언트 애플리케이션 설치는 사용된 권한 부여 흐름에 따라 다릅니다.  아래 구성은 디바이스 업데이트 REST API를 사용하는 경우에 대한 지침을 제공합니다.

* 클라이언트 인증 설정: '네이티브 또는 웹 클라이언트에 대한 리디렉션 URI'.
* API 권한 설정 - Device Update for IoT Hub 표시:
  * 위임된 권한: 'user_impersonation'
  * **선택 사항**, 관리자 동의 허용

[다음 단계: 디바이스 업데이트 리소스 만들기 및 액세스 제어 역할 구성](./create-device-update-account.md)