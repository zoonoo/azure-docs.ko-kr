<properties 
    pageTitle="Azure VM에 따라 RemoteApp 이미지 만들기"
    description="Azure 가상 컴퓨터를 시작하는 RemoteApp에 대한 이미지를 만드는 방법에 대해 알아봅니다." 
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
    ms.date="03/27/2015" 
    ms.author="elizapo" />



# Azure 가상 컴퓨터에 따라 RemoteApp 이미지 만들기

많은 고객의 의견에 따라, 이제 Azure 가상 컴퓨터에서 RemoteApp 이미지(컬렉션에서 공유하는 앱 포함) 작성을 지원할 수 있어 기쁘게 생각합니다. RemoteApp 이미지 요구 사항을 모두 충족하는 Azure VM 이미지 갤러리에 가상 컴퓨터 이미지를 추가 했습니다. 원하는 경우 해당 VM 이미지를 출발점으로 이용하여 자신의 VM을 만들 수 있습니다  "Windows Server 원격 데스크톱 세션 호스트" 이미지를 라이브러리에서 찾으면 됩니다.

이미지를 만든 다음 Azure VM 라이브러리에서 RemoteApp으로 업로드하는 두 단계가 있습니다.

## Azure VM에 따라 사용자 지정 이미지 만들기

Azure VM을 기반으로 이미지를 만들려면 이 단계를 사용합니다.

1. Azure 가상 컴퓨터를 만듭니다. Azure 가상 컴퓨터 이미지 갤러리에서 "Windows Server 원격 데스크톱 세션 호스트" 이미지를 사용할 수 있습니다. 이 이미지는 Azure RemoteApp 템플릿 이미지 요구 사항을 모두 충족합니다. 

	자세한 내용은 [Windows를 실행 하는 VM 만들기](virtual-machines-windows-tutorial.md).를 참조하세요.

2. VM에 연결하고 RemoteApp을 통해 공유하려는 앱을 구성합니다. 앱에 필요한 모든 추가 Windows 구성을 수행할 수 있는지 확인합니다. 

	자세한 내용은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-log-on-windows-server.md).을 참조하세요. 

3. Windows Server 원격 데스크톱 세션 호스트 이미지를 사용하는 경우 VM이 RemoteApp pre reqs를 충족하는지 확인하는 유효성 검사 스크립트가 포함되어 있습니다. 스크립트를 실행하려면 바탕 화면에서 **ValidateRemoteAppImage**를 두 번 클릭합니다. 다음 단계를 진행하기 전에 스크립트에서 보고한 모든 오류가 해결되었는지 확인합니다.

4. SYSPREP가 일반화되고 이미지를 캡처합니다. 지침은 [템플릿으로 사용할 Windows 가상 컴퓨터를 캡처하는 방법](virtual-machines-capture-image-windows-server.md)을 참조하세요.

 

## RemoteApp 이미지 라이브러리로 이미지 가져오기

다음 단계를 사용하여 새 이미지를 RemoteApp으로 가져옵니다.

1. **템플릿 이미지**에서 탭:
	- 기존 이미지가 없는 경우 **템플릿 이미지를 업로드하거나 가져오기**를 클릭합니다. 
	- 적어도 하나의 이미지가 이미 있는 경우 **+**를 클릭하여 새 이미지를 추가합니다.

2. **가상 컴퓨터에서 이미지 가져오기** 라이브러리를 선택한 후 **다음**을 클릭합니다.

3. 다음 페이지에서 목록에서 사용자 지정 이미지를 선택하고 이미지를 만들 때 나열된 단계를 수행했는지 확인합니다. **다음**을 클릭합니다.
4. 새 RemoteApp 이미지에 대한 이름을 입력하고 위치를 선택한 다음 확인 표시를 클릭하여 가져오기 프로세스를 시작합니다.

> [AZURE.NOTE] Azure 가상 컴퓨터에서 지원하는 모든 Azure 위치에서 Azure RemoteApp에서 지원하는 모든 Azure 위치로 이미지를 가져올 수 있습니다. 가져오기는 위치에 따라 최대 25분까지 걸릴 수 있습니다.

이제, 사용자의 요구에 따라 [클라우드](remoteapp-create-cloud-deployment.md) 컬렉션 또는 [하이브리드](remoteapp-create-hybrid-deployment.md)의 새로운 컬렉션을 만들 준비가 되었습니다.

<!--HONumber=52-->