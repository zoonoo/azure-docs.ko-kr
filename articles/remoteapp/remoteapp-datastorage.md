
<properties
    pageTitle="Azure RemoteApp에 대한 사용자 지정 이미지에 중요 데이터를 저장하지 않음 | Microsoft Azure"
    description="Azure RemoteApp의 사용자 지정 이미지에 데이터를 저장하기 위한 지침 알아보기"
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
    ms.date="04/07/2016"
    ms.author="elizapo" />


# 사용자 지정 이미지에 중요 데이터를 저장하지 않음

Azure RemoteApp에서 사용자 고유의 응용 프로그램을 호스팅할 때는 먼저 사용자 지정 이미지를 만들어야 합니다. 사용자 지정 이미지를 사용하여 사용자에게 앱을 서비스하는 VM 인스턴스를 만듭니다. 사용자 지정 이미지는 응용 프로그램만 포함해야 하며, SQL 데이터베이스, 개인 파일, 특수 데이터 파일(예: QuickBooks) 등의 손실될 수 있는 중요 데이터는 포함할 수 없습니다. 모든 중요 데이터는 파일 서버에서 Azure RemoteApp 외부, 다른 Azure VM 또는 SQL Azure에 있어야 합니다. 이미지는 데이터 원본에 연결되고 데이터를 표시하는 응용 프로그램만 호스팅해야 합니다. 자세한 내용은 [Azure RemoteApp 이미지에 대한 요구 사항](remoteapp-imagereqs.md)을 검토하세요.

중요 데이터를 저장해서는 안 되는 이유를 이해하려면 Azure RemoteApp의 작동 방식을 이해해야 합니다. 컬렉션을 만들거나 업데이트할 때는 백그라운드에서 여러 차례의 이미지 복제 또는 복사본이 만들어집니다. 이러한 모든 VM 인스턴스는 사용자 지정 이미지의 정확한 복제본입니다. 사용자가 응용 프로그램을 실행할 때 이 VM 인스턴스 중 하나와 연결됩니다. 그러나 이 인스턴스들은 비영구적이기 때문에 동일한 인스턴스가 보장되는 것은 아니며 문제가 되지도 않습니다. 응용 프로그램을 호스팅하는 VM 인스턴스는 비영구적이며 컬렉션 업데이트 중에 파괴, 삭제 등이 일어날 수 있습니다.

컬렉션이 프로비전되어 사용자가 VM 연결을 시작하면 사용자 데이터가 영구적인 것이 되어 보호됩니다. [사용자 프로필 디스크(UPD)](remoteapp-upd.md)라고 하는 VHD 안에서 c:\\users<userprofile>에 있는 별도의 저장소에 저장되기 때문입니다. 응용 프로그램이 시작되면 UPD가 탑재되며 운영 체제에서 로컬 사용자 프로필과 동일하게 처리됩니다. [Azure RemoteApp이 사용자 데이터와 설정을 저장하는 방법](remoteapp-upd.md)에서 자세히 알아보세요.

이미지에 상주할 수 없는 데이터 예:

- 사용자가 액세스할 수 있는 공유 데이터
- SQL DB 또는 QuickBooks DB
- D:\\의 모든 데이터

모든 사용자의 UPD에 복사되는 기본 프로필에 상주할 수 있는 데이터 예:

- 사용자별 구성 파일
- 사용자가 자신의 UPD에 보존해야 하는 스크립트

핵심 내용:

- 사용자 지정 이미지를 만들 때 이미지에 손실될 수 있는 중요 이미지를 저장하지 않습니다.
- 중요한 데이터는 항상 별도의 파일 서버, 클라우드의 별도 Azure VM에 상주해야 하며 항상 Azure RemoteApp에서 응용 프로그램을 호스팅하는 VM 인스턴스 외부에 있어야 합니다. 
- 사용자 데이터는 사용자 프로필 디스크(UPD)에 저장되어 유지됩니다.

<!---HONumber=AcomDC_0427_2016-->