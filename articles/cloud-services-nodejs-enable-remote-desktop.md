<properties 
	pageTitle="클라우드 서비스에 원격 데스크톱 사용(Node.js)" 
	description="Azure Node.js 응용 프로그램을 호스트하는 가상 컴퓨터에 대해 원격 데스크톱 액세스를 사용하도록 설정하는 방법에 대해 알아봅니다." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Azure에서 원격 데스크톱 사용

원격 데스크톱을 사용하면 Azure에서 실행 중인 역할 인스턴스의 데스크톱에
액세스할 수 있습니다. 원격 데스크탑 연결을 사용하여
가상 컴퓨터를 구성하거나 응용 프로그램의 문제를
해결할 수 있습니다.

> [AZURE.NOTE] 이 문서의 단계는 Azure 클라우드 서비스로 호스트되는 노드 응용 프로그램에만 적용됩니다.

이 작업에는 다음 단계가 포함됩니다.

-   [1단계: Azure PowerShell을 사용하여 원격 데스크톱 액세스에 대한 서비스 구성]
-   [2단계: 역할 인스턴스에 연결]
-   [3단계: 원격 데스크톱 액세스를 사용하지 않도록 서비스 구성 Azure PowerShell 사용]

## <a name="step1"> </a>1단계: Azure PowerShell을 사용하여 원격 데스크톱 액세스에 대한 서비스 구성

원격 데스크톱을 사용하려면 서비스 정의와 사용자 이름, 암호 및 
클라우드의 역할 인스턴스로 인증하기 위한 인증서를 갖춘 
서비스 구성을 구성해야 합니다. [Azure PowerShell]은 **Enable-AzureServiceProjectRemoteDesktop** cmdlet를 포함합니다.
이 구성을 수행합니다.

서비스 정의를 만든 컴퓨터에서 다음 단계를
수행합니다.

1.  **시작** 메뉴에서 **Azure PowerShell**을 선택합니다.

	![Azure PowerShell start menu entry][powershell-menu]

2.  디렉터리를 서비스 디렉터리로 변경하고
    **Enable-AzureServiceProjectRemoteDesktop** 및 사용자 이름과
    클라우드에서 역할 인스턴스로 인증할 때 사용하는 암호를
    입력합니다.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  서비스 구성 변경 내용을 클라우드에 게시합니다. 다음의
    **Azure PowerShell** 프로프트에서
    **Publish-AzureServiceProject**를 입력합니다.

	![publish-azureserviceproject][publish-project]

이러한 단계가 완료되면, 클라우드에 있는 서비스의 역할 인스턴스가
원격 데스크톱 액세스를 위해 구성됩니다.

## <a name="step2"> </a>2단계: 역할 인스턴스에 연결

이제 Azure에서 배포가 실행되고 있으므로
역할 인스턴스에 연결할 수 있습니다.

1.  [Azure 관리 포털]에서 **클라우드 서비스**를 선택한 다음 위 1단계에서 배포한 서비스를 선택합니다.

	![azure management portal][cloud-services]

2.  **인스턴스**를 클릭한 다음 **프로덕션**이나 **스테이징**을 클릭하여 해당 서비스의 인스턴스를 확인합니다. 인스턴스를 선택하고 페이지 아래쪽에서 **연결**을 클릭합니다.

    ![The instances page][3]

2.  **연결**을 클릭하면 웹 브라우저에서
    .rdp 파일을 저장할지 묻습니다. Internet Explorer를 사용 중이면 **열기**를 클릭합니다.

    ![prompt to open or save the .rdp file][4]

3.  파일이 열리면 다음 보안 프롬프트가 나타납니다.

    ![Windows security prompt][5]

4.  **연결**을 클릭하면 인스턴스에 액세스할 자격 증명을 입력하기 위한
    보안 프롬프트가 나타납니다. 만든 암호를 입력합니다.
    [Step 1][1단계: Azure PowerShell을 사용하여 원격 데스크톱 액세스에 대한 서비스 구성]을 사용하여 원격 데스크톱 액세스에 대한 서비스를 구성한 후 **확인**을 클릭합니다.

    ![username/password prompt][6]

연결되면 원격 데스크톱 연결은
Azure의 인스턴스의 데스크톱을 보여줍니다. 성공적으로
인스턴스에 원격으로 액세스하고 필요한 모든 작업을 수행할 수 있으며
응용 프로그램을 관리합니다.

![Remote desktop session][7]

## <a name="step3"> </a>3단계: Azure PowerShell을 사용하여 원격 데스크톱 액세스를 사용하지 않도록 서비스 구성

클라우드의 역할 인스턴스에 원격 데스크톱 연결이 더 이상 필요하지 않으면,
[Azure PowerShell]을 사용하여 원격 데스크톱 액세스를 사용하지 않도록 합니다.

1.  **시작** 메뉴에서 **Azure PowerShell**을 선택합니다.

2.  디렉터리를 서비스 디렉터리로 변경하고
    **Disable-AzureServiceProjectRemoteDesktop**을 입력합니다.

3.  서비스 구성 변경 내용을 클라우드에 게시합니다. 다음의
    **Azure PowerShell** 프로프트에서
    **Publish-AzureServiceProject**를 입력합니다.

## 추가 리소스

- [Azure에서 역할 인스턴스 원격 액세스] 
- [Azure 역할과 함께 원격 데스크톱 사용]

  [1단계: Azure PowerShell을 사용하여 원격 데스크톱 액세스에 대한 서비스 구성]: #step1
  [2단계: 역할 인스턴스에 연결]: #step2
  [3단계: 원격 데스크톱 액세스를 사용하지 않도록 서비스 구성 Azure PowerShell 사용]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure 관리 포털]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
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

<!--HONumber=45--> 
