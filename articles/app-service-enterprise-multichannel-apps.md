<properties 
	pageTitle="엔터프라이즈 다중 채널 앱" 
	description="다중 채널 앱이 온-프레미스 리소스와 클라우드 기반 소프트웨어 서비스를 포괄하는 방식을 간략하게 설명합니다." 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="stefsch"/>

# 엔터프라이즈용 다중 채널 응용 프로그램 만들기

## 개요

엔터프라이즈 다중 채널 앱은 여러 기술을 사용하여 고객에게 데이터를 제공합니다.  엔터프라이즈 웹, 모바일 및 API 소비자는 모두 다양한 출처의 정보를 검색하고 처리합니다.  이러한 출처는 온-프레미스에서 실행되는 내부 시스템과 클라우드 기반
서비스를 포괄합니다.  

엔터프라이즈 응용 프로그램은 직원 및 비즈니스 파트너가 엔터프라이즈의 직접 제어하에 보안 ID를 사용하여 데이터에 연결하는 보안 액세스에 대한 요구 사항도 있습니다.

AAS(Azure 앱 서비스)를 실행하는 엔터프라이즈 응용 프로그램은 이러한 기능을 모두 제공할 수 있습니다.  

아래에 표시된 예제 직원 출장 응용 프로그램은 Office 365 및 Salesforce와 같은 SaaS(Software-as-a-Service) 서비스와 온-프레미스 리소스 모두와 통합되는 AAD(Azure Active Directory)를 통해 보안이 설정되는 엔터프라이즈 다중 채널 응용 프로그램을 
보여 줍니다.

## <a name="acceptablefiles"></a>Azure Active Directory를 통한 액세스 제어

출장 응용 프로그램의 사용자는 먼저 회사 Active Directory에 대해 인증해야 합니다.  AAD(Azure Active Directory)를 사용하도록 응용 프로그램을 구성하기 위해 다음과 같은 몇 가지 단계를 수행했습니다.

* 엔터프라이즈용 Azure Active Directory를 만들었습니다.
* 엔터프라이즈의 온-프레미스 Active Directory를 Azure Active Directory와 페더레이션했습니다.
* 마지막으로, Azure 앱 서비스의 쉬운 AAD 통합 기능을 사용하여 응용 프로그램을 AAD에 등록했습니다. 

최종적으로, 응용 프로그램이 사용자에게 AAD(및 확장을 통해 회사 AD)에 로그인하라는 메시지를 표시합니다.
	
![AAD Login][AADLogin]

AAD와 통합되도록 AAS를 설정하는 방법에 대한 자세한 내용은 [AAD와 AAS 통합][AASIntegrationwithAAD]을 참조하세요. 

## <a name="acceptablefiles"></a>온-프레미스 리소스 액세스

사용자가 AAD에 로그인하면 계획된 회사 출장 목록이 표시됩니다.  웹 응용 프로그램이 Microsoft Azure에서 실행되고 있으므로 이 정보를 포함하는 온-프레미스 SQL Server에 액세스할 방법이 필요합니다.

![Data from On-premises Sql Server][DatafromOnpremisesSqlServer]

응용 프로그램은 지점 및 사이트 간 VNET 통합 기능으로 구성됩니다.  따라서 응용 프로그램이 표준 데이터 액세스 논리(이 경우 Entity Framework)를 사용하여, 회사 네트워크 내부에서 실행되고 있는 원격 SQL Server에 투명하게 액세스할 수 있습니다.

지점 및 사이트 간 VNET 통합에 대한 자세한 내용은 [VNET 통합][VNETIntegration]을 참조하세요.

## <a name="acceptablefiles"></a>Office 365와의 통합

직원이 출장 레코드를 만들 때마다 웹 응용 프로그램은 SharePoint Online 목록에 출장 요청을 만듭니다. 응용 프로그램에는 직원이 SharePoint 목록에 쉽게 액세스하는 데 사용할 수 있는 링크가 있습니다.

![SharePoint List Link][SharepointListLink]

링크를 클릭하면 자동으로 SharePoint 목록으로 이동합니다.  직원은 회사 AAD에 로그인했으므로 AAD에서 발급한 보안 토큰을 사용하여 Office 365에 대해 투명하게 인증됩니다.

![SharePoint List][SharepointList]

웹 응용 프로그램은 SharePoint Online 문서 라이브러리에 출장 문서도 만듭니다.

![SharePoint Document Library][SharepointDocumentLibrary]

SharePoint Online에 만들어진 자산을 통해 웹 응용 프로그램은 Office 365의 기능을 활용할 수 있습니다.  예를 들어 SharePoint 목록에 항목이 만들어질 때마다 의사 결정/승인 워크플로가 트리거될 수 있습니다.

Office 365 통합에 대한 자세한 내용은 [Office 365 통합][Office365Integration]을 참조하세요.

## <a name="acceptablefiles"></a>SaaS 서비스와의 통합

현재 회사에서는 다양한 SaaS 서비스를 사용하고 있으며 다른 응용 프로그램의 SaaS 데이터와 상호 작용해야 합니다.  출장 응용 프로그램은
이 시나리오의 한 가지 예를 보여 줍니다.  출장 응용 프로그램은 직원이 고객 계정에 대한 출장을 계획할 때마다 Salesforce의 고객 계정 정보를 업데이트합니다.

![Salesforce Integration][SalesforceIntegration]

회사의 Salesforce 계정은 AAD 자격 증명을 사용하여 Salesforce에 대한 투명한 인증을 허용하도록 AAD로 설정됩니다. 따라서 직원이 AAD를 사용하여 출장 응용 프로그램에 로그인하면 응용 프로그램이 Salesforce에 저장된 데이터를 읽고 쓸 수 있습니다.

SaaS 통합에 대한 자세한 내용은 [SaaS 통합][SaaSIntegration]을 참조하세요.

## <a name="NextSteps"></a>다음 단계

자세한 내용은 [Azure 응용 프로그램 서비스][AzureApplicationServices]를 참조하세요.
 
[AASIntegrationwithAAD]:http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]:http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/ 
[Office365Integration]: app-service-cloud-app-platform.md
[SaaSIntegration]: app-service-cloud-app-platform.md
[AzureApplicationServices]: app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png

<!--HONumber=49-->