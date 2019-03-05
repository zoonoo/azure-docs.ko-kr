---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963368"
---
### <a name="launch-the-publish-wizard"></a>게시 마법사 시작

**솔루션 탐색기**에서 **SharingService** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

게시 마법사가 자동으로 시작됩니다. **App Service** > **게시**를 선택하여 **App Service 만들기** 대화 상자를 엽니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

**App Service 만들기** 대화 상자에서 **계정 추가**를 클릭하고, Azure 구독에 로그인합니다. 이미 로그인한 경우 드롭다운에서 원하는 계정을 선택합니다.

> [!NOTE]
> 이미 로그인한 경우 **만들기**를 선택하지 마십시오.
>

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [resource group intro text](resource-group.md)]

**리소스 그룹** 옆에 있는 **새로 만들기**를 선택합니다.

리소스 그룹의 이름을 **myResourceGroup**으로 지정하고 **확인**을 선택합니다.

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

[!INCLUDE [app-service-plan](app-service-plan.md)]

**호스팅 계획** 옆에 있는 **새로 만들기**를 선택합니다.

**호스팅 계획 구성** 대화 상자에서 테이블의 설정을 사용합니다.

| 설정 | 제안 값 | 설명 |
|-|-|-|
|App Service 플랜| MySharingServicePlan | App Service 플랜의 이름입니다. |
| 위치 | 미국 서부 | 웹앱이 호스팅된 데이터 센터입니다. |
| 크기 | 무료 | [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)은 호스팅 기능을 결정합니다. |

**확인**을 선택합니다.

### <a name="create-and-publish-the-web-app"></a>웹앱 만들기 및 게시

**앱 이름**에서 고유한 앱 이름(유효한 문자는 `a-z`, `0-9` 및 `-`)을 입력하거나 자동으로 생성된 고유한 이름을 적용합니다. 웹앱의 URL은 `https://<app_name>.azurewebsites.net`이며, 여기서 `<app_name>`은 앱 이름입니다.

**만들기**를 선택하여 Azure 리소스 만들기를 시작합니다.

마법사가 완료되면 Azure에 ASP.NET Core 웹앱을 게시한 다음 기본 브라우저에서 앱을 시작합니다.

![Azure에서 게시된 ASP.NET 웹앱](./media/spatial-anchors-azure/web-app-running-live.png)

[작성 및 게시 단계](#create-and-publish-the-web-app)에서 지정한 앱 이름이 `https://<app_name>.azurewebsites.net` 형식의 URL 접두사로 사용됩니다. 나중에 사용되므로 이 URL를 기록해 둡니다.
