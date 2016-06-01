
<properties
    pageTitle="고객 가상 네트워크에 배포되는 Azure RemoteApp용 허용 목록에 대한 포트 및 URL 목록 | Microsoft Azure"
    description="Azure RemoteApp을 통해 통신하기 위해 구성해야 하는 포트 및 URL을 알아봅니다."
    services="remoteapp"
	documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/12/2016"
    ms.author="elizapo" />



# 고객 가상 네트워크에 배포된 Azure RemoteApp에 대한 액세스를 허용하는 포트 및 URL 목록입니다. 

다음은 클라우드나 하이브리드 컬렉션을 VNET(가상 네트워크)에 배포하는 경우 Azure RemoteApp에 적용됩니다. 가상 네트워크에 대한 자세한 내용은 [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요. 트래픽을 Azure RemoteApp에 대해 선택한 가상 네트워크 리소스로 제한하는 NSG(네트워크 보안 그룹)를 만든 경우 다음에 액세스할 수 있는지와 가상 네트워크의 보안 정책을 통해 허용되는지 확인하세요. 네트워크 보안 그룹에 대한 자세한 내용은 다음을 참조하세요. [네트워크 보안 그룹이란? (NSG)](../virtual-network/virtual-networks-nsg.md).

##  Azure RemoteApp 서브넷에 다음 끝점 및 URL에 대한 액세스 권한 필요: 
*	**.servicebus.windows.net
*	 **. servicebus.net
*	 https://*.remoteapp.windwsazure.com  
*	 https://www.remoteapp.windowsazure.com 
*	 https://*remoteapp.windowsazure.com  
*	 https://*.core.windows.net  
*	 아웃바운드: TCP: 443, TCP: 10101-10175 
*	 선택 사항 - UDP: 10201-10275  
 
## Azure RemoteApp 클라이언트에 다음 끝점 및 URL에 대한 액세스 권한 필요: 

데스크톱, 장치 등의 클라이언트로 Azure RemoteApp 컬렉션에서 배포된 앱에 연결합니다.

-  https://telemetry.remoteapp.windowsazure.com  
-  https://*.remoteapp.windowsazure.com(이 주소에 대한 선택적 UDP 포트입니다.) 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://*.core.windows.net  
-  아웃바운드: TCP: 443  
-  선택 사항 - UDP: 3391 

<!---HONumber=AcomDC_0518_2016-->