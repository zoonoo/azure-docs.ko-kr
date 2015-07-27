<properties 
	pageTitle="Azure API 관리에서 상호 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법" 
	description="Azure API 관리에서 상호 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법에 대해 알아봅니다." 
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
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Azure API 관리에서 상호 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법

API 관리에서는 상호 인증서를 사용하여 API의 백 엔드 서비스에 대한 액세스를 보호하는 기능을 제공합니다. 이 가이드에서는 API 게시자 포털에서 인증서를 관리하는 방법과 인증서를 사용하여 백 엔드 서비스에 액세스하도록 API를 구성하는 방법을 설명합니다.

API 관리 REST API를 사용하여 인증서를 관리하는 방법에 대한 자세한 내용은 [Azure API 관리 REST API 인증서 엔터티][]를 참조하세요.

## <a name="prerequisites"> </a>필수 조건

이 가이드에서는 상호 인증서 인증을 사용하여 API의 백 엔드 서비스에 액세스하도록 API 관리 서비스 인스턴스를 구성하는 방법을 설명합니다. 이 항목의 단계를 수행하기 전에 상호 인증서 인증을 사용하도록 백 엔드 서비스를 구성해야 하며 API 관리 게시자 포털에서 업로드할 인증서 및 해당 인증서의 암호에 액세스할 수 있어야 합니다.

## <a name="step1"> </a>클라이언트 인증서 업로드

시작하려면 API 관리 서비스에 대해 Azure 포털에서 **관리**를 클릭합니다. API 관리 게시자 포털로 이동됩니다.

![API 게시자 포털][api-management-management-console]

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][] 자습서의 [API 관리 서비스 인스턴스 만들기][]를 참조하세요.

왼쪽의 **API 관리** 메뉴에서 **보안**을 클릭하고 **클라이언트 인증서**를 클릭합니다.

![클라이언트 인증서][api-management-security-client-certificates]

새 인증서를 업로드하려면 **인증서 업로드**를 클릭합니다.

![인증서 업로드][api-management-upload-certificate]

인증서를 찾은 다음 인증서의 암호를 입력합니다.

>인증서는 **.pfx** 형식이어야 합니다. 자체 서명된 인증서도 사용할 수 있습니다.

![인증서 업로드][api-management-upload-certificate-form]

**업로드**를 클릭하여 인증서를 업로드합니다.

>이때 인증서 암호의 유효성을 검사하여 암호가 잘못된 경우 오류 메시지가 표시됩니다.

![업로드된 인증서][api-management-certificate-uploaded]

업로드된 인증서는 **클라이언트 인증서** 탭에 표시됩니다. 인증서가 여러 개인 경우 제목이나 지문의 마지막 4자를 적어 둡니다. 이러한 항목은 인증서를 사용하도록 API를 구성할 때 인증서를 선택하는 데 사용됩니다. 여기에 대해서는 다음 섹션인 [프록시 인증에 상호 인증서를 사용하도록 API 구성][]에서 설명합니다.

## <a name="step1a"> </a>클라이언트 인증서 삭제

인증서를 삭제하려면 원하는 인증서 옆에 있는 **삭제**를 클릭합니다.

![인증서 삭제][api-management-certificate-delete]

**예, 삭제합니다.**를 클릭하여 삭제를 확인합니다.

![삭제 확인][api-management-confirm-delete]

인증서를 API에서 사용하고 있는 경우 경고 화면이 표시됩니다. 이러한 인증서를 삭제하려면 먼저 해당 인증서를 사용하도록 구성된 API에서 인증서를 제거해야 합니다.

![삭제 확인][api-management-confirm-delete-policy]

## <a name="step2"> </a>프록시 인증에 상호 인증서를 사용하도록 API 구성

왼쪽의 **API 관리** 메뉴에서 **API**를 클릭하고 원하는 API의 이름을 클릭한 후에 **보안** 탭을 클릭합니다.

![API 보안][api-management-api-security]

**자격 증명 사용** 드롭다운 목록에서 **상호 인증서**를 선택합니다.

![상호 인증서][api-management-mutual-certificates]

**클라이언트 인증서** 드롭다운 목록에서 원하는 인증서를 선택합니다. 인증서가 여러 개이면 이전 섹션에서 적어 둔 제목이나 지문의 마지막 4자를 통해 올바른 인증서를 확인할 수 있습니다.

![인증서 선택][api-management-select-certificate]

**저장**을 클릭하여 API에 대한 구성 변경 내용을 저장합니다.

>이 변경 내용은 즉시 적용되며 해당 API의 작업 호출은 인증서를 사용하여 백 엔드 서버에서 인증됩니다.

![API 변경 내용 저장][api-management-save-api]

>API의 백 엔드 서비스에 대해 프록시 인증용으로 지정된 인증서는 해당 API의 정책에 포함되며 정책 편집기에서 볼 수 있습니다.

![인증서 정책][api-management-certificate-policy]

## 다음 단계

자세한 내용은 다음 비디오를 참조하세요.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Azure API 관리 시작]: api-management-get-started.md#create-service-instance

[Azure API 관리 REST API 인증서 엔터티]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[프록시 인증에 상호 인증서를 사용하도록 API 구성]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



 

<!---HONumber=July15_HO3-->