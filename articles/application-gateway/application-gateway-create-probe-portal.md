---
title: 포털을 사용하여 사용자 지정 프로브 만들기
titleSuffix: Azure Application Gateway
description: 포털을 사용하여 Application Gateway에 대한 사용자 지정 프로브를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074600"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>포털을 사용하여 Application Gateway에 대한 사용자 지정 프로브 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 클래식 파워쉘](application-gateway-create-probe-classic-ps.md)

이 문서에서는 Azure 포털을 통해 기존 응용 프로그램 게이트웨이에 사용자 지정 상태 프로브를 추가합니다. Azure 응용 프로그램 게이트웨이는 상태 프로브를 사용하여 백 엔드 풀에서 리소스의 상태를 모니터링합니다.

## <a name="before-you-begin"></a>시작하기 전에

Application Gateway 아직 없는 경우 방문 [Application Gateway 만들기](application-gateway-create-gateway-portal.md)를 참조하여 사용할 Application Gateway를 만듭니다.

## <a name="create-probe-for-application-gateway-v2-sku"></a>응용 프로그램 게이트웨이 v2 SKU용 프로브 생성

프로브는 포털을 통해 두 단계 프로세스로 구성됩니다. 첫 번째 단계는 프로브 구성에 필요한 값을 입력하는 것입니다. 두 번째 단계에서는 이 프로브 구성을 사용하여 백 엔드 상태를 테스트하고 프로브를 저장합니다. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>프로브 속성 입력

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 아직 계정이 없는 경우 [1개월 무료 평가판에](https://azure.microsoft.com/free) 등록할 수 있습니다.

2. Azure Portal의 [즐겨찾기] 창에서 [모든 리소스]를 클릭합니다. [모든 리소스] 블레이드에서 애플리케이션 게이트웨이를 클릭합니다. 선택한 구독에 이미 여러 리소스가 있는 경우 [이름으로 필터링...]에서 partners.contoso.net을 입력합니다. 애플리케이션 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

3. **상태 프로브를** 선택한 다음 새 상태 프로브를 추가하려면 **추가를** 선택합니다.

   ![새 프로브 추가][4]

4. 상태 **프로브 추가** 페이지에서 프로브에 필요한 정보를 입력하고 완료시 **확인을**선택합니다.

   |**설정** | **값** | **세부 정보**|
   |---|---|---|
   |**이름**|customProbe|이 값은 포털에서 액세스할 수 있는 프로브에 부여된 친숙한 이름입니다.|
   |**프로토콜**|HTTP 또는 HTTPS | 상태 프로브에서 사용하는 프로토콜입니다. |
   |**호스트**|예: contoso.com|이 값은 응용 프로그램 서버에서 실행되는 가상 호스트(VM 호스트 이름과 다른)의 이름입니다. 프로브는 (프로토콜)로 전송됩니다.://(호스트 이름):( httpsetting에서 포트)/urlPath.  이는 응용 프로그램 게이트웨이에서 다중 사이트가 구성된 경우에 적용됩니다. 응용 프로그램 게이트웨이가 단일 사이트에 대해 구성된 경우 '127.0.0.1'을 입력합니다.|
   |**백 엔드 HTTP 설정에서 호스트 이름 선택**|Yes 또는 No|프로브의 *호스트* 헤더를 이 프로브가 연관된 HTTP 설정과 연결된 백 엔드 풀의 백 엔드 리소스의 호스트 이름으로 설정합니다. Azure 앱 서비스와 같은 다중 테넌트 백엔드의 경우 특별히 필요합니다. [자세히 알아보기](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**경로**|/ 또는 다른 경로|사용자 지정 프로브의 전체 url 중 나머지 부분입니다. 유효한 경로는 '/'로 시작합니다. http의 기본 경로:\//contoso.com 그냥 사용 '/' |
   |**간격(초)**|30|상태를 확인하기 위해 프로브가 실행되는 주기입니다. 30초 이하 값을 설정하는 것은 권장되지 않습니다.|
   |**시간 제한(초)**|30|프로브가 타이밍이 바오기 전에 대기하는 시간입니다. 이 시간 지정 기간 내에 유효한 응답을 받지 못하면 프로브가 실패한 것으로 표시됩니다. 시간 제한 간격은 http 호출이 백 엔드 상태 페이지를 사용하도록 설정할 수 있을만큼 충분히 높아야 합니다. 시간 시간 값은 이 프로브 설정에 사용되는 '간격' 값 또는 이 프로브와 연결될 HTTP 설정의 '요청 시간 초과' 값을 초과해서는 안 됩니다.|
|**비정상 임계값**|3|비정상으로 간주되는 연속 실패 시도 횟수입니다. 임계값을 1 이상으로 설정할 수 있습니다.|
   |**프로브 일치 조건 사용**|Yes 또는 No|기본적으로 상태 코드가 200에서 399 사이인 HTTP(S) 응답은 정상으로 간주됩니다. 백 엔드 응답 코드 또는 백 엔드 응답 본문의 허용 범위를 변경할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**HTTP 설정**|드롭다운에서 선택|프로브는 여기에서 선택한 HTTP 설정과 연결되므로 선택한 HTTP 설정과 연결된 해당 백 엔드 풀의 상태를 모니터링합니다. 선택한 HTTP 설정에서 사용되는 것과 프로브 요청에 동일한 포트를 사용합니다. 다른 사용자 지정 프로브와 연결되지 않은 HTTP 설정만 선택할 수 있습니다. <br>이 프로브 구성에서 선택한 프로토콜과 동일한 프로토콜을 가지며 백 엔드 HTTP *설정 스위치에서 호스트 이름 선택* 스위치에 대해 동일한 상태를 가진 연결에는 이러한 HTTP 설정만 사용할 수 있습니다.|
   
   > [!IMPORTANT]
   > 프로브는 하나 이상의 HTTP 설정과 연결된 경우에만 백 엔드의 상태를 모니터링합니다. 이 프로브가 연관된 HTTP 설정에 연결된 백 엔드 풀의 백 엔드 리소스를 모니터링합니다. 프로브 요청은 http://(호스트 이름):(httpsetting에서 포트)/urlPath로 전송됩니다.

### <a name="test-backend-health-with-the-probe"></a>프로브로 백엔드 상태 테스트

프로브 속성을 입력한 후 백 엔드 리소스의 상태를 테스트하여 프로브 구성이 올바른지, 백 엔드 리소스가 예상대로 작동하는지 확인할 수 있습니다.

1. **테스트를** 선택하고 프로브의 결과를 기록합니다. 응용 프로그램 게이트웨이는 이 프로브에 사용되는 HTTP 설정과 연결된 백 엔드 풀의 모든 백 엔드 리소스의 상태를 테스트합니다. 

   ![백엔드 상태 테스트][5]

2. 비정상 백 엔드 리소스가 있는 경우 **세부 정보** 열을 확인하여 리소스의 비정상 상태 이유를 이해합니다. 잘못된 프로브 구성으로 인해 리소스가 비정상으로 표시된 경우 **다시 프로브 링크로 이동을** 선택하고 프로브 구성을 편집합니다. 그렇지 않으면 백 엔드 문제로 인해 리소스가 비정상으로 표시된 경우 백 엔드 리소스문제를 해결한 다음 다시 **프로브 링크로 이동을** 선택하고 **Test를**선택하여 백 엔드를 다시 테스트합니다.

   > [!NOTE]
   > 비정상 백 엔드 리소스를 사용하여도 프로브를 저장하도록 선택할 수 있지만 권장되지는 않습니다. 이는 응용 프로그램 게이트웨이가 프로브에 의해 비정상으로 결정되는 백 엔드 풀에서 해당 백 엔드 리소스를 제거하기 때문입니다. 백 엔드 풀에 정상 리소스가 없는 경우 응용 프로그램에 액세스할 수 없으며 502 오류가 발생합니다.

   ![프로브 결과 보기][6]

3. 프로브를 저장하려면 **추가를** 선택합니다. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>응용 프로그램 게이트웨이 v1 SKU용 프로브 생성

프로브는 포털을 통해 두 단계 프로세스로 구성됩니다. 첫 번째 단계는 프로브를 만드는 것입니다. 두 번째 단계로 애플리케이션 게이트웨이의 백 엔드 http 설정에 프로브를 추가합니다.

### <a name="create-the-probe"></a><a name="createprobe"></a>프로브 만들기

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 아직 계정이 없는 경우 [1개월 무료 평가판에](https://azure.microsoft.com/free) 등록할 수 있습니다.

2. Azure Portal의 즐겨찾기 창에서 **모든 리소스**를 선택합니다. **모든 리소스** 페이지에서 응용 프로그램 게이트웨이를 선택합니다. 선택한 구독에 이미 여러 리소스가 있는 경우 [이름으로 필터링...]에서 partners.contoso.net을 입력합니다. 애플리케이션 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

3. 프로브를 선택한 다음 **추가를** 선택하여 프로브를 **추가합니다.**

   ![정보가 입력된 프로브 추가 블레이드][1]

4. 상태 프로브 블레이드 **추가에서** 프로브에 필요한 정보를 입력하고 완료되면 **확인을**선택합니다.

   |**설정** | **값** | **세부 정보**|
   |---|---|---|
   |**이름**|customProbe|이 값은 포털에서 액세스할 수 있는 프로브에 부여된 친숙한 이름입니다.|
   |**프로토콜**|HTTP 또는 HTTPS | 상태 프로브에서 사용하는 프로토콜입니다. |
   |**호스트**|예: contoso.com|이 값은 응용 프로그램 서버에서 실행되는 가상 호스트(VM 호스트 이름과 다른)의 이름입니다. 프로브는 (프로토콜)로 전송됩니다.://(호스트 이름):( httpsetting에서 포트)/urlPath.  이는 응용 프로그램 게이트웨이에서 다중 사이트가 구성된 경우에 적용됩니다. 응용 프로그램 게이트웨이가 단일 사이트에 대해 구성된 경우 '127.0.0.1'을 입력합니다.|
   |**백 엔드 HTTP 설정에서 호스트 이름 선택**|Yes 또는 No|프로브의 *호스트* 헤더를 이 프로브가 연관된 HTTP 설정과 연결된 백 엔드 풀의 백 엔드 리소스의 호스트 이름으로 설정합니다. Azure 앱 서비스와 같은 다중 테넌트 백엔드의 경우 특별히 필요합니다. [자세히 알아보기](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**경로**|/ 또는 다른 경로|사용자 지정 프로브의 전체 url 중 나머지 부분입니다. 유효한 경로는 '/'로 시작합니다. http의 기본 경로:\//contoso.com 그냥 사용 '/' |
   |**간격(초)**|30|상태를 확인하기 위해 프로브가 실행되는 주기입니다. 30초 이하 값을 설정하는 것은 권장되지 않습니다.|
   |**시간 제한(초)**|30|프로브가 타이밍이 바오기 전에 대기하는 시간입니다. 이 시간 지정 기간 내에 유효한 응답을 받지 못하면 프로브가 실패한 것으로 표시됩니다. 시간 제한 간격은 http 호출이 백 엔드 상태 페이지를 사용하도록 설정할 수 있을만큼 충분히 높아야 합니다. 시간 시간 값은 이 프로브 설정에 사용되는 '간격' 값 또는 이 프로브와 연결될 HTTP 설정의 '요청 시간 초과' 값을 초과해서는 안 됩니다.|
|**비정상 임계값**|3|비정상으로 간주되는 연속 실패 시도 횟수입니다. 임계값을 1 이상으로 설정할 수 있습니다.|
   |**프로브 일치 조건 사용**|Yes 또는 No|기본적으로 상태 코드가 200에서 399 사이인 HTTP(S) 응답은 정상으로 간주됩니다. 백 엔드 응답 코드 또는 백 엔드 응답 본문의 허용 범위를 변경할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > 호스트 이름은 서버 이름과 같지 않습니다. 이 값은 애플리케이션 서버에서 실행 중인 가상 호스트의 이름입니다. 프로브는 http://(host name):(port from httpsetting)/urlPath로 전송됩니다.

### <a name="add-probe-to-the-gateway"></a>게이트웨이에 프로브 추가

프로브를 만들었으므로 이제 게이트웨이에 추가해야 합니다. 프로브 설정은 애플리케이션 게이트웨이의 백 엔드 http 설정에 지정됩니다.

1. 애플리케이션 게이트웨이에서 **HTTP 설정**을 클릭하여 구성 블레이드를 불러온 다음, 창에 나열된 현재 백 엔드 http 설정을 클릭합니다.

   ![https 설정 창][2]

2. **appGatewayBackEndHttpSettings** 설정 페이지에서 사용자 **지정 프로브 사용** 확인란을 선택하고 **사용자 지정 프로브** 드롭다운의 프로브 만들기 섹션에서 만든 [프로브를](#createprobe) 선택합니다.
   완료되면 **저장**을 클릭하여 해당 설정을 적용합니다.

## <a name="next-steps"></a>다음 단계

백 엔드 상태 보기를 사용하여 프로브에 의해 결정된 [백 엔드](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)리소스의 상태를 봅니다.

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
