---
title: "Azure Active Directory의 명명된 위치 | Microsoft Docs"
description: "명명된 위치를 구성하여 조직에서 소유한 IP 주소가 비정상적 위치 간 이동 불가능 위험 이벤트 유형에 대해 거짓 긍정을 생성하지 않도록 할 수 있습니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9637c56cf88ccf4c54fda789d4e7e3ca3e0a4fed
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="named-locations-in-azure-active-directory"></a>Azure Active Directory의 명명된 위치

명명된 위치는 조직 내에서 신뢰할 수 있는 IP 주소 범위에 레이블을 지정할 수 있는 Azure Active Directory의 기능입니다. 사용자 환경에서는 [위험 이벤트](active-directory-reporting-risk-events.md)를 감지한 경우 명명된 위치를 사용하여 *비정상적 위치 간 이동 불가능* 위험 이벤트 유형에 대해 보고된 거짓 긍정 수를 줄일 수 있습니다. 




## <a name="configuration"></a>구성

**명명된 위치를 구성하려면:**

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **Azure Active Directory**를 클릭합니다.

    ![명명된 위치](./media/active-directory-named-locations/01.png)

3. **Azure Active Directory** 블레이드의 **보안** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![명명된 위치](./media/active-directory-named-locations/05.png)


4. **조건부 액세스** 블레이드의 **관리** 섹션에서 **명명된 위치**를 클릭합니다.

    ![명명된 위치](./media/active-directory-named-locations/06.png)


5. 상단 메뉴의 **명명된 위치** 블레이드에서 **새 위치**를 클릭합니다.

    ![명명된 위치](./media/active-directory-named-locations/07.png)


6. **새** 블레이드에서 다음 단계를 수행합니다.

    ![명명된 위치](./media/active-directory-named-locations/08.png)

    a. **이름** 텍스트 상자에 명명된 위치의 이름을 입력합니다.

    b. **IP 범위** 텍스트 상자에 IP 범위를 입력합니다. IP 범위는 CIDR(*Classless Inter-Domain Routing*) 형식이어야 합니다.  

    c. **만들기**를 클릭합니다.



## <a name="what-you-should-know"></a>알아야 할 사항

**대량 업데이트** - 명명된 위치를 만들거나 업데이트하는 경우 대량 업데이트를 위해 IP 주소가 포함된 CSV 파일을 업로드 또는 다운로드할 수 있습니다. 업로드를 수행하면 목록을 덮어쓰지 않고 목록에 파일의 IP 범위가 추가됩니다.

![명명된 위치](./media/active-directory-named-locations/09.png)


**제한 사항** - 명명된 위치는 최대 60개까지 정의할 수 있으며 각 위치에는 IP 범위를 한 개만 할당할 수 있습니다. 명명된 위치가 하나만 구성된 경우 해당 위치에 대해 IP 범위를 최대 500개까지 정의할 수 있습니다.


## <a name="next-steps"></a>다음 단계

다음에 대한 자세한 정보:

- 위험 이벤트는 [Azure Active Directory 위험 이벤트](active-directory-reporting-risk-events.md)를 참조하세요.


