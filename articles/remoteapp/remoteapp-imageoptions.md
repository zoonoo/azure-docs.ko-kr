<properties 
    pageTitle="Azure RemoteApp 이미지 만들기"
    description="RemoteApp에 대한 이미지를 만드는 데 사용할 수 있는 옵션을 알아봅니다." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />



# Azure RemoteApp 이미지 만들기

Azure RemoteApp에서는 이미지를 사용하여 사용자와 공유할 앱을 보관합니다. 선택한 응용 프로그램을 포함하는 클라우드 또는 하이브리드 RemoteApp 컬렉션을 만들려면 먼저 설치된 응용 프로그램을 포함하는 이미지를 만듭니다. 그런 다음 해당 이미지를 사용하는 컬렉션을 만들고 컬렉션에 사용자를 할당한 후 해당 사용자에게 앱을 게시합니다.

이미지를 만들거나 사용하기 위한 다양한 옵션이 있습니다. 이미지에 대한 기본 [요구 사항](remoteapp-imagereqs.md)으로는 Windows Server 2012 R2를 실행하고 RDSH(원격 데스크톱 세션 호스트) 역할이 설치되어 있어야 합니다. 이미지를 얻는 방법이 흥미롭습니다.

다음과 같은 옵션을 사용하여 이미지에 연결할 수 있습니다.

- [Azure 가상 컴퓨터를 기반으로 이미지](remoteapp-image-on-azurevm.md)를 가져와서 사용할 수 있습니다. 이 방법은 사용자 지정 설정이 필요한 LOB(기간 업무) 앱에 적합합니다. 앱에서 작동하도록 이미지를 사용자 지정할 수 있습니다.
- [사용자 지정 이미지를 만든 후 업로드](remoteapp-create-custom-image.md)할 수 있습니다. 이 방법은 온-프레미스 원격 데스크톱 서비스 배포에 사용할 이미지가 이미 있는 경우에 유용합니다.
- RemoteApp 구독에 포함된 [템플릿 이미지](remoteapp-images.md) 중 하나를 사용할 수 있습니다. 이러한 이미지는 RemoteApp 팀에서 만들어서 유지 관리하며 사용자에게 제공할 수 있는 일부 표준 응용 프로그램(예: Office 제품군)을 포함합니다. 프로덕션 설정에서는 Office 365 Pro Plus 이미지만 사용할 수 습니다.

이미지를 가져오는 위치 또는 만드는 방법에 관계없이 앱이 RemoteApp에서 올바르게 작동하려면 [앱 요구 사항](remoteapp-appreqs.md)을 이해해야 합니다. 다음 단계에서는 [클라우드](remoteapp-create-cloud-deployment.md) 또는 [하이브리드](remoteapp-create-hybrid-deployment.md) 컬렉션을 만듭니다.
 

<!---HONumber=July15_HO4-->