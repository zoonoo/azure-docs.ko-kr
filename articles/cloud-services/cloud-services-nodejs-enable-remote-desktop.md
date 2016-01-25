<properties 
	pageTitle="클라우드 서비스에 대해 원격 데스크톱 사용(Node.js)" 
	description="Azure Node.js 응용 프로그램을 호스트하는 가상 컴퓨터에 대해 원격 데스크톱 액세스를 사용하도록 설정하는 방법에 대해 알아봅니다." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>


# Azure에서 원격 데스크톱 사용

원격 데스크톱을 사용하면 Azure에서 실행 중인 역할 인스턴스의 데스크톱에 액세스할 수 있습니다. 원격 데스크톱 연결을 사용하여 가상 컴퓨터를 구성하거나 응용 프로그램의 문제를 해결할 수 있습니다.

> [AZURE.NOTE]이 문서는 Azure 클라우드 서비스로 호스트된 Node.js 응용 프로그램에 적용됩니다.


## 필수 조건

- [Azure Powershell](../install-configure-powershell.md)을 설치하고 구성합니다.
- Node.js 앱을 Azure 클라우드 서비스에 배포합니다. 자세한 내용은 [Node.js 응용 프로그램을 만들어서 Azure 클라우드 서비스에 배포](cloud-services-nodejs-develop-deploy-app.md)를 참조하세요.


## 1단계: Azure PowerShell을 사용하여 원격 데스크톱 액세스에 대한 서비스 구성

원격 데스크톱을 사용하려면 Azure 서비스 정 및 구성을 사용자 이름, 암호 및 인증서와 함께 업데이트해야 합니다.

앱에 대한 소스 파일이 포함된 컴퓨터에서 다음 단계를 수행합니다.

1. **Azure PowerShell** 관리자 권한으로 실행합니다. (**시작 메뉴** 또는 **시작 화면**에서 **Azure PowerShell**을 검색합니다.)

2.  서비스 정의(.csdef) 및 서비스 구성(.cscfg) 파일이 포함된 디렉터리로 이동합니다.

3. 다음 PowerShell cmdlet를 입력합니다.

		Enable-AzureServiceProjectRemoteDesktop

4. 프롬프트에서 사용자 이름 및 암호를 입력합니다.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  변경 내용을 게시하려면 다음 PowerShell cmdlet을 입력합니다.

    	Publish-AzureServiceProject

	![publish-azureserviceproject][publish-project]

## 2단계: 역할 인스턴스에 연결

업데이트 서비스 정의를 게시한 후에 역할 인스턴스에 연결할 수 있습니다.

1.  [Azure 클래식 포털]에서 **클라우드 서비스**를 선택한 다음 해당 서비스를 선택합니다.

	![Azure 클래식 포털][cloud-services]

2.  **인스턴스**를 클릭한 다음 **프로덕션**이나 **스테이징**을 클릭하여 해당 서비스의 인스턴스를 확인합니다. 인스턴스를 선택하고 페이지 아래쪽에서 **연결**을 클릭합니다.

    ![인스턴스 페이지][3]

2.  **연결**을 클릭하면 웹 브라우저에서 .rdp 파일을 저장할지 묻습니다. 이 파일을 엽니다. (예를 들어, Internet Explorer를 사용 중이면 **열기**를 클릭합니다.

    ![.rdp 파일을 열거나 저장할지 물음][4]

3.  파일이 열리면 다음 보안 프롬프트가 나타납니다.

    ![Windows 보안 프롬프트][5]

4.  **연결**을 클릭하면 인스턴스에 액세스할 자격 증명을 입력하기 위한 보안 프롬프트가 나타납니다. [1단계][1단계: Azure PowerShell을 사용하여 원격 데스크톱 액세스에 대한 서비스를 구성]에서 생성한 암호를 입력한 후 **확인**을 클릭합니다.

    ![사용자 이름/암호 프롬프트][6]

연결되면 원격 데스크톱 연결은 Azure에서 인스턴스의 바탕 화면을 표시합니다.

![원격 데스크톱 세션][7]

## 3단계: 원격 데스크톱 액세스를 사용하지 않도록 서비스 구성 

클라우드의 역할 인스턴스에 대한 원격 데스크톱 연결이 더 이상 필요하지 않으면 [Azure PowerShell]을 사용하여 원격 데스크톱 액세스를 사용하지 않도록 설정합니다.

1.  다음 PowerShell cmdlet를 입력합니다.

    	Disable-AzureServiceProjectRemoteDesktop

2.  변경 내용을 게시하려면 다음 PowerShell cmdlet을 입력합니다.

    	Publish-AzureServiceProject

## 추가 리소스

- [Azure에서 역할 인스턴스 원격 액세스] 
- [Azure 역할과 함께 원격 데스크톱 사용]
- [Node.js 개발자 센터](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure 클래식 포털]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Azure에서 역할 인스턴스 원격 액세스]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Azure 역할과 함께 원격 데스크톱 사용]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 

<!---HONumber=AcomDC_0114_2016-->