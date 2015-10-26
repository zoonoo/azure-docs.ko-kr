<properties 
	pageTitle="Microsoft Azure 관리 API 인증서를 포털에 업로드" 
	description="Microsoft Azure의 관리 API 인증서를 업로드하는 방법 배우기" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="na" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015"
	ms.author="adegeo"/>


# Azure 관리 API 관리 인증서 업로드

관리 인증서를 사용하면 Azure에서 제공하는 서비스 관리 API를 사용한 인증이 가능합니다. 많은 프로그램과 도구(예: Visual Studio 또는 Azure SDK)에서 인증서를 사용하여 다양한 Azure 서비스의 구성 및 배포를 자동화합니다. 이것은 클라우드 서비스와 사실상 연관되어 있지 않습니다.

>[AZURE.WARNING]주의가 필요합니다! 이런 종류의 인증서를 사용하면 인증서로 인증하는 사람 누구나 연관된 구독을 관리할 수 있습니다.

Azure 인증서(자체 서명 인증서 만들기를 비롯한)에 대한 자세한 정보는 필요한 경우 [사용](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)할 수 있습니다.

[Azure Active Directory](http://azure.microsoft.com/documentation/services/active-directory/)를 사용하여 자동화를 위한 클라이언트 코드를 인증할 수도 있습니다.

## 관리 인증서 업로드

관리 인증서(공개 키만 포함하는 .cer 파일)를 만든 후에는 포털에 업로드할 수 있습니다. 포털에서 인증서를 사용할 수 있는 경우 일치하는 인증서(개인 키)가 있는 모든 사람이 관리 API를 통해 연결할 수 있고 연결된 구독에 대한 리소스에 액세스할 수 있습니다.

1. [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.
2. 포털 왼쪽(필요한 경우 아래로 스크롤 하여)의 **설정**을 클릭합니다. 
    
    ![설정](./media/azure-api-management-certs/settings.png)

3. **관리 인증서** 탭을 클릭합니다.

    ![설정](./media/azure-api-management-certs/certificates-tab.png)
    
4. **업로드** 단추를 클릭합니다.

    ![설정](./media/azure-api-management-certs/upload.png)
    
5. 대화 상자 정보를 입력하고 완료 **확인 표시**를 클릭합니다.

    ![설정](./media/azure-api-management-certs/upload-dialog.png)

## 다음 단계

이제 구독과 연관된 관리 인증서가 있으므로(로컬에서 일치하는 인증서를 설치한 후에) [서비스 관리 REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx)에 프로그래밍 방식으로 연결할 수 있고 구독과 연관된 다양한 Azure 리소스를 자동화할 수 있습니다.

<!---HONumber=Oct15_HO3-->