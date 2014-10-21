<properties pageTitle="How to secure back-end services using mutual certificate authentication in Azure API Management" metaKeywords="" description="Learn how to secure back-end services using mutual certificate authentication in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to secure back-end services using mutual certificate authentication in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Azure API 관리에서 상호 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법

API 관리에서는 상호 인증서를 사용하여 API의 백 엔드 서비스에 대한 액세스를 보호하는 기능을 제공합니다. 이 가이드에서는 API 관리 콘솔에서 인증서를 관리하는 방법과 인증서를 사용하여 백 엔드 서비스에 액세스하도록 API를 구성하는 방법을 설명합니다.

> API 관리 REST API를 사용하여 인증서를 관리하는 방법에 대한 자세한 내용은 [Azure API 관리 REST API 인증서 엔터티][Azure API 관리 REST API 인증서 엔터티]를 참조하세요.

## 항목 내용

-   [필수 조건][필수 조건]
-   [클라이언트 인증서 업로드][클라이언트 인증서 업로드]
-   [클라이언트 인증서 삭제][클라이언트 인증서 삭제]
-   [프록시 인증에 상호 인증서를 사용하도록 API 구성][프록시 인증에 상호 인증서를 사용하도록 API 구성]

## <a name="prerequisites"> </a>필수 조건

이 가이드에서는 상호 인증서 인증을 사용하여 API의 백 엔드 서비스에 액세스하도록 API 관리 서비스 인스턴스를 구성하는 방법을 설명합니다. 이 항목의 단계를 수행하기 전에 상호 인증서 인증을 사용하도록 백 엔드 서비스를 구성해야 하며 API 관리 콘솔에서 업로드할 인증서 및 해당 인증서의 암호에 액세스할 수 있어야 합니다.

## <a name="step1"> </a>클라이언트 인증서 업로드

시작하려면 Azure 포털에서 API 관리 서비스에 대한 **관리 콘솔**을 클릭합니다. API 관리의 관리용 포털이 열립니다.

![API 관리 콘솔][API 관리 콘솔]

> 아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][Azure API 관리 시작] 자습서의 [API 관리 서비스 인스턴스 만들기][API 관리 서비스 인스턴스 만들기]를 참조하세요.

왼쪽의 **API 관리** 메뉴에서 **보안**을 클릭하고 **클라이언트 인증서**를 클릭합니다.

![클라이언트 인증서][클라이언트 인증서]

새 인증서를 업로드하려면 **인증서 업로드**를 클릭합니다.

![인증서 업로드][인증서 업로드]

인증서를 찾은 다음 인증서의 암호를 입력합니다.

> 인증서는 **.pfx** 형식이어야 합니다. 자체 서명된 인증서도 사용할 수 있습니다.

![인증서 업로드][1]

**업로드**를 클릭하여 인증서를 업로드합니다.

> 이때 인증서 암호의 유효성을 검사하여 암호가 잘못된 경우 오류 메시지가 표시됩니다.

![업로드된 인증서][업로드된 인증서]

업로드된 인증서는 **클라이언트 인증서** 탭에 표시됩니다. 인증서가 여러 개인 경우 제목이나 지문의 마지막 4자를 적어 둡니다. 이러한 항목은 인증서를 사용하도록 API를 구성할 때 인증서를 선택하는 데 사용됩니다. 여기에 대해서는 다음 섹션인 [프록시 인증에 상호 인증서를 사용하도록 API 구성][프록시 인증에 상호 인증서를 사용하도록 API 구성]에서 설명합니다.

## <a name="step1a"> </a>클라이언트 인증서 삭제

인증서를 삭제하려면 원하는 인증서 옆에 있는 **삭제**를 클릭합니다.

![인증서 삭제][인증서 삭제]

**예, 삭제합니다.**를 클릭하여 삭제를 확인합니다.

![삭제 확인][삭제 확인]

인증서를 API에서 사용하고 있는 경우 경고 화면이 표시됩니다. 이러한 인증서를 삭제하려면 먼저 해당 인증서를 사용하도록 구성된 API에서 인증서를 제거해야 합니다.

![삭제 확인][2]

## <a name="step2"> </a>프록시 인증에 상호 인증서를 사용하도록 API 구성

왼쪽의 **API 관리** 메뉴에서 **API**를 클릭하고 원하는 API의 이름을 클릭한 후에 **보안** 탭을 클릭합니다.

![API 보안][API 보안]

**자격 증명 사용** 드롭다운 목록에서 **상호 인증서**를 선택합니다.

![상호 인증서][상호 인증서]

**클라이언트 인증서** 드롭다운 목록에서 원하는 인증서를 선택합니다. 인증서가 여러 개이면 이전 섹션에서 적어 둔 제목이나 지문의 마지막 4자를 통해 올바른 인증서를 확인할 수 있습니다.

![인증서 선택][인증서 선택]

**저장**을 클릭하여 API에 대한 구성 변경 내용을 저장합니다.

> 이 변경 내용은 즉시 적용되며 해당 API의 작업 호출은 인증서를 사용하여 백 엔드 서버에서 인증됩니다.

![API 변경 내용 저장][API 변경 내용 저장]

> API의 백 엔드 서비스에 대해 프록시 인증용으로 지정된 인증서는 해당 API의 정책에 포함되며 정책 편집기에서 볼 수 있습니다.

![인증서 정책][인증서 정책]

  [Azure API 관리 REST API 인증서 엔터티]: http://msdn.microsoft.com/library/azure/dn783483.aspx
  [필수 조건]: #prerequisites
  [클라이언트 인증서 업로드]: #step1
  [클라이언트 인증서 삭제]: #step1a
  [프록시 인증에 상호 인증서를 사용하도록 API 구성]: #step2
  [API 관리 콘솔]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
  [Azure API 관리 시작]: ../api-management-get-started
  [API 관리 서비스 인스턴스 만들기]: ../api-management-get-started/#create-service-instance
  [클라이언트 인증서]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
  [인증서 업로드]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
  [1]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
  [업로드된 인증서]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
  [인증서 삭제]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
  [삭제 확인]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
  [2]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png
  [API 보안]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
  [상호 인증서]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
  [인증서 선택]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
  [API 변경 내용 저장]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
  [인증서 정책]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
