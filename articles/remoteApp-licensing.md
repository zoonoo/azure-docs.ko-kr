<properties title="Azure RemoteApp licensing" pageTitle="Azure RemoteApp 라이선스" description="Azure RemoteApp 라이선스에 대 한 학습" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Azure RemoteApp의 라이선스 작동 방식


이제 Azure RemoteApp 서비스를 설정하고 템플릿을 만들었으며 사용자에게 앱을 게시할 준비가 되었습니다. 하지만 마지막으로 확인해야 할 사항이 있습니다. 바로 라이선스입니다. RemoteApp을 통해 공유하는 앱의 라이선스 처리 방식

라이선스를 가진 모든 앱이나 프로그램을 제공할 수 있습니다. 

Azure RemoteApp을 구독할 경우 Windows 라이선스나 원격 데스크톱 CAL이 모두 구독 비용에 포함되어 있으므로 별도로 구입할 필요가 없습니다.

Azure RemoteApp과 함께 제공된 Office 365 템플릿 이미지를 사용하려면 *기존* Office 365 ProPlus 계획이 있어야 합니다. 사용자 지정 템플릿을 사용하여 게시하는 Office 365 앱의 경우도 마찬가지입니다. 소유한 구독으로 앱을 활성화해야 합니다. 이 내용은 평가판 및 유료 구독 둘 다에 적용됩니다. 평가판 사용 중 Office 365 템플릿 이미지를 사용하려는 경우 *구독이 아직 없으면* Office 365 페이지로 이동하여 평가판 구독을 [등록](https://go.microsoft.com/fwlink/p/?LinkID=403802)하세요. 자세한 내용은 [RemoteApp과 Office가 함께 작동하는 방식](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-o365/)을 참조하세요.

평가 기간 중 Office 365 평가판 구독을 가져오지 않으려면 RemoteApp과 함께 제공된 Office 2013 Professional Plus 템플릿 이미지를 사용하세요. 이 템플릿 이미지는 30일 동안만 사용할 수 있으며 유료 컬렉션으로 변환할 수 없습니다. 평가 기간이 종료된 후 Office 2013을 계속 사용하려면 컬렉션을 삭제하고 새로 만들어야 합니다.  

참 합리적이지 않은가요? 법적으로 공유할 자격이 있는 모든 앱을 게시할 수 있습니다. 또한 프로그램을 공유할 자격이 있는지 확인해야 합니다.

클라우드 컬렉션의 CAL 또는 볼륨 라이선스 계약은 사용할 수 없습니다. 볼륨 라이선스 계약을 사용하여 하이브리드 컬렉션의 응용 프로그램을 활성화할 수 *있습니다*(Office 제외). 볼륨 라이선스 미디어에서 해당 템플릿 이미지에 설치하면 됩니다. 응용 프로그램 공급업체의 정보에 따라 원격 데스크톱 환경에 라이선스를 설치하세요.

<!--HONumber=35.2-->
