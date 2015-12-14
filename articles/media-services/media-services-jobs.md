<properties 
	pageTitle="Azure 미디어 서비스 작업 사용" 
	description="이 항목에서는 Azure 미디어 서비스 작업을 관리하는 방법에 대한 개요를 제공합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Azure 미디어 서비스 작업 사용

**작업**에는 수행할 처리에 대한 메타데이터가 포함됩니다. 각 **작업**에는 원자성 처리 태스크, 해당 입력 자산, 출력 자산, 미디어 프로세서 및 관련 설정을 지정하는 하나 이상의 **태스크**가 포함됩니다. 인코더 설정에 대한 자세한 내용은 인코더 가이드 및 인코더 스키마를 참조하세요.

인코딩 작업은 일반적으로 다른 처리(예: 패키징 또는 인코더에서 생성한 자산 암호화)와 결합됩니다. 작업 내의 태스크는 함께 연결할 수 있으며, 이때 한 태스크의 출력 자산은 다음 태스크의 입력 자산으로 제공됩니다. 이러한 방식으로 한 작업에는 미디어 프레젠테이션에 필요한 모든 처리가 포함될 수 있습니다.

이 섹션에서는 작업\\태스크를 사용할 때 수행해야 하는 일반적인 태스크에 대한 링크를 제공합니다.

>[AZURE.NOTE]현재 작업은 작업당 30개로 제한됩니다. 30개 이상의 작업을 연결해야 하는 경우 둘 이상의 작업을 만들어 작업을 연결합니다.


##미디어 프로세서 가져오기

**.NET** 또는 **REST API**를 사용하여 미디어 프로세서를 가져옵니다.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

##작업 만들기

작업은 태스크 집합(예: 인코딩 또는 인덱싱)에 대한 메타데이터를 포함하는 엔터티입니다. 각 태스크는 입력 자산에 대한 원자성 작업을 수행합니다. 인코딩 작업을 만드는 방법에 대한 예제는 다음을 참조하세요.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##인덱싱

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

##인코딩

**Azure 클래식 포털**, **.NET** 또는 **REST API**를 사용하여 **Azure 미디어 인코더**로 인코딩합니다.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##작업 진행 상태 모니터링

**Azure 클래식 포털**, **.NET** 또는 **REST API**를 사용하여 작업 진행 상태를 모니터링합니다.

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

##관련 링크

[할당량 및 제한 사항](media-services-quotas-and-limitations.md) – 미디어 서비스 인코더의 제한 사항 및 사용된 할당량에 대해 설명합니다.


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1203_2015-->