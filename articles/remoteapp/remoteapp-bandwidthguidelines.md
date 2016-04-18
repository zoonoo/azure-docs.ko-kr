<properties 
    pageTitle="Azure RemoteApp 네트워크 대역폭 - 일반적인 지침 | Microsoft Azure"
	description="Azure RemoteApp 컬렉션 및 앱에 대한 몇 가지 기본 네트워크 대역폭 지침을 알아봅니다."
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
    ms.date="03/31/2016" 
    ms.author="elizapo" />
    
# Azure RemoteApp 네트워크 대역폭 - 일반적인 지침(자체 테스트를 수행할 수 없는 경우)

Azure RemoteApp에 대한 [네트워크 대역폭 테스트](remoteapp-bandwidthtests.md)를 실행할 시간 또는 능력이 없는 경우 사용자당 네트워크 대역폭을 예측하는 데 도움이 될 수 있는 일반적인 지침이 있습니다.

이러한 시나리오가 혼합된 경우에는 원격 환경의 최신 인터넷 연결 앱에 대해 10MB/s 이하는 최소 네트워크 대역폭으로 권장되지 않습니다. 이는 평균 사용자 환경보다 더 나은 환경을 보장하지 않습니다.

## 복잡한 PowerPoint, 단순한 PowerPoint

Azure RemoteApp은 100MB LAN에서 가장 효율적으로 작동합니다. 10MB/s 네트워크 프로필에서 120밀리초가 넘는 지터가 5%를 초과하는 경우 평균적인 사용자 환경이 제공됩니다. 1MB/s에서는 다릅니다. 예를 들어 슬라이드 쇼에서 프레임을 건너뛰어 애니메이션된 전환이 보이지 않을 수 있습니다.

## Internet Explorer, 혼합된 PDF, PDF, 텍스트

10MB/s 네트워크 프로필은 대부분의 측면에서 LAN에 가깝습니다. 1MB/s는 화면에 이미지가 있는 경우 사용자가 스크롤할 때 일부 지터가 발생할 수 있지만 정상적인 환경을 제공합니다.

## Flash 비디오(YouTube)

100MB/s LAN은 최상의 환경을 제공하고, 10MB/s는 허용되는 환경(즉, 프레임 속도를 유지하지만 지터가 증가함)을 제공합니다. 1MB/s에서는 지터가 매우 높습니다.

## Word 입력(Word 원격 입력)
낮은 대역폭을 사용하는 시나리오입니다. 256KB/s에서 LAN만큼 적절한 환경을 제공합니다.

## 자세한 정보
- [Azure RemoteApp 네트워크 대역폭 사용량 예측](remoteapp-bandwidth.md)

- [Azure RemoteApp - 네트워크 대역폭과 환경 품질을 함께 작동하는 방식은 무엇인가요?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp - 몇 가지 일반적인 시나리오로 네트워크 대역폭 사용량 테스트](remoteapp-bandwidthtests.md)

<!---HONumber=AcomDC_0406_2016-->