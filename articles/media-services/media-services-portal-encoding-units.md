<properties
	pageTitle="Azure 클래식 포털을 사용하여 미디어 처리를 확장하는 방법"
	description="계정에서 프로비전할 주문형 스트리밍 예약 단위 및 인코딩 예약 단위의 수를 지정하여 미디어 서비스를 확장하는 방법에 대해 알아봅니다."
	services="media-services"
	documentationCenter=""
	authors="juliako,milangada"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2016"
	ms.author="juliako"/>


# Azure 클래식 포털을 사용하여 미디어 처리를 확장하는 방법

## 다른 기술과 함께 이 작업 수행

이 페이지에서는 미디어 처리를 확장하는 방법의 개요를 제공하며 Azure 클래식 포털을 사용하여 이 작업을 수행하는 방법을 보여 줍니다. 또한 다른 기술과 함께 이 작업을 수행합니다.

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [포털](media-services-portal-encoding-units.md)
- [REST (영문)](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## 개요

미디어 서비스 계정은 미디어 처리 작업을 처리하는 속도를 결정하는 예약 단위 유형과 연결됩니다. **S1**, **S2**, **S3** 예약 단위 유형 중에서 선택할 수 있습니다. 예를 들어 **S2** 예약 단위 유형을 사용하는 경우 **S1** 유형에 비해 동일한 인코딩 작업이 더 빠르게 실행됩니다. 자세한 내용은 [예약 단위 유형](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)을 참조하세요.

예약 단위 유형을 지정하는 것 외에도 계정에 예약 단위를 프로비전하도록 지정할 수 있습니다. 프로비전되는 예약 단위의 수에 따라 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수가 결정됩니다. 예를 들어 계정에 5개의 예약 단위가 있는 경우 처리할 미디어 작업이 있다면 5개의 미디어 작업이 동시에 실행됩니다. 나머지 작업은 큐에 대기하다가 실행 중인 작업이 완료되자마자 순차적으로 처리를 위해 선택됩니다. 계정에 프로비전된 예약 단위가 없는 경우에는 작업이 순차적으로 선택됩니다. 이 경우 한 작업 완료와 다음 작업 시작 사이의 대기 시간은 시스템의 리소스 가용성에 따라 다릅니다.

## 여러 예약 단위 유형 중에서 선택

다음 테이블을 참조하여 여러 인코딩 속도 중에서 선택할 때 적절한 속도를 결정할 수 있습니다. 이 테이블에서는 테스트를 직접 수행할 수 있도록 비디오를 다운로드하는 데 사용할 수 있는 SAS URL와 몇 가지 벤치마킹 사례도 제공합니다.

|**S1**|**S2**|**S3**|
----------|------------|----------|------------
대상 사용 사례| 단일 비트 전송률 인코딩 <br/>SD 이하 해상도의 파일, 시간이 중요하지 않은 인코딩, 저가형 비디오|단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>SD 및 HD 인코딩에서 모두 일반적으로 사용됨 |단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>풀 HD 및 4K 해상도 비디오 시간이 중요하며 소요 시간이 짧은 인코딩 
벤치마크|[입력 파일: 길이 5분/640x360p/초당 29.97프레임](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>같은 해상도에서 단일 비트 전송률 MP4 파일로 인코딩할 때 약 11분이 걸립니다.|[입력 파일: 길이 5분/1280x720p/초당 29.97프레임](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>"H264 단일 비트 전송률 720p" 기본 설정을 사용하여 인코딩할 때 약 5분이 걸립니다.<br/><br/>"H264 다중 비트 전송률 720p" 기본 설정을 사용하여 인코딩할 때 약 11.5분이 걸립니다.|[입력 파일: 길이 5분/1920x1080p/초당 29.97프레임](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D) <br/><br/>"H264 단일 비트 전송률 1080p" 기본 설정을 사용하여 인코딩할 때 약 2.7분이 걸립니다.<br/><br/>"H264 다중 비트 전송률 1080p" 기본 설정을 사용하여 인코딩할 때 약 5.7분이 걸립니다.

##고려 사항

>[AZURE.IMPORTANT] 고려 사항은 다음과 같습니다.

- 예약된 단위는 Azure 미디어 인덱서를 사용하는 인덱싱 작업을 비롯하여 모든 미디어 처리 병렬화에 대해 작동합니다. 그러나 인코딩과 달리 인덱싱 작업은 보다 빠른 예약 단위를 보다 빠르게 처리하지 않습니다.

- 예약 단위가 없는 공유 풀을 사용하는 경우의 인코딩 작업 성능은 S1 RU와 같습니다. 그러나 작업이 대기된 상태로 유지될 수 있는 시간의 상한은 없으며 언제든지 작업은 최대 하나만 실행됩니다.

- 브라질 남부, 인도 서부, 인도 중부, 인도 남부 데이터 센터는 **S2** 예약 단위 형식을 제공하지 않습니다.

- 브라질 남부, 인도 서부, 인도 중부 데이터 센터는 **S3** 예약 단위 형식을 제공하지 않습니다.

- 24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다.

## 예약 단위 유형 변경

예약 단위 유형 및 예약 단위 수를 변경하려면 다음을 수행합니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.

2. **ENCODING** 페이지를 선택합니다.

	**예약 단위 형식**을 변경하려면 S1, S2 또는 S3을 누릅니다.

	선택한 예약 단위 유형에 대한 예약 단위 수를 변경하려면 **인코딩** 슬라이더를 사용합니다.


	![프로세서 페이지](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

3. 저장 단추를 눌러 변경 내용을 저장합니다.

	저장을 누르는 즉시 새 예약 단위가 할당됩니다.
 

##할당량 및 제한 사항

할당량 및 제한 사항과 지원 티켓을 여는 방법에 대한 자세한 내용은 [할당량 및 제한 사항](media-services-quotas-and-limitations.md)을 참조하세요.



##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0727_2016-->