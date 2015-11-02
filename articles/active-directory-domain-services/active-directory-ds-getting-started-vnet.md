<properties
	pageTitle="Azure Active Directory 도메인 서비스 미리 보기: 시작 | Microsoft Azure"
	description="Azure Active Directory 도메인 서비스 시작"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스*(미리 보기)* - 시작

## Azure 가상 네트워크 선택 지침
Azure AD 도메인 서비스와 함께 사용할 가상 네트워크를 선택할 때 다음 지침을 명심해야 합니다.

- Azure AD 도메인 서비스에서 지원하는 지역에서 가상 네트워크를 선택해야 합니다. 지원되는 Azure 지역의 현재 목록은 [지역](active-directory-ds-regions.md) 페이지에 나와 있습니다.
- 기존 가상 네트워크를 사용할 계획인 경우 해당 네트워크가 지역 가상 네트워크인지 확인합니다. 레거시 선호도 그룹 메커니즘을 사용하는 가상 네트워크는 Azure AD 도메인 서비스와 함께 사용할 수 없습니다. [레거시 가상 네트워크를 지역 가상 네트워크로 마이그레이션](../virtual-networks-migrate-to-regional-vnet.md)해야 합니다.
- Azure AD 도메인 서비스에 액세스해야 하는 가상 컴퓨터를 현재 호스트하거나 호스트할 가상 네트워크를 선택합니다. 나중에 다른 가상 네트워크로 도메인 서비스를 이동할 수 없습니다.


## 2단계: Azure 가상 네트워크 만들기
다음 구성 단계는 Azure AD 도메인 서비스를 사용하도록 설정할 Azure 가상 네트워크를 만드는 것입니다. 사용하고 싶은 기존 가상 네트워크가 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

> [AZURE.NOTE]만들거나 Azure AD 도메인 서비스와 함께 사용하도록 선택하는 Azure 가상 네트워크가 Azure AD 도메인 서비스에서 지원하는 Azure 지역에 속하는지 확인합니다. Azure AD 도메인 서비스를 사용할 수 있는 Azure 지역의 목록은 [지역](active-directory-ds-regions.md) 페이지를 참조하세요.

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
7. **위치** 드롭다운에서 지원되는 Azure 지역을 선택해야 합니다. Azure AD 도메인 서비스를 사용할 수 있는 Azure 지역의 목록은 [지역](active-directory-ds-regions.md) 페이지를 참조하세요. 이 단계는 중요합니다. Azure AD 도메인 서비스에서 지원하지 않는 Azure 지역에서 가상 네트워크를 선택하는 경우 해당 가상 네트워크에서 서비스를 사용하도록 설정할 수 없습니다.
8. **가상 네트워크 만들기** 단추를 클릭하여 가상 네트워크를 만듭니다.

    ![Azure AD 도메인 서비스에 대한 가상 네트워크를 만듭니다.](./media/active-directory-domain-services-getting-started/create-vnet.png)

---
[**다음 단계 - Azure AD 도메인 서비스를 사용하도록 설정합니다.**](active-directory-ds-getting-started-enableaadds.md)

<!---HONumber=Oct15_HO4-->