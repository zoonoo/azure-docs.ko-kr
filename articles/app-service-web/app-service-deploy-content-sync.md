<properties
	pageTitle="클라우드 폴더에서 Azure 앱 서비스로 콘텐츠 동기화"
	description="클라우드 폴더에서 콘텐츠 동기화를 통해 Azure 앱 서비스에 앱을 배포하는 방법에 대해 알아봅니다."
	services="app-service"
	documentationCenter=""
	authors="dariac"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="dariac"/>
    
# 클라우드 폴더에서 Azure 앱 서비스로 콘텐츠 동기화

[Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포하기 위한 옵션 중 하나는 Dropbox 및 OneDrive와 같은 인기 있는 클라우드 저장소 서비스에서 콘텐츠를 동기화하는 것입니다.

## <a name="overview"></a>콘텐츠 동기화 배포 개요

주문형 콘텐츠 동기화 배포는 앱 서비스와 통합된 [Kudu 배포 엔진](https://github.com/projectkudu/kudu/wiki)에서 제공됩니다. [Azure 포털](https://portal.azure.com)에서 클라우드 저장소의 폴더를 지정하고 해당 폴더에서 앱 코드 및 콘텐츠 작업을 수행한 다음 단추를 클릭하여 앱 서비스를 동기화할 수 있습니다. 콘텐츠 동기화는 빌드 및 배포에 Kudu 프로세스를 활용합니다.
    
## <a name="contentsync"></a>콘텐츠 동기화 배포를 사용하는 방법
[Azure 포털](https://portal.azure.com)에서 콘텐츠 동기화를 사용하려면 다음 단계를 따릅니다.

1. Azure 포털의 앱 블레이드에서 **설정 ** > **배포 원본**을 클릭합니다. **원본 선택**을 클릭한 다음 배포 원본으로 **OneDrive** 또는 **Dropbox**를 선택합니다. 

    ![콘텐츠 동기화](./media/app-service-deploy-content-sync/deployment_source.png)

2. 모든 앱 서비스 콘텐츠가 저장될 OneDrive 또는 Dropbox에 대해 미리 정의된 특정 지정 경로에 액세스하도록 앱 서비스를 설정하여 권한 부여 워크플로를 완료합니다. 권한 부여 후에 앱 서비스 플랫폼은 지정된 콘텐츠 경로에 콘텐츠 폴더를 만드는 옵션 또는 이 지정된 콘텐츠 경로에서 기존 콘텐츠 폴더를 선택하는 옵션을 제공합니다. 앱 서비스 동기화에 사용된 클라우드 저장소 계정에 지정된 콘텐츠 경로는 다음과 같습니다.
    * **OneDrive**: `Apps\Azure Web Apps` 
    * **Dropbox**: `Dropbox\Apps\Azure`

3. 초기 콘텐츠 동기화 후 Azure 포털에서 필요에 따라 콘텐츠 동기화를 시작할 수 있습니다. 배포 기록은 **배포** 블레이드에서 확인할 수 있습니다.

    ![배포 기록](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Dropbox 배포에 대한 자세한 내용은 [Dropbox에서 배포](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)에서 제공됩니다.

<!---HONumber=AcomDC_0323_2016-->