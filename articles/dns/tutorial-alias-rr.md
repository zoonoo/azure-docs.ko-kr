---
title: 자습서 - 영역에서 리소스 레코드를 참조하는 Azure DNS 별칭 레코드를 만듭니다.
description: 이 자습서에서는 영역 내에서 리소스 레코드를 참조하도록 Azure DNS 별칭 레코드를 구성하는 방법을 보여줍니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 09c1768602fede51d7ff2b23f48278a1d0b0cd2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990844"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>자습서: 영역 리소스 레코드를 참조하는 별칭 레코드 만들기

별칭 레코드는 동일한 유형의 다른 레코드 집합을 참조할 수 있습니다. 예를 들어 DNS CNAME 레코드 집합을 동일한 유형의 다른 CNAME 레코드 집합에 대한 별칭으로 설정할 수 있습니다. 일부 레코드 집합을 별칭으로 설정하고 일부를 별칭이 아닌 항목으로 설정하려는 경우 이 기능이 유용합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 영역에서 리소스 레코드에 대한 별칭 레코드 만들기
> * 별칭 레코드 테스트


Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건
테스트할 Azure DNS에서 호스트할 수 있는 도메인 이름이 있어야 합니다. 도메인의 NS(이름 서버) 레코드를 설정하는 기능을 포함하여 이 도메인을 완전히 제어해야 합니다.

Azure DNS에서 도메인을 호스트하기 위한 지침은 [자습서: Azure DNS에 도메인 호스트](dns-delegate-domain-azure-dns.md)를 참조하세요.


## <a name="create-an-alias-record"></a>별칭 레코드 만들기

영역에서 리소스 레코드를 가리키는 별칭 레코드를 만듭니다.

### <a name="create-the-target-resource-record"></a>대상 리소스 레코드 만들기
1. Azure DNS 영역을 클릭하여 영역을 엽니다.
2. **레코드 집합**을 클릭합니다.
3. **이름** 텍스트 상자에 **서버**를 입력합니다.
4. **형식**에 대해 **A**를 선택합니다.
5. **IP 주소** 텍스트 상자에 **10.10.10.10**을 입력합니다.
6. **확인**을 클릭합니다.

### <a name="create-the-alias-record"></a>별칭 레코드 만들기
1. Azure DNS 영역을 클릭하여 영역을 엽니다.
2. **레코드 집합**을 클릭합니다.
3. **이름** 텍스트 상자에 **테스트**를 입력합니다.
4. **형식**에 대해 **A**를 선택합니다.
5. **별칭 레코드 집합** 확인란에서 **예**를 클릭하고 **영역 레코드 집합** 옵션을 선택합니다.
6. **영역 레코드 집합**에 대해 **서버** 레코드를 선택합니다.
7. **확인**을 클릭합니다.

## <a name="test-the-alias-record"></a>별칭 레코드 테스트

1. 즐겨찾는 nslookup 도구를 시작합니다. 한 가지 방법은 [https://network-tools.com/nslook](https://network-tools.com/nslook)으로 이동하는 것입니다.
2. A 레코드에 대한 쿼리 유형을 설정하고 **테스트\< 도메인 이름\>** 을 조회합니다. 답변에 대해 **10.10.10.10**을 가져와야 합니다.
3. Azure Portal에서 **서버** A 레코드를 **10.11.11.11**로 변경합니다.
4. 몇 분 정도 기다린 다음, **테스트** 레코드에 대해 nslookup을 다시 사용합니다.
5. 답변에 대해 **10.11.11.11**을 가져와야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 영역에서 **서버** 및 **테스트** 리소스 레코드를 삭제할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 영역 내에서 리소스 레코드를 참조하는 별칭 레코드를 만들었습니다. Azure DNS 및 웹앱에 대해 자세히 알아보려면 웹앱에 대한 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](./dns-web-sites-custom-domain.md)
