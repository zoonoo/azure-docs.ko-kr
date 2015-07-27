<properties 
    pageTitle="RemoteApp의 VNET에 대한 크기 정보"
    description="VNET에서 실행 중인 RemoteApp의 IP 주소 요구 사항에 대해 알아봅니다." 
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
    ms.date="04/29/2015" 
    ms.author="elizapo" />



# RemoteApp의 VNET에 대한 크기 정보

VNET(가상 네트워크)에서 RemoteApp을 사용할 경우 RemoteApp에서는 서브넷 내의 IP 주소를 사용합니다. RemoteApp 서비스의 크기에 따라 서브넷에 RemoteApp 가상 컴퓨터에 사용할 수 있는 IP 주소가 충분한지 확인해야 합니다. 이 크기 조정 지침은 컬렉션 내에서 RemoteApp이 가상 컴퓨터를 위 또는 아래로 동적으로 회전하는 방법을 완벽하게 나타내지는 않지만, 서브넷 범위를 예측하는 데 도움이 됩니다. 이는 RemoteApp 서비스가 VNET 내에 배치된 경우 RemoteApp을 제거하지 않고는 서브넷 크기를 늘릴 수 없으므로 특히 중요합니다.

최대 용량으로 실행하려는 각 RemoteApp 컬렉션에 대해 100개의 IP 주소를 사용할 수 있어야 합니다. 예를 들어 표준 요금제에 RemoteApp 컬렉션이 하나 있고 최대 500명의 사용자를 허용하려면 해당 컬렉션에 대해 100개의 IP 주소가 있어야 합니다. 마찬가지로 800명의 사용자가 있는 기본 요금제의 RemoteApp 컬렉션의 경우에도 100개의 IP 주소가 필요합니다. 최대값보다 적은 수의 사용자를 허용하려는 경우 컬렉션당 필요한 IP 주소 수를 줄일 수 있습니다. 최소 서브넷 크기 요구 사항은 30개 IP 주소입니다(/ 27).
 

<!---HONumber=July15_HO2-->