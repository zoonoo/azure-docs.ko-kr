<properties
	pageTitle="Azure 앱 서비스에 대한 모범 사례"
	description="Azure 앱 서비스에 대한 모범 사례 및 문제 해결에 대해 알아봅니다."
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="dariagrigoriu"/>
    
# Azure 앱 서비스에 대한 모범 사례

이 문서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)를 사용하는 모범 사례를 요약합니다.

## <a name="colocation"></a>공동 배치
웹앱과 같은 솔루션을 작성하는 Azure 리소스와 데이터베이스가 다른 지역에 있는 경우 다음과 같은 영향이 있습니다.

*  리소스 간 통신에 대한 대기 시간 증가
*  [Azure 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-transfers)에서 설명한 것과 같이 지역 간 아웃바운드 데이터 전송에 대한 금전적인 청구

동일한 지역의 공동 배치는 웹앱과 같은 솔루션과 콘텐츠 또는 데이터를 저장하는 데 사용되는 데이터베이스 또는 저장소 계정을 작성하는 Azure 리소스에 가장 적합합니다. 리소스를 만들 때 동일한 Azure 지역에 있어서는 안 되는 특정 비즈니스 또는 디자인 사유가 없는 한 동일한 Azure 지역에 있어야 합니다. 프리미엄 앱 서비스 계획 앱에서 현재 사용할 수 있는 [앱 서비스 복제 기능](app-service-web-app-cloning-portal.md)을 활용하여 앱 서비스 앱을 데이터베이스와 동일한 지역으로 이동할 수 있습니다.

## <a name="memoryresources"></a>앱에서 예상보다 더 많은 메모리를 사용하는 경우
앱에서 모니터링 또는 서비스 권장 사항을 통해 표시된 대로 예상보다 더 많은 메모리를 사용하는 경우 [앱 서비스 자동 복구 기능](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)을 고려합니다. 자동 복구 기능에 대한 옵션 중 하나는 메모리 임계값을 기반으로 하는 사용자 지정 작업을 수행하는 것입니다. 작업은 작업자 프로세스를 재활용하여 자리 완화에 대한 메모리 덤프를 통해 전자 메일 알림에서 조사로 범위를 확장합니다. 자동 복구는 [앱 서비스 지원 사이트 확장](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)에 대한 이 블로그 게시물에 설명된 대로 web.config 및 친숙한 사용자 인터페이스를 통해 구성될 수 있습니다.

## <a name="CPUresources"></a>앱에서 예상보다 더 많은 CPU를 사용하는 경우
앱에서 모니터링 또는 서비스 권장 사항을 통해 표시된 대로 예상보다 더 많은 CPU를 사용하거나 반복되는 CPU 스파이크를 경험하는 경우 앱 서비스 계획 강화 또는 확장을 고려합니다. 응용 프로그램이 상태 저장인 경우 강화가 유일한 옵션인 반면 응용 프로그램이 상태 비저장인 경우 확장을 통해 더 많은 유연성 및 더 높은 확장 가능성을 줍니다.

"상태 저장" 및 "상태 비저장" 응용 프로그램 비교에 대한 자세한 내용은 [Microsoft Azure 웹앱에서 확장 가능한 종단 간 다중 계층 응용 프로그램 계획](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid) 비디오를 시청할 수 있습니다. 앱 서비스 크기 조정 및 자동 크기 조정 옵션에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 크기 조정](web-sites-scale.md)을 참조하세요.

## <a name="socketresources"></a>소켓 리소스를 모두 사용한 경우
아웃바운드 TCP 연결을 소모하는 일반적인 이유는 TCP 연결을 다시 사용하도록 구현되지 않는 클라이언트 라이브러리의 사용 또는 활용되지 않는 HTTP(활성 유지)와 같은 높은 수준의 프로토콜의 경우입니다. 효율적인 아웃바운드 재사용에 대한 코드에서 구성 또는 액세스할 수 있도록 앱 서비스 계획의 앱에서 참조하는 각 라이브러리에 대한 설명서를 검토하세요. 또한 연결 누수를 방지하도록 올바른 생성 및 릴리스 또는 정리에 대한 라이브러리 설명서 지침을 따릅니다. 이러한 클라이언트 라이브러리 조사 진행 중 여러 인스턴스로 확장하여 영향을 완화할 수 있습니다.

<!---HONumber=AcomDC_0525_2016-->