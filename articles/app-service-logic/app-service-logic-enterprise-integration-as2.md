---
title: 엔터프라이즈 통합 팩에 AS2 규약을 만드는 방법 알아보기
description: 엔터프라이즈 통합 팩에 AS2 규약을 만드는 방법 알아보기 | Microsoft Azure 앱 서비스
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe

---
# AS2와 엔터프라이즈 통합
## AS2 규약 만들기
논리 앱에서 엔터프라이즈 기능을 사용하려면 먼저 규약을 만들어야 합니다.

### 시작하기 전에 필요한 항목은 다음과 같습니다.
* Azure 구독에 정의된 [통합 계정](app-service-logic-enterprise-integration-accounts.md)
* 통합 계정에 이미 정의된 둘 이상의 [파트너](app-service-logic-enterprise-integration-partners.md)

> [!NOTE]
> 규약을 만들 때 규약 파일의 내용이 규약 형식과 일치해야 합니다.
> 
> 

[통합 계정을 만들고](app-service-logic-enterprise-integration-accounts.md) [파트너를 추가](app-service-logic-enterprise-integration-partners.md)한 후에 다음과 같은 단계에 따라 규약을 만들 수 있습니다.

### Azure 포털 홈페이지에서
[Azure 포털](http://portal.azure.com "Azure 포털")에 로그인한 후에 다음을 수행합니다.

1. 왼쪽 메뉴에서 **찾아보기**를 선택합니다.

> [!TIP]
> **찾아보기** 링크가 표시되지 않으면 먼저 메뉴를 확장해야 합니다. 축소된 메뉴의 왼쪽 위에 있는 **표시 메뉴** 링크를 선택하여 이 작업을 수행합니다.
> 
> 

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)

1. 필터 검색 상자에 *통합*을 입력하고 결과 목록에서 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
2. 열린 **통합 계정** 블레이드에서 규약을 만들 통합 계정을 선택합니다. 통합 계정 목록이 표시되지 않으면 [먼저 계정을 만듭니다](app-service-logic-enterprise-integration-accounts.md "통합 계정에 대한 모든 정보"). ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
3. **규약** 타일을 선택합니다. 규약 타일이 표시되지 않으면 먼저 추가합니다. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)
4. 열린 규약 블레이드에서 **추가** 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)
5. 규약의 **이름**을 입력한 다음 열린 규약 블레이드에서 **호스트 파트너**, **호스트 ID**, **게스트 파트너**, **게스트 ID**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)

규약에 대한 설정을 구성하는 경우 유용할 수 있는 몇 가지 세부 사항은 다음과 같습니다.

| 속성 | 설명 |
| --- | --- |
| 호스트 파트너 |규약에는 호스트 및 게스트 파트너가 필요합니다. 호스트 파트너는 규약을 구성하는 조직을 나타냅니다. |
| 호스트 ID |호스트 파트너에 대한 식별자입니다. |
| 게스트 파트너 |규약에는 호스트 및 게스트 파트너가 필요합니다. 게스트 파트너는 호스트 파트너와 함께 일하는 조직을 나타냅니다. |
| 게스트 ID |게스트 파트너에 대한 식별자입니다. |
| 수신 설정 |규약에서 받은 모든 메시지에 이러한 속성을 적용합니다. |
| 송신 설정 |규약에서 보낸 모든 메시지에 이러한 속성을 적용합니다. |

다음을 계속합니다.

1. **수신 설정**을 선택하여 규약을 통해 수신된 메시지를 처리하는 방법을 구성합니다.
   
   * 필요에 따라 들어오는 메시지의 속성을 재정의할 수 있습니다. 이를 위해 **메시지 속성 재정의** 확인란을 선택합니다.
   * 모든 들어오는 메시지를 서명하려는 경우 **메시지 서명 필요** 확인란을 선택합니다. 옵션을 선택하면 메시지 서명의 유효성을 검사하는 데 사용할 **인증서**도 선택해야 합니다.
   * 또한 필요에 따라 메시지를 암호화해야 할 수 있습니다. 이를 위해 **메시지 암호화 필요** 확인란을 선택합니다. 그런 다음 들어오는 메시지를 디코딩하는 데 사용하는 **인증서**를 선택해야 합니다.
   * 또한 메시지를 압축해야 할 수 있습니다. 이를 위해 **메시지 압축 필요** 확인란을 선택합니다. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)

수신 설정을 사용하는 방법에 대해 자세히 알아보려면 아래 테이블을 참조하세요.

| 속성 | 설명 |
| --- | --- |
| 메시지 속성 재정의 |받은 메시지의 속성을 재정의할 수 있다는 것을 나타내려면 이 옵션을 선택합니다 |
| 메시지 서명 필요 |메시지를 디지털로 서명하도록 하려면 이 옵션을 사용합니다. |
| 메시지 암호화 필요 |메시지를 암호화하도록 하려면 이 옵션을 사용합니다. 암호화되지 않은 메시지는 거부됩니다. |
| 메시지 압축 필요 |메시지를 압축하도록 하려면 이 옵션을 사용합니다. 압축되지 않은 메시지는 거부됩니다. |
| MDN 텍스트 |메시지 보낸 사람에게 보내야 하는 기본 MDN입니다. |
| MDN 전송 |MDN을 보낼 수 있도록 하려면 이 기능을 사용합니다. |
| 서명된 MDN 전송 |MDN을 서명하도록 하려면 이 기능을 사용합니다. |
| MIC 알고리즘 | |
| 비동기 MDN 전송 |메시지를 비동기적으로 전송하도록 하려면 이 옵션을 사용합니다. |
| URL |메시지를 전송할 URL입니다. |

다음을 계속합니다.

1. **송신 설정**을 선택하여 규약을 통해 전송된 메시지를 처리하는 방법을 구성합니다. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)

송신 설정을 사용하는 방법에 대해 자세히 알아보려면 아래 테이블을 참조하세요.

| 속성 | 설명 |
| --- | --- |
| 메시지 서명 사용 |규약에서 보낸 모든 메시지를 서명할 수 있도록 하려면 이 확인란을 선택합니다. |
| MIC 알고리즘 |메시지 서명에서 사용할 알고리즘을 선택합니다. |
| 인증서 |메시지 서명에서 사용할 인증서를 선택합니다. |
| 메시지 암호화 사용 |규약에서 보낸 모든 메시지를 암호화하려면 이 확인란을 선택합니다. |
| 암호화 알고리즘 |메시지 암호화에 사용할 암호화 알고리즘을 선택합니다. |
| HTTP 헤더 펼침 |HTTP 콘텐츠 형식 헤더를 단일 줄로 펼치려면 이 확인란을 선택합니다. |
| MDN 요청 |규약에서 보낸 모든 메시지에 대한 MDN을 요청하려면 이 확인란을 사용합니다. |
| 서명된 MDN 요청 |이 규약에 전송된 모든 MDN을 서명하는 요청을 사용합니다. |
| 비동기 MDN 요청 |비동기 MDN을 이 규약에 전송하는 요청을 사용합니다. |
| URL |MDN을 전송할 URL입니다. |
| NRR 사용 |NRR(수신 부인 방지)을 사용하려면 이 확인란을 선택합니다. |

이제 거의 완료되었습니다.

1. 통합 계정 블레이드에서 **규약** 타일을 선택하면 새로 추가한 규약이 나열됩니다. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)

<!---HONumber=AcomDC_0803_2016-->