---
title: "엔터프라이즈 통합 팩에 AS2 규약을 만드는 방법 알아보기 | Microsoft Docs"
description: "엔터프라이즈 통합 팩에 AS2 규약을 만드는 방법 알아보기| Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 47a1c5653d84a55224c18222bc547e1c863ea442
ms.openlocfilehash: ea256557a38b3ce6cb457d195fa4b48cfd8879b8


---
# <a name="enterprise-integration-with-as2"></a>AS2와 엔터프라이즈 통합
## <a name="create-an-as2-agreement"></a>AS2 규약 만들기
논리 앱에서 엔터프라이즈 기능을 사용하려면 먼저 규약을 만들어야 합니다. 

### <a name="heres-what-you-need-before-you-get-started"></a>시작하기 전에 필요한 항목은 다음과 같습니다.
* Azure 구독에 정의된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-accounts.md)  
* 통합 계정에 이미 정의된 둘 이상의 [파트너](logic-apps-enterprise-integration-partners.md)  

> [!NOTE]
> 규약을 만들 때 규약 파일의 내용이 규약 형식과 일치해야 합니다.    
> 
> 

[통합 계정을 만들고](../logic-apps/logic-apps-enterprise-integration-accounts.md) [파트너를 추가](logic-apps-enterprise-integration-partners.md)한 후에 다음과 같은 단계에 따라 규약을 만들 수 있습니다.  

