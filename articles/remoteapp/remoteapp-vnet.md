
<properties
    pageTitle="Azure RemoteApp과 함께 사용하기 위해 Azure VNET의 유효성 검사 | Microsoft Azure"
    description="Azure VNET이 Azure RemoteApp과 함께 사용할 준비가 되었는지 확인하는 방법을 알아봅니다."
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
    ms.date="08/15/2016"
    ms.author="elizapo" />



# Azure RemoteApp과 함께 사용하기 위해 Azure VNET의 유효성을 검사

> [AZURE.IMPORTANT]
Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148)을 읽어보세요.

Azure RemoteApp과 함께 Azure VNET을 사용하기 전에 VNET의 유효성을 검사하는 것이 좋습니다. 이렇게 하면 연결 문제를 방지할 수 있습니다.

Azure VNET의 유효성을 검사하려면 다음을 수행합니다.

1. Azure RemoteApp과 함께 사용하려는 Azure VNET의 서브넷 내에 Azure 가상 컴퓨터를 만듭니다.

2. 관리 포털의 **연결** 옵션을 사용하여 이 VM에 연결합니다.
3. 가상 컴퓨터를 Azure RemoteApp과 함께 사용하려는 동일 도메인에 가입시켜 봅니다. 온-프레미스 네트워크에 연결하는 하이브리드 연결을 만드는 경우 가상 컴퓨터를 사용자의 로컬 도메인에 조인합니다.

성공할 경우 Azure VNET은 RemoteApp과 함께 사용할 준비가 된 것입니다.

종단간 하이브리드 컬렉션 워크플로에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure RemoteApp에 대한 가상 네트워크를 계획하는 방법](remoteapp-planvnet.md)
- [하이브리드 컬렉션 만들기](remoteapp-create-hybrid-deployment.md)
- [Azure RemoteApp 컬렉션을 Azure 가상 네트워크에 배포(ExpressRoute 지원 포함)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)

<!---HONumber=AcomDC_0817_2016-->