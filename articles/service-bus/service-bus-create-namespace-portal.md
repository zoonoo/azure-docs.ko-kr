<properties
   pageTitle="Azure 포털을 사용하여 서비스 버스 네임스페이스 만들기 | Microsoft Azure"
   description="서비스 버스를 시작하려면 네임스페이스가 필요합니다. Azure 포털을 사용하여 만드는 방법은 다음과 같습니다."
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Azure 포털을 사용하여 서비스 버스 네임스페이스 만들기
네임스페이스는 모든 메시징 구성 요소에 대한 공용 컨테이너입니다. 여러 큐 및 토픽은 단일 네임스페이스에 있을 수 있으며 네임스페이스는 종종 응용 프로그램 컨테이너로 사용됩니다. 현재 두 가지 다른 방법으로 서비스 버스 네임스페이스를 만들 수 있습니다.

1.	Azure 포털(이 문서)

2.	[ARM 템플릿][create-namespace-using-arm]

##Azure 포털에서 네임스페이스 만들기
[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

축하합니다. 이제 서비스 버스 네임스페이스를 만들었습니다.

##다음 단계

Azure 서비스 버스 메시징의 고급 기능 중 일부를 보여 주는 샘플로 GitHub 리포지토리를 확인하세요. [https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples]

[create-namespace-using-arm]: ./service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->