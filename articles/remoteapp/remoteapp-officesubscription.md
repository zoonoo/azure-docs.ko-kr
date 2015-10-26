
<properties 
    pageTitle="Azure RemoteApp으로 Office 365 구독을 사용하는 방법 | Microsoft Azure"
	description="Azure RemoteApp에서 Office 365 구독을 사용하여 Office 앱을 공유하는 방법을 알아봅니다."
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/24/2015" 
    ms.author="elizapo" />



# Azure RemoteApp으로 Office 365 구독을 사용하는 방법

Azure RemoteApp에서 기존 Office 365 구독을 사용하여 클라우드에서 오피스 앱을 공유할 수 있는 것을 아십니까? 구독의 대부분을 만들도록 하는 Office 365에 대한 문서의 링크를 포함하는 Office 365 + Azure RemoteApp 옵션에 대한 내용을 읽습니다.

## Office 365 구독을 사용하여 Azure RemoteApp에서 Office 응용 프로그램을 실행할 수 있습니까?

예! 사실상, Office 365 구독의 사용은 Office 응용 프로그램을 Azure RemoteApp로 전환하는 유일한 방법입니다.

(참고: 사용자의 Azure RemoteApp 배포가 호스팅 파트너에 의해 제공되는 경우 해당 파트너는 [서시브 공급자 Service Provider 라이선스 약관](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx)을 기반으로 Office 사용권을 제공할 수 있습니다.)


Office 365 구독의 가장 큰 장점은 Azure 클라우드를 포함하여 다양한 플랫폼과 환경에 동일한 사용자 라이선스를 사용하도록 한다는 점입니다. Azure RemoteApp에서 Office 응용 프로그램을 사용하는 경우 추가 라이선스를 구입하거나 어떤 특별한 방식으로 기존 라이선스를 구성할 필요가 없습니다. [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx)를 포함하는 Office 365 구독만 필요합니다.

