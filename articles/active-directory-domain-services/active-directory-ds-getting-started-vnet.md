<properties
	pageTitle="Azure Active Directory 도메인 서비스 미리 보기: 시작 | Microsoft Azure"
	description="Azure Active Directory 도메인 서비스 시작"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스*(미리 보기)* - 시작

## Azure 가상 네트워크 선택 지침
Azure AD 도메인 서비스와 함께 사용할 가상 네트워크를 선택할 때 다음 지침을 명심해야 합니다.

- Azure AD 도메인 서비스에서 지원하는 지역에서 가상 네트워크를 선택해야 합니다. Azure AD 도메인 서비스를 사용할 수 있는 Azure 지역을 알아보려면 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services/) 페이지를 참조하세요.
- 기존 가상 네트워크를 사용할 계획인 경우 해당 네트워크가 지역 가상 네트워크인지 확인합니다. 레거시 선호도 그룹 메커니즘을 사용하는 가상 네트워크는 Azure AD 도메인 서비스와 함께 사용할 수 없습니다. [레거시 가상 네트워크를 지역 가상 네트워크로 마이그레이션](../virtual-networks-migrate-to-regional-vnet.md)해야 합니다.
- 기존 가상 네트워크를 사용하려는 경우 가상 네트워크에 대해 구성된 사용자 지정 DNS 서버가 없는지 확인합니다. Azure AD 도메인 서비스에서는 DNS 서버 사용자 지정/가져오기를 지원하지 않습니다.
- 기존 가상 네트워크를 사용하려는 경우 해당 가상 네트워크에서 동일한 도메인 이름을 사용하는 기존 도메인이 없는지 확인합니다. 예를 들어, 선택한 가상 네트워크에서 'contoso.com'이라는 도메인을 이미 사용한다고 가정합니다. 이후 해당 가상 네트워크에서 동일한 도메인 이름(예: 'contoso.com')으로 Azure AD 도메인 서비스 관리되는 도메인을 사용하도록 설정하려고 합니다. Azure AD 도메인 서비스를 사용하도록 설정하면 오류가 발생합니다. 해당 가상 네트워크에서 도메인 이름이 충돌하기 때문입니다. 이 경우 다른 이름을 사용하여 Azure AD 도메인 서비스 관리되는 도메인을 설정해야 합니다. 또는 기존 도메인을 프로비전 해제한 후 Azure AD 도메인 서비스를 사용하도록 설정할 수 있습니다.
- Azure AD 도메인 서비스에 액세스해야 하는 가상 컴퓨터를 현재 호스트하거나 호스트할 가상 네트워크를 선택합니다. 이 서비스를 사용하도록 설정한 후에는 도메인 서비스를 다른 가상 네트워크로 이동할 수 없습니다.
- Azure AD 도메인 서비스는 Azure 리소스 관리자를 사용하여 만든 가상 네트워크에는 지원되지 않습니다. Azure 리소스 관리자를 사용하여 만든 가상 네트워크에서 Azure AD 도메인 서비스를 사용하려면 [클래식 가상 네트워크를 ARM 기반 가상 네트워크에 연결](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)하면 됩니다.


## 2단계: Azure 가상 네트워크 만들기
다음 구성 단계는 Azure AD 도메인 서비스를 사용하도록 설정할 Azure 가상 네트워크를 만드는 것입니다. 사용하고 싶은 기존 가상 네트워크가 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

> [AZURE.NOTE] 만들거나 Azure AD 도메인 서비스와 함께 사용하도록 선택하는 Azure 가상 네트워크가 Azure AD 도메인 서비스에서 지원하는 Azure 지역에 속하는지 확인합니다. Azure AD 도메인 서비스를 사용할 수 있는 Azure 지역을 알아보려면 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services/) 페이지를 참조하세요.

후속 구성 단계에서 Azure AD 도메인 서비스를 사용하도록 설정할 때 올바른 가상 네트워크를 선택할 수 있도록 가상 네트워크의 이름을 기록해 두어야 합니다.

Azure AD 도메인 서비스를 사용하도록 설정할 Azure 가상 네트워크를 만들기 위해 다음 구성 단계를 수행합니다.

1. **Azure 관리 포털**([https://manage.windowsazure.com](https://manage.windowsazure.com))로 이동합니다.
2. 왼쪽 창에서 **네트워크** 노드를 선택합니다.
3. 페이지 아래쪽의 작업창에서 **새로 만들기**를 클릭합니다.

    ![가상 네트워크 노드](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. **네트워크 서비스** 노드에서 **가상 네트워크**를 선택합니다.
5. 가상 네트워크를 만들기 위해 **빠른 생성**을 클릭합니다.

    ![가상 네트워크 - 빠른 생성](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. 가상 네트워크의 **이름**을 지정합니다. 이 네트워크의 **주소 공간** 또는 **최대 VM 수**를 구성하도록 선택할 수도 있습니다. 지금은 DNS 서버 설정을 ‘None’으로 설정된 상태로 둘 수 있습니다. 이 설정은 Azure AD 도메인 서비스를 사용하도록 설정한 후 업데이트됩니다.
7. **위치** 드롭다운에서 지원되는 Azure 지역을 선택해야 합니다. Azure AD 도메인 서비스를 사용할 수 있는 Azure 지역을 알아보려면 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services/) 페이지를 참조하세요. 이 단계는 중요합니다. Azure AD 도메인 서비스에서 지원하지 않는 Azure 지역에서 가상 네트워크를 선택하는 경우 해당 가상 네트워크에서 서비스를 사용하도록 설정할 수 없습니다.
8. **가상 네트워크 만들기** 단추를 클릭하여 가상 네트워크를 만듭니다.

    ![Azure AD 도메인 서비스에 대한 가상 네트워크를 만듭니다.](./media/active-directory-domain-services-getting-started/create-vnet.png)

---
[**다음 단계 - Azure AD 도메인 서비스를 사용하도록 설정합니다.**](active-directory-ds-getting-started-enableaadds.md)

<!---HONumber=AcomDC_0211_2016-->