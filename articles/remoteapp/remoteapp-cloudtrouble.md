
<properties 
    pageTitle="클라우드 컬렉션 - 만들기 문제 해결"
    description="RemoteApp 클라우드 컬렉션 만들기 오류 문제를 해결하는 방법을 알아봅니다." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="vikbucha" />



# 클라우드 컬렉션 - 만들기 문제 해결

Azure 관리 포털에 표시되는 일반적인 오류:

	DNS server could not be reached
	ProvisioningTimeout

사용자 지정 이미지를 사용하기 때문에 만들기 중 클라우드 컬렉션이 자주 실패합니다.  위의 오류 중 하나를 참조하고 사용자 지정 이미지를 사용하여 컬렉션을 만드는 경우, 다음과 같은 작업을 확인하십시오.

- 업로드한 사용자 지정 이미지가 이미지 요구 사항을 만족하는지 확인합니다. 
- 가장 자주 발생하는 일반적인 문제는 이미지가 제대로 sysprep되지 않은 것입니다.
- 이미지를 Hyper-v 내에서 부팅할 수 있는지 확인하고 이미지를 사용하여 Azure 구독에서 직접 IAAS VM을 만들어 봅니다. VM을 부팅하고 시작할 수 없는 경우, 다음은 일반적으로 사용자 지정 이미지가 올바르게 준비되지 않았음을 나타냅니다.  RemoteApp에 대한 사용자 지정 템플릿 이미지를 만드는 방법에 따라 사용자 지정 이미지가 빌드되었는지 확인합니다.

구독에 포함 된 Microsoft 이미지 중 하나를 사용하는 경우, 컬렉션을 다시 만들어 봅니다. 문제가 지속되면 다음 Microsoft 지원에 문의하세요.

	PlatformImageTrialModeOnly

이 오류가 발생한 경우, 일반적으로 유료 계정으로 업그레이드되었음을 의미하지만 서비스의 평가 모드 중에만 유효한 Microsoft 제공 이미지를 사용하려고 함을 의미합니다. 이 경우, 다시 클라우드 컬렉션을 만들어 보지만 올바른 이미지를 지정해야 합니다.


<!--HONumber=52--> 