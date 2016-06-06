<properties 
	pageTitle="Azure API 관리에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법" 
	description="Azure API 관리에서 그룹을 사용하여 개발자 계정을 관리하는 방법에 대해 알아봅니다." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/25/2016" 
	ms.author="sdanie"/>

# Azure API 관리에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법

API 관리에서 그룹은 개발자에 대한 제품 표시 여부를 관리하는 데 사용됩니다. 제품이 먼저 그룹에 표시된 다음, 이러한 그룹의 개발자가 그룹과 연결된 제품을 보고 구독할 수 있습니다.

API 관리에는 다음과 같은 변경할 수 없는 시스템 그룹이 있습니다.

-	**관리자** - Azure 구독 관리자가 이 그룹의 구성원입니다. 관리자는 API 관리 서비스 인스턴스를 관리하며 개발자가 사용하는 API, 작업 및 제품을 만듭니다.
-	**개발자** - 인증된 개발자 포털 사용자가 이 그룹에 속합니다. 개발자는 API를 사용하여 응용 프로그램을 빌드하는 고객입니다. 개발자는 개발자 포털에 액세스할 수 있는 권한을 받으며 API의 작업을 호출하는 응용 프로그램을 빌드합니다.
-	**게스트** - API 관리 인스턴스의 개발자 포털을 방문하는 인증되지 않은 개발자 포털 사용자(예: 잠재 고객)가 이 그룹에 속합니다. 예를 들어 API를 볼 수 있지만 호출할 수는 없는 기능과 같이 특정 읽기 전용 액세스 권한을 받을 수 있습니다.

이러한 시스템 그룹 외에도 관리자는 사용자 지정 그룹을 만들거나 [연관된 Azure Active Directory 테넌트에서 외부 그룹을 가져올 수 있습니다.][] 사용자 지정 및 외부 그룹은 시스템 그룹과 함께 사용되어 개발자에게 API 제품에 대한 표시 여부 및 액세스를 제공합니다. 예를 들어, 특정 파트너 조직과 관련된 개발자를 위한 하나의 사용자 지정 그룹을 만들고 관련 API만을 포함한 제품에서 API에 대한 액세스를 허용합니다. 사용자는 두 그룹 이상의 구성원이 될 수 있습니다.

이 가이드에서는 API 관리 인스턴스의 관리자가 새 그룹을 추가하고 이 그룹과 새 제품 및 개발자를 연결하는 방법을 보여 줍니다.

>[AZURE.NOTE] 게시자 포털에서 그룹 만들기 및 관리 외에도 API 관리 REST API [그룹](https://msdn.microsoft.com/library/azure/dn776329.aspx) 엔터티를 사용하여 그룹을 만들고 관리할 수 있습니다.

## <a name="create-group"> </a>그룹 만들기

새 그룹을 만들려면 API 관리 서비스에 대해 Azure 클래식 포털에서 **관리**를 클릭합니다. API 관리 게시자 포털로 이동됩니다.

![게시자 포털][api-management-management-console]

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][] 자습서의 [API 관리 서비스 인스턴스 만들기][]를 참조하세요.

왼쪽의 **API 관리** 메뉴에서 **그룹**을 클릭한 다음 **그룹 추가**를 클릭합니다.

![새 그룹 추가][api-management-add-group]

그룹의 고유한 이름 및 선택적 설명을 입력하고 **저장**을 클릭합니다.

![새 그룹 추가][api-management-add-group-window]

새 그룹이 그룹 탭에 표시됩니다. 그룹의 **이름** 또는 **설명**을 편집하려면 목록의 그룹 이름을 클릭합니다. 그룹을 삭제하려면 **삭제**를 클릭합니다.

![그룹 추가됨][api-management-new-group]

그룹이 생성되었으므로, 제품 및 개발자와 연결할 수 있습니다.

## <a name="associate-group-product"> </a>그룹과 제품 연결

그룹과 제품을 연결하려면 왼쪽의 **API 관리** 메뉴에서 **제품**을 클릭한 다음, 원하는 제품의 이름을 클릭합니다.

![표시 여부 설정][api-management-add-group-to-product]

**표시 여부** 탭을 선택하여 그룹을 추가 및 제거하고, 제품에 대한 현재 그룹을 봅니다. 그룹을 추가 또는 제거하려면 원하는 그룹의 확인란을 선택하거나 선택 취소하고 **저장**을 클릭하세요.

![표시 여부 설정][api-management-add-group-to-product-visibility]

>[AZURE.NOTE] Azure Active Directory 그룹을 추가하려면 [Azure API 관리에서 Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법](api-management-howto-aad.md)을 참조하세요.
>
>제품에 대한 **표시 여부** 탭에서 그룹을 구성하려면 **그룹 관리**를 클릭합니다.

제품이 그룹과 연결되면 그룹의 개발자가 제품을 보고 구독할 수 있습니다.

## <a name="associate-group-developer"> </a>그룹과 개발자 연결

그룹과 개발자를 연결하려면 왼쪽의 **API 관리** 메뉴에서 **사용자**를 클릭한 다음 그룹과 연결할 개발자 옆의 확인란을 선택합니다.

![그룹에 개발자 추가][api-management-add-group-to-developer]

원하는 개발자를 선택한 후 **그룹에 추가** 드롭다운에서 원하는 그룹을 클릭합니다. **그룹에서 제거** 드롭다운을 사용하여 그룹에서 개발자를 제거할 수 있습니다.

![개발자][api-management-add-group-to-developer-saved]

개발자와 그룹 간의 연결을 추가한 후에는 **사용자** 탭에서 확인할 수 있습니다.


## <a name="next-steps"> </a>다음 단계

-	그룹에 개발자를 추가하면 개발자가 해당 그룹과 연결된 제품을 보고 구독할 수 있습니다. 자세한 내용은 [Azure API 관리에서 제품을 만들고 게시하는 방법][]을 참조하세요.
-	게시자 포털에서 그룹 만들기 및 관리 외에도 API 관리 REST API [그룹](https://msdn.microsoft.com/library/azure/dn776329.aspx) 엔터티를 사용하여 그룹을 만들고 관리할 수 있습니다.


[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[Azure API 관리에서 제품을 만들고 게시하는 방법]: api-management-howto-add-products.md

[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md
[Azure API 관리 시작]: api-management-get-started.md#create-service-instance
[연관된 Azure Active Directory 테넌트에서 외부 그룹을 가져올 수 있습니다.]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group

<!---HONumber=AcomDC_0525_2016-->