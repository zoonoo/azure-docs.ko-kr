---
author: baanders
description: Azure Digital Twins 설정의 액세스 권한 단계에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009665"
---
Azure Digital Twins 인스턴스를 설정 하 고 나면 클라이언트 응용 프로그램을 통해 해당 인스턴스와 상호 작용 하는 것이 일반적입니다. 작업 중인 클라이언트 응용 프로그램을 만들려면 클라이언트 앱이 Azure Digital Twins에 대해 인증할 수 있는지 확인 해야 합니다. 클라이언트 앱에서 사용할 [Azure Active Directory (AZURE AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) **앱 등록** 을 설정 하 여 수행 합니다.

이 앱 등록에서는 [Azure Digital Twins api](../articles/digital-twins/how-to-use-apis-sdks.md)에 대 한 액세스 권한을 구성 합니다. 나중에 클라이언트 앱이 앱 등록에 대해 인증 되 고, 그 결과 Api에 대해 구성 된 액세스 권한이 부여 됩니다.

>[!TIP]
> 구독 소유자/관리자는 모든 새 Azure 디지털 Twins 인스턴스에 대해 새 앱 등록을 설정 *하거나* , 한 번만 수행 하 고 구독의 모든 Azure 디지털 쌍 인스턴스 간에 공유 되는 단일 앱 등록을 설정 하는 것이 좋습니다.

### <a name="create-the-registration"></a>등록 만들기