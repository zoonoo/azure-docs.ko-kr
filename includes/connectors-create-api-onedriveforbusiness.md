---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102840"
---
## <a name="prerequisites"></a>필수 구성 요소

* [비즈니스용 OneDrive](https://OneDrive.com) 계정 

Logic Apps에서 비즈니스용 OneDrive 계정을 사용 하려면 먼저 Azure Portal의 비즈니스용 OneDrive 계정에 연결 하도록 Logic Apps에 권한을 부여 해야 합니다.

다음 단계를 수행 하 여 비즈니스용 OneDrive 계정에 연결 하도록 Logic Apps에 권한을 부여 합니다.  

1. Azure Portal에 로그인합니다. 

1. **Azure 서비스**에서 **Logic Apps**를 선택 합니다. 그런 다음 목록에서 논리 앱의 이름을 선택 합니다.

1. 논리 앱 메뉴의 **개발 도구**아래에서 **논리 앱 디자이너** 를 선택 합니다.

1. Logic Apps 디자이너에서 드롭다운 목록에서 **Microsoft 관리 Api 표시** 를 선택한 다음 검색 상자에 *비즈니스용 OneDrive* 를 입력 합니다. 사용할 트리거 또는 동작을 선택 합니다.  

   ![비즈니스용 OneDrive API 작업을 사용 하 여 되풀이 트리거를 보여 주는 Logic Apps Designer의 스크린샷](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. 이전 비즈니스용 OneDrive에 대 한 연결을 만들지 않은 경우 프롬프트에 따라 비즈니스용 OneDrive 자격 증명을 제공 합니다. 이러한 자격 증명은 비즈니스용 OneDrive 계정의 데이터에 액세스 하도록 논리 앱에 권한을 부여 하는 데 사용 됩니다.  

   ![비즈니스용 OneDrive에 대 한 로그인 프롬프트를 표시 하는 Logic Apps 디자이너의 스크린샷](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. 비즈니스용 OneDrive 사용자 이름 및 암호를 제공 하 여 논리 앱에 권한을 부여 합니다.  

   ![로그인 프롬프트를 표시 하는 비즈니스용 OneDrive 로그인 페이지의 스크린샷](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. 이제 연결이 단계에 나열 됩니다. 저장을 선택한 다음 논리 앱을 계속 만듭니다. 

   ![비즈니스용 OneDrive 연결이 나열 된 트리거를 보여 주는 Logic Apps 디자이너의 스크린샷](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
