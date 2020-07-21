---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524213"
---
## <a name="prerequisites"></a>필수 구성 요소

* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 계정 

논리 앱에서 OneDrive 계정을 사용하려면 먼저 OneDrive 계정에 연결하도록 논리 앱에 권한을 부여합니다.  Azure 포털의 논리 앱 내에서 이 작업을 쉽게 수행할 수 있습니다. 

다음 단계를 사용하여 OneDrive 계정에 연결하도록 논리 앱에 권한을 부여합니다.

1. 논리 앱을 만듭니다. Logic Apps 디자이너의 드롭다운 목록에서 **Microsoft 관리 API 표시**를 선택한 다음 검색 상자에 "OneDrive"를 입력합니다. 트리거 또는 동작 중 하나를 선택합니다.  
   !["Microsoft 관리 되는 Api 표시" 라는 대화 상자에 "onedrive"가 포함 된 검색 상자가 있습니다. 다음은 4 개 트리거의 목록입니다. 목록의 첫 번째는 "OneDrive-파일이 생성 될 때" 이며 선택 된 것입니다.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. 이전에 OneDrive에 대한 연결을 만들지 않은 경우 OneDrive 자격 증명을 사용하여 로그인하라는 메시지가 표시됩니다.  
   !["OneDrive-파일이 생성 될 때" 라는 대화 상자에 "로그인" 단추가 있습니다.](./media/connectors-create-api-onedrive/onedrive-2.png)
3. **로그인**을 선택하고 사용자 이름 및 암호를 입력합니다. **로그인을**선택 합니다.  
   !["로그인" 이라는 대화 상자가 표시 되 면 "Microsoft 계정 사용"이 표시 됩니다. "메일 또는 전화" 라는 레이블이 지정 된 두 개의 텍스트 상자와 "암호"는 "로그인 유지" 확인란 및 "로그인" 이라는 레이블이 있습니다.](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    이러한 자격 증명을 사용하여 OneDrive 계정의 데이터에 연결하도록 논리 앱에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다. 
4. **예**를 선택하여 OneDrive 계정에 사용할 논리 앱에 권한을 부여합니다.  
   !["이 앱에서 사용자의 정보에 액세스 하도록 허용 하 시겠습니까?" 라는 대화 상자가 나타납니다. 다음 네 가지 작업을 수행할 수 있는 권한을 요청 합니다. 1) "자동으로 로그인", 2) "전자 메일 주소 액세스", 3) "사용자의 정보에 액세스", 4) "OneDrive 파일 액세스" 사용 권한을 부여 하는 "예" 단추가 있으며,이 단추를 거부 하는 "아니요" 단추가 있습니다. 이러한 응용 프로그램 사용 권한을 변경 하는 링크가 있습니다.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. 연결이 만들어졌는지 확인합니다. 이제 논리 앱의 다른 단계를 진행합니다.  
   !["파일을 만들 때" 라는 대화 상자에는 "폴더" 라는 텍스트 상자와 연결 된 찾아보기 단추가 있습니다.](./media/connectors-create-api-onedrive/onedrive-5.png)

