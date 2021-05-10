---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87040224"
---
## <a name="prerequisites"></a>필수 구성 요소

* [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 계정 

Logic Apps에서 OneDrive 계정을 사용하려면 먼저 Azure Portal에서 OneDrive 계정에 연결할 권한을 Logic Apps에 부여해야 합니다.

다음 단계에 따라 OneDrive 계정에 연결할 권한을 Logic Apps에 부여합니다.  

1. Azure Portal에 로그인합니다. 

1. **Azure 서비스** 에서 **Logic Apps** 를 선택합니다. 그런 다음, 목록에서 논리 앱의 이름을 선택합니다.

1. 논리 앱 메뉴의 **개발 도구** 에서 **논리 앱 디자이너** 를 선택합니다.

1. Logic Apps 디자이너의 드롭다운 목록에서 **Microsoft 관리형 API 표시** 를 선택한 다음, 검색 상자에 *OneDrive* 를 입력합니다. 사용할 트리거 또는 작업을 선택합니다.

   ![추가할 OneDrive API 작업 목록을 보여주는 Logic Apps 디자이너의 스크린샷.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. 이전에 OneDrive에 대한 연결을 만들지 않은 경우 프롬프트에 따라 OneDrive 자격 증명을 사용하여 로그인합니다.  

   ![OneDrive API에 대한 로그인 프롬프트를 보여주는 Logic Apps 디자이너의 스크린샷.](./media/connectors-create-api-onedrive/onedrive-2.png)

3. **로그인** 을 선택하고 사용자 이름 및 암호를 입력합니다. **로그인** 을 선택합니다. 

   ![OneDrive API 권한 부여에 대한 Microsoft 계정 로그인 페이지의 스크린샷.](./media/connectors-create-api-onedrive/onedrive-3.png)   

    이러한 자격 증명은 OneDrive 계정의 데이터에 액세스하는 권한을 논리 앱에 부여하는 데 사용됩니다. 

4. **예** 를 선택하여 OneDrive 계정에 사용할 논리 앱에 권한을 부여합니다.  

   ![허용된 작업을 보여주는 Logic Apps에 대한 Microsoft 계정 권한 부여의 스크린샷.](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. 이제 연결이 단계에 나열됩니다. 저장을 선택한 다음, 논리 앱을 계속 만듭니다. 

   ![OneDrive API 연결을 사용하는 작업 편집기를 보여주는 Logic Apps 디자이너의 스크린샷](./media/connectors-create-api-onedrive/onedrive-5.png)
