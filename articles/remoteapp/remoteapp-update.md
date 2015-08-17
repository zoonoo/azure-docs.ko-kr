<properties
   pageTitle="Azure RemoteApp 컬렉션 업데이트"
   description="Azure RemoteApp 컬렉션 업데이트 방법"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="06/09/2015"
   ms.author="elizapo"/>

# Azure RemoteApp에서 컬렉션 업데이트

때로 Azure RemoteApp 컬렉션의 앱이나 이미지를 업데이트해야 하는 경우가 있습니다. 클라우드든 하이브리드 컬렉션이든 Azure RemoteApp 구독에 포함된 이미지 중 하나를 사용한다면 모든 업데이트는 Azure RemoteApp 자체에서 처리되므로 신경 쓸 필요가 없습니다.

하지만 처음부터 새로 만들었거나 기존 이미지를 수정하여 만든 사용자 지정 이미지를 사용할 경우, 이미지와 앱을 유지 관리하는 것은 사용자의 책임입니다.

그렇다면 컬렉션은 어떻게 업데이트할 수 있을까요? 방법은 상당히 간단합니다.

1. 컬렉션에 사용되는 이미지를 업데이트합니다. 필요한 모든 패치 또는 업데이트를 적용하고 새 이름으로 저장합니다.
2. 이 이미지를 RemoteApp으로 [업로드](remoteapp-uploadimage.md) 또는 [가져오기](remoteapp-image-on-azurevm)합니다.
3. 이제 컬렉션 페이지에서 **업데이트**를 클릭합니다.
4. **템플릿 이미지** 목록에서 새 이미지를 선택합니다.
4. 여기서 신경 쓸 부분이 하나 있습니다. 컬렉션의 앱을 현재 사용하고 있는 사용자를 어떻게 처리할까 하는 부분을 결정해야 합니다. 다음 옵션이 있습니다.
	- **업데이트 후 사용자에게 60분의 시간 제공**. 업데이트가 완료되면 Azure RemoteApp에서 현재 앱을 사용 중인 사용자에게 작업을 저장하고 로그오프 했다가 다시 로그인하라는 메시지를 표시합니다. 60분이 지난 후에도 로그오프하지 않은 모든 사용자는 자동으로 로그오프됩니다. 사용자는 즉시 다시 로그인할 수 있습니다. 
	- **사용자를 즉시 로그아웃**. 업데이트가 완료되면 경고 없이 모든 사용자를 자동으로 로그오프합니다. 이 옵션을 선택하면 사용자가 데이터를 잃을 수 있습니다. 그러나 즉시 앱에 다시 연결하는 것은 가능합니다.

1. 확인 표시를 클릭하여 업데이트를 시작합니다.

 

<!---HONumber=August15_HO6-->