### <a name="from-the-azure-portal-home-page"></a>Azure 포털 홈페이지에서
[Azure Portal](http://portal.azure.com "Azure Portal")에 로그인한 후 다음을 수행합니다.  

1. **추가 서비스**를 선택하고 필터 검색 상자에 **통합**을 입력합니다. 결과 목록에서 **통합 계정**을 선택합니다.    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
2. 인증서를 추가할 통합 계정을 선택합니다. 
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
3. **규약** 타일을 선택합니다. 규약 타일이 표시되지 않으면 먼저 추가합니다.   
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
4. 열린 규약 블레이드에서 **추가** 단추를 선택합니다.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
5. 규약의 **이름**을 입력하고, 블레이드에서 **AS2**, **규약 형식**, **호스트 파트너**, **호스트 ID**, **게스트 파트너**, **게스트 ID**를 선택합니다.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

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

1. **수신 설정** 을 선택하여 규약을 통해 수신된 메시지를 처리하는 방법을 구성합니다.  
   
   * 필요에 따라 들어오는 메시지의 속성을 재정의할 수 있습니다. 이를 위해 **메시지 속성 재정의**를 선택합니다.
   * 들어오는 메시지를 전부 서명하려면 **메시지 서명 필요**를 선택합니다. 이 옵션을 선택하면 메시지 서명의 유효성을 검사할 *게스트 파트너 공용 인증서*를 선택해야 합니다.
   * 들어오는 메시지를 전부 암호화하려면 **메시지 암호화 필요**를 선택합니다.  이 옵션을 선택하면 들어오는 메시지를 해독할 *호스트 파트너 개인 인증서*를 선택해야 합니다.
   * 또한 메시지를 압축해야 할 수 있습니다. 이를 위해 **메시지 압축 필요**를 선택합니다.    
   * 받은 메시지에 대한 동기 MDN을 보내도록 **MDN 전송**을 선택합니다.
   * 받은 메시지에 대한 서명된 MDN을 보내도록 **서명된 MDN 전송**을 선택합니다.
   * 받은 메시지에 대한 비동기 MDN을 보내도록 **비동기 MDN 전송**을 선택합니다.     
    ![](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

수신 설정을 사용하는 방법에 대해 자세히 알아보려면 아래 테이블을 참조하세요.  

| 속성 | 설명 |
| --- | --- |
| 메시지 속성 재정의 |받은 메시지의 속성을 재정의할 수 있다는 것을 나타내려면 이 옵션을 선택합니다 |
| 메시지 서명 필요 |메시지를 디지털로 서명하도록 하려면 이 옵션을 사용합니다.  서명 확인을 위한 게스트 파트너 공용 인증서를 구성합니다.  |
| 메시지 암호화 필요 |메시지를 암호화하도록 하려면 이 옵션을 사용합니다. 암호화되지 않은 메시지는 거부됩니다. 메시지를 해독하기 위한 호스트 파트너 개인 인증서를 구성합니다.  |
| 메시지 압축 필요 |메시지를 압축하도록 하려면 이 옵션을 사용합니다. 압축되지 않은 메시지는 거부됩니다. |
| MDN 텍스트 |메시지 보낸 사람에게 보내야 하는 기본 MDN입니다. |
| MDN 전송 |MDN을 보낼 수 있도록 하려면 이 기능을 사용합니다. |
| 서명된 MDN 전송 |MDN을 서명하도록 하려면 이 기능을 사용합니다. |
| MIC 알고리즘 | |
| 비동기 MDN 전송 |메시지를 비동기적으로 전송하도록 하려면 이 옵션을 사용합니다. |
| URL |MDN을 전송할 URL입니다. |

다음을 계속합니다.  

1. **송신 설정** 을 선택하여 규약을 통해 전송된 메시지를 처리하는 방법을 구성합니다.  

   * 파트너에게 서명된 메시지를 보내도록 **메시지 서명 사용**을 선택합니다. 이 옵션을 선택하면 메시지를 서명하기 위해 *호스트 파트너 개인 인증서 MIC 알고리즘* 및 *호스트 파트너 개인 인증서*를 선택해야 합니다.
   * 파트너에게 암호화된 메시지를 보내도록 **메시지 암호화 사용**을 선택합니다. 이 옵션을 선택하면 메시지를 암호화하기 위해 *게스트 파트너 공용 인증서 알고리즘* 및 *게스트 파트너 공용 인증서*를 선택해야 합니다.
   * 메시지를 압축하려면 **메시지 압축 필요**를 선택합니다. 
   * HTTP 콘텐츠 형식 헤더를 단일 줄로 펼치려면 **HTTP 헤더 펼침**을 선택합니다. 
   * 보낸 메시지에 대한 동기 MDN을 받으려면 **MDN 요청**을 선택합니다.
   * 보낸 메시지에 대한 서명된 MDN을 받으려면 **서명된 MDN 요청**을 선택합니다.
   * 보낸 메시지에 대한 비동기 MDN을 받으려면 **비동기 MDN 요청**을 선택합니다. 이 옵션을 선택하면 MDN을 전송할 URL을 입력해야 합니다.  
   * NRR(수신 부인 방지)을 사용하려면 **NRR 사용**을 선택합니다.    
   ![](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

송신 설정을 사용하는 방법에 대해 자세히 알아보려면 아래 테이블을 참조하세요.  

| 속성 | 설명 |
| --- | --- |
| 메시지 서명 사용 |규약에서 보낸 모든 메시지를 서명할 수 있도록 하려면 이 옵션을 선택합니다. |
| MIC 알고리즘 |메시지 서명에 사용할 알고리즘을 선택합니다. 메시지를 서명할 호스트 파트너 개인 인증서 MIC 알고리즘을 구성합니다. |
| 인증서 |메시지 서명에 사용할 인증서를 선택합니다. 메시지를 서명할 호스트 파트너 개인 인증서를 구성합니다. |
| 메시지 암호화 사용 |규약에서 보낸 모든 메시지를 암호화하려면 이 옵션을 선택합니다. 메시지를 암호화하는 게스트 파트너 공용 인증서 알고리즘을 구성합니다. |
| 암호화 알고리즘 |메시지 암호화에 사용할 암호화 알고리즘을 선택합니다. 메시지를 암호화하는 게스트 파트너 공용 인증서를 구성합니다. |
| HTTP 헤더 펼침 |HTTP 콘텐츠 형식 헤더를 단일 줄로 펼치려면 이 옵션을 선택합니다. |
| MDN 요청 |이 규약에서 보낸 모든 메시지에 대한 MDN을 요청하려면 이 옵션을 사용합니다. |
| 서명된 MDN 요청 |이 규약에 전송된 모든 MDN을 서명하는 요청을 사용합니다. |
| 비동기 MDN 요청 |비동기 MDN을 이 규약에 전송하는 요청을 사용합니다. |
| URL |MDN을 전송할 URL입니다. |
| NRR 사용 |NRR(수신 부인 방지)을 사용하려면 이 옵션을 선택합니다. |

통합 계정 블레이드에서 **규약** 타일을 선택하면 새로 추가한 규약이 나열됩니다.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>다음 단계
* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")  


<!--HONumber=Jan17_HO4-->