Office 365 ProPlus를 사용하면 [공유 컴퓨터 활성화](https://technet.microsoft.com/library/Dn782860.aspx)가 가능합니다. 이 기능을 사용하면 Azure RemoteApp(및 원격 데스크톱 서비스)과 같은 가상 및 클라우드 환경에서 Office에 대한 임시 사용자 기반을 활성화할 수 있습니다.

어떤 Office 365 계획이 Office 365 ProPlus를 포함하나요? [각 계획 내에서 서비스 가용성](https://technet.microsoft.com/library/office-365-plan-options.aspx) 테이블을 확인합니다. 계획이 모두 Office 365 ProPlus를 포함하지는 않습니다.(예: Office 365 비즈니스 계획) 계획이 포함하지 않는 경우 포함하는 계획으로 업그레이드하는 것이 좋습니다. (예: Office 365 교육 E3)

## 이제 그래서 Azure RemoteApp와 함께 사용하는 Office 365 ProPlus 라이선스는 어떻나요?

Office 365 ProPlus에 대한 각 사용자 라이선스를 사용하면 단일 사용자가 태블릿 및 휴대폰에 더해 최대 5개의 컴퓨터에 Office 응용 프로그램을 활성화할 수 있습니다. 장치에서 Office를 비활성화할 때까지 각 활성화는 사용자로 등록됩니다. (사용자는 [Office 365 포털](https://portal.office365.com/)에서 장치를 관리할 수 있습니다.)

Azure RemoteApp을 사용하면 단일 사용자가 인식하지 않고 같은 날짜에 여러 대의 컴퓨터에 로그인할 수 있습니다. 이는 사용자에게 공유된 앱과 프로그램만 표시하는 동시에 서비스에서 자동으로 클라우드의 리소스를 관리하고 크기를 조정하기 때문입니다. 이 시나리오를 위해 Office 365 ProPlus는 공유 컴퓨터 인증 모드를 제공합니다. 즉, 사용자는 이러한 리소스에 액세스하기 위해 라이선스 관리를 수행할 필요가 없으며 개별 컴퓨터가 컴퓨터 정품 인증 제한 5대에 계산되지 않습니다.

(관리자가)사용자에게 Office 365 ProPlus 라이선스를 할당하는 한 사용자는 개인 장치는 물론 Azure RemoteApp 컬렉션을 통해 Office를 사용할 수 있습니다.

## 어떤 Office 응용 프로그램을 Office 365 및 Azure RemoteApp과 함께 사용할 수 있나요?

Office 365 ProPlus 구독을 사용하여 Office 2013 및 Office 2016(출시된 후에)를 공유할 수 있습니다. Azure RemoteApp는 이전 버전의 Office를 지원하지 않습니다.

## Visio Pro 또는 Project Pro의 경우는 어떤가요?

RemoteApp 구독에 포함된 Office 365 ProPlus 이미지는 Visio Pro 및 Project Pro를 모두 포함합니다. 하지만 Office 365 ProPlus 구독을 사용하여 해당 프로그램을 활성화할 수 없습니다. 각자 자신의 라이선스가 있습니다. [Office 365 포털](https://portal.office365.com/)에서 정품 인증할 수 있습니다.

이러한 프로그램을 사용하지 않으려는 경우 라이선스가 필요하지 않습니다. 방금 사용하려는 프로그램을 활성화하고 다른 프로그램을 건너뜁니다. 이미지에 계속 표시되지만 사용할 수는 없습니다.

## 어떻게 Office 365 및 Azure RemoteApp을 시작하나요?

Office 365 라이선스의 세부 정보를 파악했으므로 Azure RemoteApp에서 사용하기 시작합니다. 사용이 매우 쉽습니다.

Azure RemoteApp 컬렉션을 만들 때 **Office 365 ProPlus(구독 필수)** 이미지를 사용합니다.

![Office 365 Pro Plus와 Azure RemoteApp 이미지](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


이 이미지는 Windows Server 및 Office 365 ProPlus의 최신 버전을 포함합니다. 컬렉션(게시 앱 포함)을 구성한 후에 (RemoteApp 클라이언트를 사용하여) Azure RemoteApp에 로그인하고 Office 앱에 Office 365 자격 증명을 제공합니다. 라이선스는 필요한 설정 또는 관리 없이 자동으로 제공됩니다.

## Office 365 ProPlus를 사용하여 사용자 지정 이미지를 만들 수 있나요?

Office 365 ProPlus를 포함하는 컬렉션에 사용자 지정 이미지를 만들 수 있습니다. 2개의 선택 사항이 있습니다. 제공된 Azure 갤러리 이미지를 사용하거나 사용자 고유의 사용자 지정 이미지를 만들고 Office 365 ProPlus를 설치할 수 있습니다.

### Azure 갤러리 이미지 사용

컬렉션에 Office 365 ProPlus를 배포하는 가장 쉬운 방법은 Azure RemoteApp 구독에 포함된 [Azure 갤러리 이미지 중 하나로 시작](remoteapp-image-on-azurevm.md)하는 것입니다. **Office 365 ProPlus가 미리 설치된 Windows 서버 원격 데스크톱 세션 호스트** 이미지를 선택합니다. 그런 다음 해당 이미지에 원하는 다른 앱을 설치하면 준비가 되었습니다.

### 사용자 지정 이미지 사용

항상 사용자 지정 이미지를 만들 수 있습니다. [Azure VM](remoteapp-image-on-azurevm.md) 또는 [로컬로 이미지 만들기](remoteapp-create-custom-image.md)를 만들고 Azure에 업로드할 수 있습니다. 두 경우 모두, 공유 컴퓨터 활성화 노드를 사용하여 Office 365 ProPlus 설치하도록 합니다. [Office 배포 도구](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx)를 사용하고 설치용 [지침](https://technet.microsoft.com/library/Dn782858.aspx)을 수행합니다.

### 사용자 지정 이미지에서 Office 365 ProPlus에 대한 자동 업데이트를 사용하지 않도록 설정합니다. 중요합니다.

사용자 지정 이미지는 Azure RemoteApp에서 사용자의 요청이 증가함에 따라 추가 리소스를 추가하기 위한 템플릿으로 사용됩니다. 지연 및 연결 문제를 방지하려면 이미지에서 Office에 대한 자동 업데이트를 사용하지 않도록 설정합니다. 이렇게 하지 않으면 시작할 때 해당 템플릿으로 만든 모든 리소스가 자동으로 업데이트됩니다. 사용자 지정 이미지의 업데이트에 표준 Azure RemoteApp 프로세스를 대신 사용합니다. 이러한 방법으로 템플릿 이미지에서 Office 응용 프로그램을 업데이트하고 Azure RemoteApp는 사용자가 업데이트를 하도록 처리합니다.

자동 업데이트를 사용하지 않으려면 다음을 Office 배포 도구 구성 파일에 추가합니다.

		<Updates Enabled="FALSE" />

그래서 구성 파일에 다음 줄이 포함되어야 합니다.
	
		<Display Level="NONE" AcceptEULA="TRUE" />
		<Property Name="SharedComputerLicensing" Value="1" />
		<Updates Enabled="FALSE" />

## 그래서 어떻게 Office 365 ProPlus로 이미지를 업데이트할 수 있나요?

컬렉션에서 이미지를 업데이트하는 데 많은 이유가 있습니다. 다음에 몇몇 이유가 있습니다.

- 최신 Windows 업데이트 받기 
- 최신 Office 365 ProPlus 응용 프로그램 업데이트 받기
- 사용자 지정 앱 업데이트
- 이미지 자체에 대한 기타 구성 설정 변경

업데이트 이미지를 사용하는 컬렉션을 업데이트하기 위한 종단 간 단계는 [여기](remoteapp-update.md)로 이동합니다. 하지만 이미지 및 Office 365 ProPlus를 업데이트하는 방법에 대한 자세한 내용은 다음 정보를 확인합니다.

이미지를 업데이트하는 데 두 가지 옵션이 있습니다. 이미지를 완전히 새로운 것으로 대체하거나 수동으로 기존 이미지를 업데이트합니다.

### 이미지를 최신 Azure 갤러리 이미지로 대체하고 사용자 지정을 추가합니다.
이 옵션을 사용하여 Microsoft가 Windows Server 및 Office 365 ProPlus 업데이트를 관리할 수 있습니다. 기존 이미지를 업데이트하는 대신 최신 갤러리 이미지에 따라 완전히 새로운 이미지를 만듭니다. 그런 다음 사용자 지정 앱을 설치, 이미지 구성 수정 등 이미지를 사용자 지정하기 전에 수행한 단계를 반복합니다.

갤러리 이미지는 정기적으로 업데이트되므로 Windows Server 및 Office 365 ProPlus 앱이 최신인지 파악하여 쉽게 휴지할 수 있습니다. 물론 장단점은 새 이미지를 가져 올 때마다 사용자 지정을 적용해야 한다는 것입니다. 스크립트를 만들어 사용자 지정 설정을 자동화할 수 있습니다.

### 기존 이미지를 수동으로 업데이트

이 옵션을 사용하여 표준 Windows 도구를 사용하여 이미지에 업데이트를 적용합니다. Office 365 ProPlus의 경우 Office 배포 도구를 사용하여 최신 업데이트 또는 Office 365 ProPlus의 버전을 다운로드하고 설치합니다.

> [AZURE.IMPORTANT]Office 365 ProPlus 자동 업데이트를 사용하지 않도록 설정합니다.

업데이트에 Office 배포 도구를 사용하는 데 대한 자세한 정보가 필요하나요?

- [Office 배포 도구를 사용하여 Office 365 제품에 대한 간편 실행 배포](https://technet.microsoft.com/library/JJ219423.aspx)
- [Office 배포 도구를 사용하여 Office 365 ProPlus 배포 및 업데이트](https://channel9.msdn.com/Events/Ignite/2015/BRK3168)(동영상)
- [Office 365 ProPlus에 대한 업데이트 설정 구성](https://technet.microsoft.com/library/dn761708.aspx)

<!---HONumber=Oct15_HO3-->