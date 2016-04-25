<properties 
	pageTitle="Azure API 관리 FAQ | Microsoft Azure" 
	description="Azure API 관리에 대한 일반적인 질문과 대답, 패턴 및 모범 사례를 알아봅니다." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/12/2016" 
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

### API 관리 팀에 질문하려면 어떻게 해야 합니까?

-	[API 관리 MSDN 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)에 질문을 게시하면 됩니다.
-	`apimgmt@microsoft.com`으로 전자 메일을 보낼 수도 있습니다.

### 기능이 미리 보기인 경우 어떤 의미입니까?

미리 보기 기능은 기능적으로 완전하지만 이 기능에 대한 의견을 적극적으로 기다리고 있으므로 미리 보기로 제공됩니다. 고객 피드백에 대한 응답으로 새로운 변경 사항을 만들었을 수 있으므로 프로덕션 환경에서 사용할 기능에 따르지 않는 것이 좋습니다. 미리 보기 기능에 대한 의견이 있는 경우 [API 관리 팀에 질문하려면 어떻게 해야 합니까?](#how-can-i-ask-a-question-to-the-api-management-team)에 설명된 메커니즘 중 하나를 사용하여 알려 주시기 바랍니다.

### API 관리 게이트웨이와 백 엔드 서비스 간의 연결을 보호하는 데 지원되는 옵션은 무엇입니까?

지원되는 여러 가지 옵션이 있습니다.

1. HTTP 기본 인증을 사용할 수 있습니다. 자세한 내용은 [API 설정 구성](api-management-howto-create-apis.md#configure-api-settings)을 참조하세요.
2. [Azure API 관리에서 클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](api-management-howto-mutual-certificates.md)에 설명된 대로 SSL 수동 인증을 사용할 수 있습니다.
3. 백 엔드 서비스에서 IP 허용 목록을 사용할 수 있습니다. 표준 또는 프리미엄 계층 API 관리 인스턴스를 사용하는 경우 게이트웨이의 IP 주소가 일관성 있게 유지되므로 이 IP 주소를 허용하도록 허용 목록을 구성할 수 있습니다. Azure 클래식 포털의 **대시보드**에서 API 관리 인스턴스의 IP 주소를 검색할 수 있습니다.
4. API 관리 인스턴스를 Azure 가상 네트워크(클래식)에 연결할 수 있습니다. 자세한 내용은 [Azure API 관리에서 VPN 연결을 설정하는 방법](api-management-howto-setup-vpn.md)을 참조하세요.

### API 관리 인스턴스를 새 인스턴스에 복사하려면 어떻게 해야 합니까?

API 관리 서비스 인스턴스를 새 인스턴스에 복사하는 데 사용할 수 있는 여러 가지 옵션이 있습니다.

-	API 관리의 백업 및 복원 기능을 사용할 수 있습니다. 자세한 내용은 [Azure API 관리에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](api-management-howto-disaster-recovery-backup-restore.md)을 참조하세요.
-	[API 관리 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx)를 사용하여 사용자 고유의 백업 및 복원 기능을 만들어 서비스 인스턴스에서 원하는 엔터티를 저장하고 복원할 수 있습니다.
-	Git를 사용하여 서비스 구성을 다운로드하고 새 인스턴스에 다시 업로드할 수 있습니다. 자세한 내용은 [Git를 사용하여 API 관리 서비스 구성을 저장 및 구성하는 방법](api-management-configuration-repository-git.md)을 참조하세요.

### API 관리 인스턴스를 프로그래밍 방식으로 관리할 수 있습니까?

예. [API 관리 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx)와 [서비스 배포](https://msdn.microsoft.com/library/mt619282.aspx) 및 [서비스 관리](https://msdn.microsoft.com/library/mt613507.aspx) PowerShell cmdlet을 사용하여 관리할 수 있습니다.

### 관리자 그룹에 사용자를 추가하려면 어떻게 해야 합니까?

지금은 관리자가 Azure 클래식 포털을 통해 관리자 권한으로 로그인한 사용자 또는 API 관리 인스턴스를 포함하는 Azure 구독의 공동 관리자로 제한됩니다. 게시자 포털에서 만든 사용자를 관리자로 지정하거나 관리자 그룹에 추가할 수 없습니다.


### 추가하려는 정책이 정책 편집기에서 활성화되지 않는 이유는 무엇입니까?

추가하려는 정책이 활성화되지 않는 경우 해당 정책의 올바른 범위에 속하는지 확인하세요. 각 정책 문은 특정 범위 및 정책 섹션에서 사용하도록 되어 있습니다. 정책의 정책 섹션 및 범위를 검토하려면 [정책 참조](https://msdn.microsoft.com/library/azure/dn894080.aspx)에서 해당 정책에 대한 **사용** 섹션을 확인하세요.

<!---HONumber=AcomDC_0413_2016-->