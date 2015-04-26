<properties title="Using Office365 with Azure RemoteApp" pageTitle="Azure RemoteApp과 함께 Office365 사용" description="Office 365 및 Azure RemoteApp 연동 방법을 알아봅니다." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Azure RemoteApp과 함께 Office365 사용

RemoteApp에서 Office 응용 프로그램을 호스트하는 두 가지 옵션(Office 365 ProPlus 또는 Office 2013 Professional Plus 평가판) 중에서 선택할 수 있습니다.

##Office 365 ProPlus 
Office 365 ProPlus 템플릿 이미지를 사용하여 RemoteApp 컬렉션을 만들 수 있습니다. 이 옵션을 사용하면 Office 365 서비스를 RemoteApp까지 확장할 수 있습니다. 기존 구독 계획이 있어야 하며, 독립 실행형이나 Office 365 서비스 계획을 통해 사용자에게 Office 365 ProPlus 서비스에 대한 라이선스가 있어야 합니다. Office 앱의 RemoteApp 인스턴스는 각 사용자에게 허용되는 5개 설치 인스턴스 중 하나로 계산되며 완전히 활성화됩니다. [Office 365 관리 포털](https://portal.office365.com/)에서 사용자의 Office 365 라이선스를 관리할 수 있습니다. [Office 365 서비스 계획](http://technet.microsoft.com/library/office-365-plan-options.aspx)에 대한 자세한 내용을 읽어 보세요.  

Office 365 ProPlus 옵션은 30일 무료 평가 기간 동안 및 프로덕션 모드에서 모두 사용할 수 있지만 평가가 만료된 후에는 지원되는 유일한 옵션입니다.  

Office 365 ProPlus가 포함된 사용자 지정 템플릿 이미지를 만들 수도 있습니다. 이러한 템플릿 이미지를 생성하려면 RDS에서 Office 365 ProPlus에 대한 [배포 단계](http://technet.microsoft.com/ko-kr/library/dn782858(v=office.15).aspx)를 따르세요.

##Office 2013 Professional Plus 평가판 
RemoteApp의 30일 평가 기간 동안 Office 2013 Professional Plus(평가판) 템플릿 이미지를 사용하여 RemoteApp 컬렉션을 만들 수 있습니다. Azure Active Directory 작업 계정이나 Microsoft 계정을 사용하여 이 평가판 컬렉션에 사용자를 할당할 수 있습니다. 추가 구독은 필요하지 않습니다.

이 옵션은 RemoteApp의 Office를 평가하고 파악하는 데 유용합니다. 그러나 이 옵션은 평가 및 테스트 용도로만 사용됩니다. Office 2013 Professional Plus(평가판) 템플릿 이미지를 사용하여 만든 RemoteApp 컬렉션은 프로덕션 모드로 전환할 수 없으며 평가 기간이 끝나면 사용할 수 없습니다.

##평가판에서 프로덕션으로 전환
30일 무료 평가판을 시작하면 유료 계정으로 전환해야 하기까지 남은 기간을 알리는 메모가 포털의 RemoteApp 섹션에 표시됩니다. 이 메모의 링크를 사용하여 계정을 활성화하고 프로덕션 모드로 전환할 수 있습니다. 

계정을 활성화하면 계정의 모든 RemoteApp 컬렉션에 영향을 줍니다. 

- Windows Server 2012 R2 또는 Office 365 ProPlus 템플릿 이미지를 사용하여 실행되는 컬렉션이 프로덕션으로 매끄럽게 전환됩니다. 진행 중인 세션을 비롯한 모든 사용자 데이터와 설정은 원래대로 유지됩니다.
- 업로드된 사용자 지정 템플릿 이미지가 있는 경우 이러한 이미지를 사용하는 컬렉션도 매끄럽게 전환됩니다.
- Office 2013 Professional Plus(평가판) 템플릿 이미지는 평가 용도로만 사용됩니다. 이 템플릿 이미지를 사용하여 실행되는 컬렉션은 프로덕션으로 전환할 수 없습니다. "사용 안 함" 상태로 설정됩니다.


평가판이 만료될 때까지 프로덕션 모드로 전환하지 않으면 RemoteApp 컬렉션을 사용할 수 없습니다. 걱정하지 마세요. 설정과 사용자 데이터는 90일 동안 저장되므로 데이터 손실 없이 서비스를 활성화하고 프로덕션 모드로 전환할 수 있습니다.

<!--HONumber=35.2-->
