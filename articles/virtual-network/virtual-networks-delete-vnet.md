<properties 
   pageTitle="VNet(가상 네트워크)을 삭제하는 방법"
   description="기존 VNet을 삭제하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/29/2015"
   ms.author="telmos" />

# VNet(가상 네트워크)을 삭제하는 방법

VNet을 삭제하려는 경우 단지 **삭제**를 클릭하여 삭제할 수 없습니다. 다음과 같이 먼저 수행해야 할 몇 가지 사항이 있습니다.

1. **설정 저장(옵션)** - 가상 네트워크의 설정을 로컬 파일에 저장하려는 경우 가상 네트워크를 삭제하기 전에 먼저 구성 파일을 내보냅니다. 자세한 내용은 [가상 네트워크 설정을 네트워크 구성 파일로 내보내기](https://msdn.microsoft.com/library/azure/dn133804.aspx)를 참조하세요. 설정 저장을 통해 필요한 경우 VNet을 나중에 다시 만들 수 있습니다.

1. **가상 네트워크 게이트웨이 삭제** - VNet에 대한 게이트웨이를 구성한 경우 VNet을 삭제하기 전에 먼저 해당 게이트웨이를 삭제해야 합니다. 가상 네트워크 게이트웨이를 삭제하려면 가상 네트워크의 대시보드 페이지로 이동합니다. 페이지 맨 아래에서 **게이트웨이 삭제**를 클릭합니다.
						
	다음 단계를 계속 진행하기 전에 게이트웨이가 삭제될 때까지 5-10분을 기다려야 합니다.

1. **클라우드 서비스, 웹 사이트 및 VM 삭제** - VNet에 배포한 개체가 있는 경우 먼저 해당 개체를 삭제해야 VNet을 삭제할 수 있습니다.

1. **가상 네트워크 삭제** - *이름* 행의 오른쪽을 클릭하여 삭제하려는 VNet을 강조 표시한 후 페이지 맨 아래에 있는 **삭제**를 클릭합니다. 화면상의 지침을 따릅니다.

1. **추가 작업** - 또한 가상 네트워크를 삭제한 후 로컬 네트워크 설정, DNS 서버 및 선호도 그룹을 삭제하도록 선택할 수도 있습니다.
 

<!---HONumber=July15_HO4-->