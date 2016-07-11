
<properties
    pageTitle="Azure RemoteApp과 함께 Office 사용 | Microsoft Azure" 
    description="Office 및 Azure RemoteApp 연동 방법에 대해 알아봅니다."
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
    ms.date="06/27/2016"
    ms.author="elizapo" />

# Azure RemoteApp과 함께 Office 사용

Azure RemoteApp에서 Office 응용 프로그램 호스팅을 위한 두 가지 옵션으로, Office 365 ProPlus 또는 Office 2013 Professional Plus 평가판이 있습니다.

**이 문서는 향상된 새 문서로 곧 대체될 것입니다. [Azure RemoteApp과 함께 Office 365 구독을 사용하는 방법](remoteapp-officesubscription.md)을 확인하세요. Office 365 + Azure RemoteApp을 사용하는 데 필요한 모든 정보를 제공합니다.**

## Office 365 ProPlus
Office 365 ProPlus 템플릿 이미지를 사용하여 RemoteApp 컬렉션을 만들 수 있습니다. 이 옵션을 사용하면 Office 365 서비스를 RemoteApp까지 확장할 수 있습니다. 기존 구독 계획이 있어야 하며, 독립 실행형이나 Office 365 서비스 계획을 통해 사용자에게 Office 365 ProPlus 서비스에 대한 라이선스가 있어야 합니다.

RemoteApp은 Office 365 공유 컴퓨터 정품 인증을 지원합니다. 공유 컴퓨터 정품 인증을 활성화하고 설치에 [Office 배포 도구](http://www.microsoft.com/download/details.aspx?id=36778)를 사용합니다. Office 365 ProPlus는 활성화 없이 설치합니다. 사용자가 Office 365를 포함하는 컬렉션에 로그인하면 Office는 해당 사용자가 Office 365 ProPlus에 프로비전되었는지 확인합니다. 프로비전된 경우 Office는 Office 365 ProPlus를 임시로 활성화합니다. 이 활성화는 해당 사용자가 서비스를 로그아웃할 때까지 지속됩니다.

Office 365 공유 컴퓨터 정품 인증을 사용하려면 [사용자 지정 템플릿](remoteapp-create-custom-image.md)을 만들고 [이 지침](https://technet.microsoft.com/library/dn782858.aspx)을 따라 Office 365 ProPlus를 만들어야 합니다.

[Office 365 관리 포털](https://portal.office365.com/)에서 사용자의 Office 365 라이선스를 관리할 수 있습니다. [Office 365 서비스 계획](http://technet.microsoft.com/library/office-365-plan-options.aspx)에 대한 자세한 내용을 읽어 보세요.


## Office 2013 Professional Plus 평가판
RemoteApp의 30일 평가 기간 동안 Office 2013 Professional Plus(평가판) 템플릿 이미지를 사용하여 RemoteApp 컬렉션을 만들 수 있습니다. Azure Active Directory 작업 계정이나 Microsoft 계정을 사용하여 이 평가판 컬렉션에 사용자를 할당할 수 있습니다. 추가 구독은 필요하지 않습니다.

이 옵션은 RemoteApp의 Office를 평가하고 파악하는 데 유용합니다. 그러나 이 옵션은 평가 및 테스트 용도로만 사용됩니다. Office 2013 Professional Plus(평가판) 템플릿 이미지를 사용하여 만든 RemoteApp 컬렉션은 프로덕션 모드로 전환할 수 없으며 평가 기간이 끝나면 사용할 수 없습니다.

## 평가판에서 프로덕션으로 전환
30일 무료 평가판을 시작하면 유료 계정으로 전환해야 하기까지 남은 기간을 알리는 메모가 포털의 RemoteApp 섹션에 표시됩니다. 이 메모의 링크를 사용하여 계정을 활성화하고 프로덕션 모드로 전환할 수 있습니다.

계정을 활성화하면 계정의 모든 RemoteApp 컬렉션에 영향을 줍니다.

- Windows Server 2012 R2 또는 Office 365 ProPlus 템플릿 이미지를 사용하여 실행되는 컬렉션이 프로덕션으로 매끄럽게 전환됩니다. 진행 중인 세션을 비롯한 모든 사용자 데이터와 설정은 원래대로 유지됩니다.
- 업로드된 사용자 지정 템플릿 이미지가 있는 경우 이러한 이미지를 사용하는 컬렉션도 매끄럽게 전환됩니다.
- Office 2013 Professional Plus(평가판) 템플릿 이미지는 평가 용도로만 사용됩니다. 이 템플릿 이미지를 사용하여 실행되는 컬렉션은 프로덕션으로 전환할 수 없습니다. "사용 안 함" 상태로 설정됩니다.


평가판이 만료될 때까지 프로덕션 모드로 전환하지 않으면 RemoteApp 컬렉션을 사용할 수 없습니다. 걱정하지 마세요. 설정과 사용자 데이터는 90일 동안 저장되므로 데이터 손실 없이 서비스를 활성화하고 프로덕션 모드로 전환할 수 있습니다.

<!---HONumber=AcomDC_0629_2016-->