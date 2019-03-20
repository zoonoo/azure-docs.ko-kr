---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "58115949"
---
도메인을 사용하려는 경우 [Azure 관리 포털](https://portal.azure.com)에서 도메인을 직접 구입할 수 있습니다. 도메인 이름을 구입하고 웹 앱에 할당하려면 다음 단계를 수행합니다.

1. 브라우저에서 [Azure 관리 포털](https://portal.azure.com)을 엽니다.
2. **Web Apps** 탭에서 웹앱의 이름을 클릭하고 **설정**을 선택한 다음 **사용자 지정 도메인 및 SSL**을 선택합니다.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. **사용자 지정 도메인 및 SSL** 블레이드에서 **도메인 구입**을 클릭합니다.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. **도메인 구입** 블레이드에서 텍스트 상자를 사용하여 구입할 도메인 이름을 입력합니다. 사용 가능한 도메인이 텍스트 상자 아래에 나타납니다. 구입할 도메인을 선택합니다.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. **연락처 정보**를 클릭하고 도메인의 연락처 정보 양식을 작성합니다.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. **도메인 구입** 블레이드에서 **선택**을 클릭하면 **구매 확인** 블레이드에 구매 정보가 표시됩니다. 약관에 동의하고 **구입**을 클릭하면 주문이 제출되며, **알림**에서 구매 과정을 모니터링할 수 있습니다.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. 도메인을 성공적으로 주문했으면 도메인을 관리하고 웹 앱에 할당할 수 있습니다. 도메인 오른쪽의 **"..."** 를 클릭합니다. 그러면 **구매 취소** 또는 **도메인 관리**를 수행할 수 있습니다. **도메인 관리**를 클릭하면 **도메인 관리** 블레이드에서 **하위 도메인**을 웹앱에 바인딩할 수 있습니다.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    구성이 완료되면 사용자 지정 도메인 이름이 웹 앱의 **호스트 이름 바인딩** 섹션에 표시됩니다.

이때 브라우저에서 사용자 지정 도메인 이름을 입력해야 웹 앱으로 이동하는지 확인할 수 있습니다.

