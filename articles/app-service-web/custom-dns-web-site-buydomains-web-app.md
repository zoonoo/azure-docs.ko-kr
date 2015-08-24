
<properties
	pageTitle="Azure 앱 서비스 웹 앱에서 사용자 지정 도메인 이름 구입 방법"
	description="Azure 앱 서비스에서 웹 앱으로 사용자 지정 도메인 이름을 구입하는 방법에 대해 알아봅니다."
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/21/2015"
	ms.author="mwasson"/>

# Azure 앱 서비스에서 사용자 지정 도메인 이름 구입 및 구성

> [AZURE.SELECTOR]
- [Buy Domain for Web Apps](custom-dns-web-site-buydomains-web-app.md)
- [Web Apps with External Domains](web-sites-custom-domain-name.md)
- [Web Apps with Traffic Manager](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)




[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

웹 앱을 만들면 Azure에서 azurewebsites.net의 하위 도메인에 할당됩니다. 예를 들어 웹 앱의 이름이 **contoso**인 경우 URL은 **contoso.azurewebsites.net**입니다. Azure는 가상 IP 주소도 할당합니다.

프로덕션 웹 앱의 경우 사용자에게 사용자 지정 도메인 이름을 표시할 수 있습니다. 이 문서에서는 [앱 서비스 웹 앱](http://go.microsoft.com/fwlink/?LinkId=529714)으로 사용자 지정 도메인을 구입 및 구성하는 방법에 대해 설명합니다.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## 개요

> [AZURE.NOTE]연결된 활성 신용 카드가 없는 구독을 사용하여 도메인을 구입하지 않도록 합니다. 구독을 사용할 수 없게 될 수 있습니다.

웹 앱에 대한 도메인 이름을 없는 경우 [Azure 관리 포털](https://portal.azure.com)에서 쉽게 구입할 수 있습니다. 구매 과정 중에 WWW 및 루트 도메인의 DNS 레코드가 웹 앱에 자동으로 매핑되도록 선택할 수 있습니다. 또한 Azure 포털 내에서 도메인을 관리할 수도 있습니다.


도메인 이름을 구입하고 웹 앱에 할당하려면 다음 단계를 수행합니다.

1. 브라우저에서 [Azure 관리 포털](https://portal.azure.com)을 엽니다.

2. **웹앱** 탭에서 웹앱의 이름을 클릭하고 **설정**을 선택한 다음 **사용자 지정 도메인 및 SSL**을 선택합니다.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. **사용자 지정 도메인 및 SSL** 블레이드에서 **도메인 구입**을 클릭합니다.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. **도메인 구입** 블레이드에서 텍스트 상자를 사용하여 구입할 도메인 이름을 입력하고 Enter 키를 누릅니다. 사용 가능한 도메인이 텍스트 상자 아래에 나타납니다. 구입할 도메인을 선택합니다. 여러 도메인을 한 번에 구입할 수 있습니다.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. **연락처 정보**를 클릭하고 도메인의 연락처 정보 양식을 작성합니다.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

> [AZURE.NOTE]모든 필수 필드(특히, 전자 메일 주소)를 가능한 한 정확하게 기입해야 합니다. "개인정보 보호" 없이 도메인을 구입하는 경우 도메인이 활성화되기 전에 사용자의 전자 메일을 확인하도록 요청하는 메시지가 표시될 수 있습니다. 경우에 따라 잘못된 연락처 정보 데이터로 인해 도메인을 구입하지 못할 수 있습니다.

6. 이제 다음과 같은 옵션을 선택할 수 있습니다.

	a) 매년 도메인 "자동 갱신"
	
	b) 무료로 구입 가격에 포함된 "개인정보 보호" 옵트인
	
	c) 현재 웹 앱에 WWW 및 루트 도메인의 "기본 호스트 이름 할당"

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
> [AZURE.NOTE]C 옵션은 DNS 바인딩 및 호스트 이름 바인딩을 자동으로 구성합니다. 이 방식으로, 구매를 완료하는 즉시(일부 경우, DNS 전파 지연 발생) 사용자 지정 도메인을 사용하여 웹 앱에 액세스할 수 있습니다. 웹 앱이 Azure 트래픽 관리자 뒤에 있는 경우, A-레코드가 트래픽 관리자에서 작동하지 않으므로 루트 도메인을 할당하는 옵션이 표시되지 않습니다.
>
>항상 하나의 웹 앱을 통해 구매한 도메인/하위 도메인을 다른 웹 앱에 할당할 수 있으며 그 반대의 경우도 수행할 수 있습니다. 자세한 내용은 8단계를 참조하세요.

	
7. **도메인 구입** 블레이드에서 **선택**을 클릭하면 **구매 확인** 블레이드에 구매 정보가 표시됩니다. 약관에 동의하고 **구입**을 클릭하면 주문이 제출되며, **알림**에서 구매 과정을 모니터링할 수 있습니다. 도메인 구매를 완료하는 데는 몇 분 정도 걸릴 수 있습니다. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. 도메인을 성공적으로 주문했으면 도메인을 관리하고 웹 앱에 할당할 수 있습니다. 도메인 오른쪽의 **"..."**를 클릭합니다. 그러면 **구매 취소** 또는 **도메인 관리**를 수행할 수 있습니다. **도메인 관리**를 클릭하면 **도메인 관리** 블레이드에서 **하위 도메인**을 웹 앱에 바인딩할 수 있습니다. **하위 도메인**을 다른 웹 앱에 바인딩하려는 경우는 해당 웹 앱의 컨텍스트 내에서 이 단계를 수행합니다. 여기서 트래픽 관리자를 드롭다운 메뉴에서 선택하기만 하면 도메인을 트래픽 관리자 끝점에 할당할 수 있습니다(웹 앱이 TM 뒤에 있는 경우). 이 작업을 수행하면 도메인/하위 도메인이 해당 트래픽 관리자 끝점 뒤에 있는 모든 웹 앱에 자동으로 할당됩니다. 

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

> [AZURE.NOTE]5일 이내에 "구매 취소"하면 전액 환불이 가능합니다. 5일 후에는 "구매 취소"를 할 수 없으며, 도메인 "삭제" 옵션이 대신 표시됩니다. 도메인을 삭제하면 환불 없이 구독에서 해제되어 사용 가능한 도메인이 됩니다.

	Once configuration has completed, the custom domain name will be listed in the **Hostname bindings** section of your web app.

이때 브라우저에서 사용자 지정 도메인 이름을 입력해야 웹 앱으로 이동하는지 확인할 수 있습니다.
 

<!---HONumber=August15_HO7-->