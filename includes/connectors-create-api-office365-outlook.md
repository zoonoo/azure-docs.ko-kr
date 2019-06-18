---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b216de0a5094066977467b2899567122d585fb7e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66149722"
---
#### <a name="prerequisites"></a>필수 조건
* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* [Office 365](https://office365.com) 계정  

논리 앱에서 Office 365 계정을 사용하기 전에 Office 365 계정에 연결하도록 논리 앱에 권한을 부여합니다. Azure 포털의 논리 앱 내에서 이 작업을 쉽게 수행할 수 있습니다.  

다음 단계를 사용하여 Office 365 계정에 연결하도록 논리 앱에 권한을 부여합니다.

1. 논리 앱을 만듭니다. Logic Apps 디자이너의 드롭다운 목록에서 **Microsoft 관리 API 표시**를 선택한 다음 검색 상자에 "office 365"를 입력합니다. 트리거 또는 동작 중 하나를 선택합니다.  
    ![Office 365 연결 만들기 단계](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. 이전에 Office 365에 대한 연결을 만들지 않은 경우 Office 365 자격 증명을 사용하여 로그인하라는 메시지가 표시됩니다.  
    ![Office 365 연결 만들기 단계](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. **로그인**을 선택하고 사용자 이름 및 암호를 입력합니다. **로그인**을 선택합니다.  
    ![Office 365 연결 만들기 단계](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    이러한 자격 증명을 사용하여 Office 365 계정에 연결하도록 논리 앱에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다. 
4. 연결이 만들어졌는지 확인합니다. 이제 논리 앱의 다른 단계를 진행합니다.   
    ![Office 365 연결 만들기 단계](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

