<properties 
	pageTitle="Azure API 관리 FAQ | Microsoft Azure" 
	description="Azure API 관리에 대한 일반적인 질문과 대답, 패턴 및 모범 사례를 알아봅니다." 
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
	ms.date="08/09/2016" 
	ms.author="sdanie"/>

# Azure API 관리 FAQ

Azure API 관리에 대한 일반적인 질문과 대답, 패턴 및 모범 사례를 알아봅니다.

## 질문과 대답

-	[API 관리 팀에 질문하려면 어떻게 해야 합니까?](#how-can-i-ask-a-question-to-the-api-management-team)
-	[기능이 미리 보기인 경우 어떤 의미입니까?](#what-does-it-mean-if-a-feature-is-in-preview)
-	[API 관리 게이트웨이와 백 엔드 서비스 간의 연결을 보호하는 데 지원되는 옵션은 무엇입니까?](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
-	[API 관리 인스턴스를 새 인스턴스에 복사하려면 어떻게 해야 합니까?](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
-	[API 관리 인스턴스를 프로그래밍 방식으로 관리할 수 있습니까?](#can-i-manage-my-api-management-instance-programmatically)
-	[관리자 그룹에 사용자를 추가하려면 어떻게 해야 합니까?](#how-can-i-add-a-user-to-the-administrators-group)
-	[추가하려는 정책이 정책 편집기에서 활성화되지 않는 이유는 무엇입니까?](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)
-	[API 관리를 통해 API 버전 관리를 수행하는 방법은 무엇입니까?](#how-can-i-achieve-api-versioning-with-api-management)
-	[여러 API 환경(예: 샌드박스 및 프로덕션)을 구성하는 방법은 무엇입니까?](#how-can-i-configure-multiple-environments-of-apis-for-example-sandbox-and-production)
-	[API 관리에서 SOAP가 지원됩니까?](#is-soap-supported-in-api-management)
-	[API 관리 게이트웨이 IP 주소가 상수입니까? 그것을 방화벽 규칙에 사용할 수 있습니까?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-	[ADFS 보안을 통해 OAUth 2.0 권한 부여 서버를 구성할 수 있습니까?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-	[여러 곳의 지리적 위치에 배포할 때 API 관리에서 사용하는 라우팅 방법은 무엇입니까?](#what-routing-method-does-api-management-use-when-deployed-to-multiple-geographic-locations)
-	[ARM 템플릿을 사용하여 API 관리 서비스 인스턴스를 만들 수 있나요?](#can-i-create-an-api-management-service-instance-using-an-arm-template)
-	[백 엔드에 대해 자체 서명된 SSL 인증서를 사용할 수 있나요?](#can-i-use-a-self-signed-ssl-certificate-for-a-backend)
-	[GIT 리포지토리를 복제하려고 할 때 인증 실패가 발생하는 이유는 무엇인가요?](#why-am-i-getting-authentication-failure-when-i-try-to-clone-the-git-repository)
-	[API 관리는 Express 경로와 함께 작동하나요?](#does-api-management-work-with-express-route)


### API 관리 팀에 질문하려면 어떻게 해야 합니까?

-	[API 관리 MSDN 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)에 질문을 게시하면 됩니다.
-	`apimgmt@microsoft.com`으로 전자 메일을 보낼 수 있습니다.
-	[기능 요청](https://feedback.azure.com/forums/248703-api-management)을 보낼 수 있습니다.

### 기능이 미리 보기인 경우 어떤 의미입니까?

미리 보기 기능은 기능적으로 완전하지만 이 기능에 대한 의견을 적극적으로 기다리고 있으므로 미리 보기로 제공됩니다. 고객 피드백에 대한 응답으로 새로운 변경 사항을 만들었을 수 있으므로 프로덕션 환경에서 사용할 기능에 따르지 않는 것이 좋습니다. 미리 보기 상태의 기능에 대한 의견이 있는 경우 [API 관리 팀에 질문하려면 어떻게 해야 합니까?](#how-can-i-ask-a-question-to-the-api-management-team)에 설명된 메커니즘 중 하나를 사용하여 알려 주시기 바랍니다.

### API 관리 게이트웨이와 백 엔드 서비스 간의 연결을 보호하는 데 지원되는 옵션은 무엇입니까?

지원되는 여러 가지 옵션이 있습니다.

1. HTTP 기본 인증을 사용할 수 있습니다. 자세한 내용은 [API 설정 구성](api-management-howto-create-apis.md#configure-api-settings)을 참조하세요.
2. [Azure API 관리에서 클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](api-management-howto-mutual-certificates.md)에 설명된 대로 SSL 상호 인증을 사용할 수 있습니다.
3. 백 엔드 서비스에서 IP 허용 목록을 사용할 수 있습니다. 표준 또는 프리미엄 계층 API 관리 인스턴스를 사용하는 경우 게이트웨이의 IP 주소가 일관성 있게 유지되므로 이 IP 주소를 허용하도록 허용 목록을 구성할 수 있습니다. Azure 클래식 포털의 **대시보드**에서 API 관리 인스턴스의 IP 주소를 검색할 수 있습니다.
4. API 관리 인스턴스를 Azure 가상 네트워크(클래식)에 연결할 수 있습니다. 자세한 내용은 [Azure API 관리에서 VPN 연결을 설정하는 방법](api-management-howto-setup-vpn.md)을 참조하세요.

### API 관리 인스턴스를 새 인스턴스에 복사하려면 어떻게 해야 합니까?

API 관리 서비스 인스턴스를 새 인스턴스에 복사하는 데 사용할 수 있는 여러 가지 옵션이 있습니다.

-	API 관리의 백업 및 복원 기능을 사용할 수 있습니다. 자세한 내용은 [Azure API 관리에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](api-management-howto-disaster-recovery-backup-restore.md)을 참조하세요.
-	[API 관리 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx)를 사용하여 사용자 고유의 백업 및 복원 기능을 만들어 서비스 인스턴스에서 원하는 엔터티를 저장하고 복원할 수 있습니다.
-	Git를 사용하여 서비스 구성을 다운로드하고 새 인스턴스에 다시 업로드할 수 있습니다. 자세한 내용은 [Git를 사용하여 API 관리 서비스 구성을 저장 및 구성하는 방법](api-management-configuration-repository-git.md)을 참조하세요.

### API 관리 인스턴스를 프로그래밍 방식으로 관리할 수 있습니까?

예. [API 관리 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx), [Microsoft Azure API 관리 서비스 관리 라이브러리 SDK](http://aka.ms/apimsdk), [서비스 배포](https://msdn.microsoft.com/library/mt619282.aspx) 및 [서비스 관리](https://msdn.microsoft.com/library/mt613507.aspx) PowerShell cmdlet을 사용하여 관리할 수 있습니다.

### 관리자 그룹에 사용자를 추가하려면 어떻게 해야 합니까?

아래 단계를 통해 수행할 수 있습니다.

1. 새 [Azure 포털](https://portal.azure.com)에 로그인
2. 원하는 API 관리 인스턴스를 포함하는 리소스 그룹으로 이동
3. "API 관리 참여자" 역할에 원하는 사용자를 추가

작업이 완료되면 새로 추가된 참여자는 Azure PowerShell [cmdlet](https://msdn.microsoft.com/library/mt613507.aspx)을 사용하여 관리자 권한으로 로그인합니다.

1. `Login-AzureRmAccount` cmdlet을 사용하여 로그인합니다.
2. `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`을 사용하여 서비스를 포함하는 구독에 컨텍스트를 설정합니다.
3. `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`를 사용하여 SSO 토큰을 가져옵니다.
4. 브라우저에서 URL을 복사하고 붙여 넣으려면 사용자는 관리 포털에 액세스해야 합니다.


### 추가하려는 정책이 정책 편집기에서 활성화되지 않는 이유는 무엇입니까?

추가하려는 정책이 활성화되지 않는 경우 해당 정책의 올바른 범위에 속하는지 확인하세요. 각 정책 문은 특정 범위 및 정책 섹션에서 사용하도록 되어 있습니다. 정책의 정책 섹션 및 범위를 검토하려면 [정책 참조](https://msdn.microsoft.com/library/azure/dn894080.aspx)에서 해당 정책에 대한 **사용** 섹션을 확인하세요.


### API 관리를 통해 API 버전 관리를 수행하는 방법은 무엇입니까?

-	API 관리에 다른 버전을 나타내는 별개의 API를 구성할 수 있습니다. 예를 들어, `MyAPI v1` 및 `MyAPI v2`를 두 개의 다른 API로 구성할 수 있고, 개발자는 사용할 버전을 선택할 수 있습니다.
-	버전 세그먼트를 포함하지 않는 서비스 URL로(예: `https://my.api`) API를 구성할 수도 있습니다. 그 다음 각 작업의 [URL 다시 쓰기](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) 템플릿에서 버전 세그먼트를 구성할 수 있습니다. 예를 들면, `/resource`의 [URL 템플릿](api-management-howto-add-operations.md#url-template) 및 `/v1/Resource`의 [URL 다시 쓰기](api-management-howto-add-operations.md#rewrite-url-template) 템플릿으로 작업을 수행할 수 있습니다. 이렇게 하면 각 작업에서 개별적으로 버전 세그먼트 값을 변경할 수 있습니다.
-	API 서비스 URL의 “기본” 버전 세그먼트를 유지하려면, 선택한 작업에서 백 엔드 요청 경로를 변경하기 위해 [백 엔드 서비스 설정](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) 정책을 사용하는 정책을 설정할 수 있습니다.

### 여러 API 환경(예: 샌드박스 및 프로덕션)을 구성하는 방법은 무엇입니까?

현재 옵션은 다음과 같습니다.

-	동일한 테넌트에 별개의 API를 호스팅할 수 있습니다.
-	다른 테넌트에 동일한 API를 호스팅할 수 있습니다.

### API 관리에서 SOAP가 지원됩니까?

현재는, Azure API 관리 내 SOAP에 대해 제한된 지원을 제공합니다. 이 기능은 현재 조사 중 입니다. 여러분 고객의 WSDL 사례와, 그들에게 필요한 기능에 대한 설명을 기다리고 있습니다. 이런 내용은 개발팀의 생각을 구체화하는 데 도움이 됩니다. [API 관리 팀에 질문하려면 어떻게 해야 합니까?](#how-can-i-ask-a-question-to-the-api-management-team)에 언급된 연락처 정보를 사용하여 문의해 주십시오.

작동을 해야 하는 경우에는, 일부 커뮤니티에 대안이 제시되어 있습니다. [Azure API 관리 - APIM, HTTP를 통한 SOAP WCF 사용](http://mostlydotnetdev.blogspot.com/2015/03/azure-api-management-apim-consuming.html)을 참조하세요.

이런 식으로 솔루션을 구현하려면 정책을 수동으로 구성해야 하며, WSDL 가져오기/내보내기가 지원되지 않고, 사용자는 개발자 포털에서 테스트 콘솔을 사용하여 요청 본문을 형성해야 합니다.

### API 관리 게이트웨이 IP 주소가 상수입니까? 그것을 방화벽 규칙에 사용할 수 있습니까?

표준 계층 및 프리미엄 계층에서 API 관리 테넌트의 공용 IP 주소(VIP)는 아래 나열된 몇 가지 사항을 제외하고, 테넌트의 수명 동안 정적입니다. 다중 지역 배포를 위해 구성된 프리미엄 계층은 지역 당 하나의 공용 IP 주소에 할당됩니다.

IP 주소는 다음 상황에서 변경됩니다.

-	서비스가 삭제되고 다시 생성되었습니다.
-	서비스 구독이 일시 중단되었다가(예: 미지불) 복원되었습니다.
-	VNET이 추가되거나 제거되었습니다.(VNET은 프리미엄 계층에서만 지원됩니다.)
-	지역 주소는 지역이 비워졌다가 다시 입력된 경우에 변경됩니다.(다중 지역 배포는 프리미엄 계층에서만 지원됩니다.)

IP 주소(또는 다중 지역 배포의 경우 여러 IP 주소)는 Azure 클래식 포털의 테넌트 페이지에서 찾을 수 있습니다.

### ADFS 보안을 통해 OAUth 2.0 권한 부여 서버를 구성할 수 있습니까?

이 시나리오 구성에 대한 정보는 [API 관리에서 ADFS 사용](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)을 참조하세요.

### 여러 곳의 지리적 위치에 배포할 때 API 관리에서 사용하는 라우팅 방법은 무엇입니까? 

API 관리는 [성능 트래픽 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method)을 사용합니다. 들어오는 트래픽은 가장 가까운 API 게이트웨이로 라우팅됩니다. 한 지역이 오프라인 상태가 되면, 들어오는 트래픽은 다음으로 가까운 게이트웨이로 자동으로 라우팅됩니다. 라우팅 방법에 대한 자세한 내용은 [트래픽 관리자 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)을 참조하세요.

### ARM 템플릿을 사용하여 API 관리 서비스 인스턴스를 만들 수 있나요?

예, [Azure API 관리 서비스](http://aka.ms/apimtemplate) 빠른 시작 템플릿을 참조하세요.

### 백 엔드에 대해 자체 서명된 SSL 인증서를 사용할 수 있나요?

예. 아래 단계를 따르세요.

1. 관리 API를 사용하여 [백 엔드](https://msdn.microsoft.com/library/azure/dn935030.aspx) 엔터티 만들기
2. skipCertificateChainValidation 속성을 true로 설정
3. 자체 서명된 인증서를 더 이상 허용하지 않으려면 백 엔드 엔터티를 삭제하거나 skipCertificateChainValidation 속성을 false로 설정할 수 있습니다.

### GIT 리포지토리를 복제하려고 할 때 인증 실패가 발생하는 이유는 무엇인가요? 

GIT 자격 증명 관리자를 사용하거나 Visual Studio를 통해 리포지토리를 복제하려고 하면 암호 길이를 127자로만 제한하여 생성된 암호가 잘리는 Windows 자격 증명 대화 상자의 알려진 문제가 발생한 것일 수 있습니다. 암호 길이를 줄이는 작업 중입니다. 현재는 복제하는 데 GIT Bash를 사용하세요.

### API 관리는 Express 경로와 함께 작동하나요?

예!

<!---HONumber=AcomDC_0824_2016-->