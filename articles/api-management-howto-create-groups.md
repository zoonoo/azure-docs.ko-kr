<properties 
	pageTitle="Azure API 관리에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법" 
	description="Azure API 관리에서 그룹을 사용하여 개발자 계정을 관리하는 방법에 대해 알아봅니다." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Azure API 관리에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법

API 관리(미리 보기)에서 그룹은 개발자에 대한 제품 표시 여부를 관리하는 데 사용됩니다. 제품이 먼저 그룹에 표시된 다음, 이러한 그룹의 개발자가 그룹과 연결된 제품을 보고 구독할 수 있습니다.

API 관리에는 다음과 같은 기본 제공 그룹이 있습니다.

- **관리자** - 관리자는 API 관리 서비스 인스턴스를 관리하며 개발자가 사용하는 API, 작업 및 제품을 만듭니다.
-   **개발자** - 개발자는 API를 사용하여 응용 프로그램을 구축하는 고객입니다. 개발자는 개발자 포털에 액세스할 수 있는 권한을 받으며 API의 작업을 호출하는 응용 프로그램을 빌드합니다.
- **게스트** - API 관리 인스턴스의 개발자 포털을 방문하는 인증되지 않은 사용자(예: 잠재 고객)가 이 그룹에 속합니다. 예를 들어 API를 볼 수 있지만 호출할 수는 없는 기능과 같이 특정 읽기 전용 액세스 권한을 받을 수 있습니다.

이러한 기본 제공 그룹 이외에, 관리자는 사용자 지정 그룹을 만들 수 있습니다. 사용자 지정 그룹은 기본 제공 개발자 그룹과 동일한 권한을 가지며 여러 개발자 그룹을 관리하는 데 사용할 수 있습니다. 예를 들어 한 제품의 API를 사용하는 사용자 지정 개발자 그룹을 만들고 다른 제품의 API를 사용하는 다른 개발자 그룹을 만들 수 있습니다.

이 가이드에서는 API 관리 인스턴스의 관리자가 새 그룹을 추가하고 이 그룹과 새 제품 및 개발자를 연결하는 방법을 보여 줍니다.

## 항목 내용

- [그룹 만들기][그룹 만들기]
- [그룹과 제품 연결][그룹과 제품 연결]
- [그룹과 개발자 연결][그룹과 개발자 연결]
- [다음 단계][다음 단계]

## <a name="create-group"></a> 그룹 만들기

새 그룹을 만들려면 Azure 포털에서 API 관리 서비스에 대한 **관리 콘솔**을 클릭합니다. API 관리의 관리용 포털이 열립니다.

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][Azure API 관리 시작] 자습서의 [API 관리 서비스 인스턴스 만들기][API 관리 서비스 인스턴스 만들기]를 참조하세요.

![API 관리 콘솔][API 관리 콘솔]

왼쪽의 **API 관리** 메뉴에서 **그룹**을 클릭한 다음 **그룹 추가**를 클릭합니다.

![새 그룹 추가][새 그룹 추가]

그룹의 고유한 이름 및 선택적 설명을 입력하고 **저장**을 클릭합니다.

![새 그룹 추가][1]

새 그룹이 그룹 탭에 표시됩니다. 그룹의 **이름** 또는 **설명**을 편집하려면 목록의 그룹 이름을 클릭합니다. 그룹을 삭제하려면 **삭제**를 클릭합니다.

![그룹 추가됨][그룹 추가됨]

그룹이 생성되었으므로, 제품 및 개발자와 연결할 수 있습니다.

## <a name="associate-group-product"></a> 그룹과 제품 연결

그룹과 제품을 연결하려면 왼쪽의 **API 관리** 메뉴에서 **제품**을 클릭한 다음, 원하는 제품의 이름을 클릭합니다.

![표시 여부 설정][표시 여부 설정]

**표시 여부** 탭을 선택하여 그룹을 추가 및 제거하고, 제품에 대한 현재 그룹을 봅니다. 그룹을 추가 또는 제거하려면 원하는 그룹의 확인란을 선택하거나 선택 취소하고 **저장**을 클릭하세요.

![표시 여부 설정][2]

>제품에 대한 **표시 여부** 탭에서 그룹을 구성하려면 **그룹 관리**를 클릭합니다.

제품이 그룹과 연결되면 그룹의 개발자가 제품을 보고 구독할 수 있습니다.

## <a name="associate-group-developer"></a> 그룹과 개발자 연결

그룹과 개발자를 연결하려면 왼쪽의 **API 관리** 메뉴에서 **개발자**를 클릭한 다음, 그룹과 연결할 개발자 옆에 있는 확인란을 선택합니다.

![그룹에 개발자 추가][그룹에 개발자 추가]

원하는 개발자를 선택한 후 **그룹에 추가** 드롭다운에서 원하는 그룹을 클릭합니다. **그룹에서 제거** 드롭다운을 사용하여 그룹에서 개발자를 제거할 수 있습니다.

![개발자][개발자]

개발자와 그룹 간의 연결을 추가한 후에는 **개발자** 탭에서 확인할 수 있습니다.

## <a name="next-steps"></a> 다음 단계

그룹에 개발자를 추가하면 개발자가 해당 그룹과 연결된 제품을 보고 구독할 수 있습니다. 자세한 내용은 [Azure API 관리에서 제품을 만들고 게시하는 방법][Azure API 관리에서 제품을 만들고 게시하는 방법]을 참조하세요.


[그룹 만들기]: #create-group
[그룹과 제품 연결]: #associate-group-product
[그룹과 개발자 연결]: #associate-group-developer
[다음 단계]: #next-steps
[Azure API 관리 시작]: ../api-management-get-started
[API 관리 서비스 인스턴스 만들기]: ../api-management-get-started/#create-service-instance
[API 관리 콘솔]: ./media/api-management-howto-create-groups/api-management-management-console.png
[새 그룹 추가]: ./media/api-management-howto-create-groups/api-management-add-group.png
[1]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[그룹 추가됨]: ./media/api-management-howto-create-groups/api-management-new-group.png
[표시 여부 설정]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[2]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[그룹에 개발자 추가]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[개발자]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png
[Azure API 관리에서 제품을 만들고 게시하는 방법]: ../api-management-howto-add-products

<!--HONumber=46--> 
