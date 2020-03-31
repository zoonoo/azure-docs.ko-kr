---
title: 포털을 사용하여 피어 ASN을 Azure 구독에 연결
titleSuffix: Azure
description: 포털을 사용하여 피어 ASN을 Azure 구독에 연결
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912209"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>포털을 사용하여 피어 ASN을 Azure 구독에 연결

피어링 요청을 제출하기 전에 먼저 아래 단계를 사용하여 ASN을 Azure 구독과 연결해야 합니다.

원하는 경우 [PowerShell](howto-subscription-association-powershell.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>피어라젠 생성하여 ASN을 Azure 구독과 연결

### <a name="sign-in-to-the-portal"></a>포털에 로그인
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>피어링 리소스 공급자등록
아래 단계에 따라 구독에서 피어링 리소스 공급자에 등록합니다. 이 작업을 실행하지 않으면 피어링을 설정하는 데 필요한 Azure 리소스에 액세스할 수 없습니다.

1. 포털의 왼쪽 상단 모서리에 있는 **구독을** 클릭합니다. 표시되지 않으면 **추가 서비스를** 클릭하고 검색합니다.

    > [!div class="mx-imgBorder"]
    > ![구독 열기](./media/rp-subscriptions-open.png)

1. 피어링에 사용할 구독을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![구독 시작](./media/rp-subscriptions-launch.png)

1. 구독이 열리면 왼쪽에서 리소스 **공급자를**클릭합니다. 그런 다음 오른쪽 창에서 검색 창에서 *피어링을* 검색하거나 스크롤 막대를 사용하여 **Microsoft.Peering을** 찾고 **상태를**봅니다. 상태가 ***등록된***경우 아래 단계를 건너뛰고 **PeerAsn 만들기**섹션으로 진행합니다. 상태가 ***등록되지 않은***경우 **Microsoft.Peering을** 선택하고 **등록을**클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![등록 시작](./media/rp-register-start.png)

1. 상태가 ***등록으로 변경되는지 관찰합니다.***

    > [!div class="mx-imgBorder"]
    > ![등록 진행 중](./media/rp-register-progress.png)

1. 등록이 완료될 때까지 잠시 기다립니다. 그런 다음 **새로 고침을** 클릭하고 상태가 ***등록되어***있는지 확인합니다.

    > [!div class="mx-imgBorder"]
    > ![등록 완료](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>피어라센 만들기
Azure 구독과 ASN(자율 시스템 번호)을 연결하기 위한 새 PeerAsn 리소스를 만들 수 있습니다. 연결해야 하는 각 ASN에 대해 **PeerAsn을** 만들어 여러 ASN을 구독에 연결할 수 있습니다.

1. **리소스** > **만들기를 클릭합니다 모두 보기.**

    > [!div class="mx-imgBorder"]
    > ![피어라센 검색](./media/peerasn-seeall.png)

1. 검색 상자에서 *PeerAsn을* 검색하고 키보드에서 *Enter를* 누른 다음 을 누른다. 결과에서 **PeerAsn** 리소스를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![발사 피어아센](./media/peerasn-launch.png)

1. **PeerAsn이** 시작되면 **만들기를**클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![피어라센 만들기](./media/peerasn-create.png)

1. 피어 **ASN 연결** 페이지에서 기본 탭 아래에 아래와 같이 필드를 **입력합니다.**

    > [!div class="mx-imgBorder"]
    > ![피어라센 기본 탭](./media/peerasn-basics-tab.png)

    * **이름은** 리소스 이름에 해당하며 원하는 모든 이름을 지정할 수 있습니다.  
    * ASN을 연결해야 하는 **구독을** 선택합니다.
    * **피어 이름은** 회사 이름에 해당하며 PeeringDB 프로필에 최대한 가까워야 합니다. 값은 문자 a-z, A-Z 및 공백만 지원합니다.
    * 피어 ASN 필드에 **ASN을** 입력합니다.
    * 새 **만들기를** 클릭하고 네트워크 운영 센터(NOC)의 **이메일 주소** 및 **전화 번호를** 입력합니다.
1. 그런 다음 **검토 + 만들고** 포털이 입력한 정보의 기본 유효성 검사를 실행하는지 확인합니다. 이 표시 는 맨 위에 리본에 표시 됩니다., *실행 최종 유효성 검사...*

    > [!div class="mx-imgBorder"]
    > ![피어라센 리뷰 탭](./media/peerasn-review-tab-validation.png)

1. 리본의 메시지가 *유효성 검사 전달로*바뀌면 정보를 확인하고 **만들기를**클릭하여 요청을 제출합니다. 유효성 검사가 통과하지 못하면 **이전을** 클릭하고 위의 단계를 반복하여 요청을 수정하고 입력한 값에 오류가 없는지 확인합니다.

    > [!div class="mx-imgBorder"]
    > ![피어라센 리뷰 탭](./media/peerasn-review-tab.png)

1. 요청을 제출한 후 배포가 완료될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하십시오. 성공적인 배포는 아래와 같이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어라센성공](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>피어라센의 상태 보기
PeerAsn 리소스가 성공적으로 배포되면 Microsoft에서 연결 요청을 승인할 때까지 기다려야 합니다. 승인까지 최대 12시간이 소요될 수 있습니다. 승인되면 위의 섹션에 입력한 이메일 주소로 알림을 받게 됩니다.

> [!IMPORTANT]
> 피어링 요청을 제출하기 전에 유효성 검사상태가 "승인됨"으로 바뀔 때까지 기다립니다. 이 승인에는 최대 12시간이 걸릴 수 있습니다.

## <a name="modify-peerasn"></a>피어라센 수정
PeerAsn 수정은 현재 지원되지 않습니다. 수정해야 하는 경우 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하십시오.

## <a name="delete-peerasn"></a>피어라센 삭제
피어라젠 삭제는 현재 지원되지 않습니다. PeerAsn을 삭제해야 하는 경우 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하십시오.

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 직접 피어링 만들기 또는 수정](howto-direct-portal.md)
* [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)
* [포털을 사용하여 Exchange 피어링 만들기 또는 수정](howto-exchange-portal.md)
* [포털을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어링 자주 묻는 질문(FAQ)을 방문하십시오.](faqs.md)