
<properties 
    pageTitle="하이브리드 컬렉션 - 만들기 문제 해결"
    description="RemoteApp 하이브리드 컬렉션 만들기 오류 문제를 해결하는 방법을 알아봅니다." 
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



# 하이브리드 컬렉션 - 만들기 문제 해결

하이브리드 컬렉션을 만드는 동안 오류를 해결하기 전에 하이브리드 컬렉션이 만들어지는 방법을 이해하는 것이 도움이 됩니다. 하이브리드 컬렉션을 사용하려면 RemoteApp 인스턴스가 도메인에 가입되어야 합니다. - 컬렉션을 만드는 동안 이 작업을 수행해야 합니다.  컬렉션 만들기 프로세스가 시작되면 업로드한 템플릿 이미지의 복사본에 VNET가 만들어지며 NET를 만들 때 지정한 DNS IP 레코드에서 확인되는 도메인에 대한 사이트-VPN 터널을 통해 도메인에 가입됩니다.

Azure 관리 포털에 표시되는 일반적인 오류:

	DNS server could not be reached

	Could not join the domain. Unable to reach the domain.

위의 오류 중 하나가 표시되면 다음 작업을 확인합니다.

- DNS IP 구성이 유효한 지 확인
- DNS IP 레코드가 공용 IP 레코드이거나 VNET 만들 때 지정한 "로컬 주소 공간"의 일부인지 확인
- 활성 또는 연결된 상태인지 확인하려면 VNET 터널 확인 
- VPN 연결의 온-프레미스 측이 네트워크 트래픽을 차단하지 않는지 확인합니다. 로컬 VPN 장치 또는 소프트웨어의 로그를 확인하여 있는지를 확인할 수 있습니다.
- 컬렉션을 만들 때 지정한 도메인을 준비하고 실행을 확인합니다.

	ProvisioningTimeout


이 오류가 표시되면 다음 작업을 확인하십시오.

- VPN 연결의 온-프레미스 측이 네트워크 트래픽을 차단하지 않는지 확인합니다. 로컬 VPN 장치 또는 소프트웨어의 로그를 확인하여 있는지를 확인할 수 있습니다.
- 업로드한 RemoteApp 템플릿 이미지가 제대로 sysprep되었는지 확인합니다. 여기에서 RemoteApp 이미지 요구 사항을 확인할 수 있습니다.: http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/ 
- 업로드한 템플릿 이미지를 사용하여 VM을 만들고 (a) 로컬 Hyper-v 서버에서 (b)Azure 구독에 Azure IAAS VM을 만들어서 부팅되고 실행되는지 확인합니다. VM을 생성하지 못하거나 시작할 수 없는 경우, 이 오류는 일반적으로 템플릿 이미지가 올바르게 준비되지 않아 수정해야 함을 표시합니다.

	DomainJoinFailed_InvalidUserNameOrPassword

	DomainJoinFailed_InvalidParameter

위의 오류 중 하나가 표시되면 다음 작업을 확인합니다.

- 도메인 가입을 위해 입력한 자격 증명이 유효한 지 확인
- 도메인 가입 자격 증명이 올바르거나 적절한 도메인 가입 권한이 있는지 확인
- 조직 단위(OU)가 올바르게 포맷되었는지 확인하고 Active directory에서 존재를 확인합니다.

<!--HONumber=52-->