---
title: 포털을 사용 하 여 피어 ASN을 Azure 구독에 연결
titleSuffix: Azure
description: 포털을 사용 하 여 피어 ASN을 Azure 구독에 연결
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d3737be5a3186774f230aef9d932464a27a764f4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775642"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>포털을 사용 하 여 피어 ASN을 Azure 구독에 연결

피어 링 요청을 제출 하기 전에 먼저 다음 단계를 사용 하 여 ASN을 Azure 구독과 연결 해야 합니다.

원하는 경우 [PowerShell](howto-subscription-association-powershell.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerAsn을 만들어 Azure 구독과 ASN 연결

### <a name="sign-in-to-the-portal"></a>포털에 로그인
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-peerasn"></a>PeerAsn 만들기
Azure 구독과 함께 ASN (자치 시스템 번호)을 연결 하기 위한 새 PeerAsn 리소스를 만들 수 있습니다. 연결 해야 하는 각 ASN에 대해 **Peerasn** 을 만들어 여러 asns를 구독에 연결할 수 있습니다.

1. **리소스 만들기를 클릭 하** > **모두 표시**를 클릭 합니다.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn](./media/peerasn-seeall.png) 검색

1. 검색 상자에서 *Peerasn* 을 검색 하 고 키보드에서 *enter 키* 를 누릅니다. 결과에서 **Peerasn** 리소스를 클릭 합니다.

    > [!div class="mx-imgBorder"]
    > PeerAsn](./media/peerasn-launch.png)를 시작 ![

1. **Peerasn** 이 시작 되 면 **만들기**를 클릭 합니다.

    > [!div class="mx-imgBorder"]
    > PeerAsn](./media/peerasn-create.png) ![만듭니다.

1. **피어 ASN 연결** 페이지의 **기본 사항** 탭에서 아래와 같이 필드를 입력 합니다.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 기본 탭](./media/peerasn-basics-tab.png)

    * **이름은** 리소스 이름에 해당 하며, 선택 하는 것이 될 수 있습니다.  
    * ASN과 연결 해야 하는 **구독** 을 선택 합니다.
    * **피어 이름은** 회사의 이름에 해당 하며, PeeringDB 프로필에 최대한 가까이 있어야 합니다. 값은 a-z, a-z 및 공백만 지원 합니다.
    * **피어 asn** 필드에 ASN을 입력 합니다.
    * **새로 만들기** 를 클릭 하 고 네트워크 운영 센터 (NOC)의 **전자 메일 주소** 및 **전화 번호** 를 입력 합니다.
1. 그런 다음 **검토 + 만들기** 를 클릭 하 고 포털에서 입력 한 정보에 대 한 기본 유효성 검사를 실행 합니다. *최종 유효성 검사를 실행*하는 것 처럼 위쪽의 리본에 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 검토 탭](./media/peerasn-review-tab-validation.png)

1. 리본의 메시지가 *유효성 검사 통과*로 바뀌고 나면 정보를 확인 하 고 **만들기**를 클릭 하 여 요청을 제출 합니다. 유효성 검사에 통과 하지 못하는 경우 **이전** 을 클릭 하 고 위의 단계를 반복 하 여 요청을 수정 하 고 입력 한 값에 오류가 없는지 확인 합니다.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 검토 탭](./media/peerasn-review-tab.png)

1. 요청을 제출한 후 배포가 완료 될 때까지 기다립니다. 배포에 실패 하는 경우 [Microsoft 피어 링](mailto:peering@microsoft.com)에 문의 하십시오. 성공적으로 배포 되 면 다음과 같이 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 성공](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>PeerAsn의 상태 보기
PeerAsn 리소스가 성공적으로 배포 되 면 Microsoft가 연결 요청을 승인할 때까지 기다려야 합니다. 승인 하는 데 최대 12 시간이 걸릴 수 있습니다. 승인 되 면 위 섹션에 입력 한 전자 메일 주소로 알림을 받게 됩니다.

> [!IMPORTANT]
> 상태를 피어 링 요청을 제출 하기 전에 ValidationState가 "승인 됨"으로 전환 될 때까지 기다립니다. 이 승인에는 최대 12 시간이 걸릴 수 있습니다.

## <a name="modify-peerasn"></a>PeerAsn 수정
PeerAsn 수정은 현재 지원 되지 않습니다. 수정 해야 하는 경우 [Microsoft 피어 링](mailto:peering@microsoft.com)에 문의 하세요.

## <a name="delete-peerasn"></a>PeerAsn 삭제
PeerAsn 삭제는 현재 지원 되지 않습니다. PeerAsn을 삭제 해야 하는 경우 [Microsoft 피어 링](mailto:peering@microsoft.com)에 문의 하세요.

## <a name="next-steps"></a>다음 단계

* [포털을 사용 하 여 직접 피어 링 만들기 또는 수정](howto-direct-portal.md)
* [포털을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)
* [포털을 사용 하 여 Exchange 피어 링 만들기 또는 수정](howto-exchange-portal.md)
* [포털을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어 링 faq](faqs.md) (영문)를 참조 하세요.