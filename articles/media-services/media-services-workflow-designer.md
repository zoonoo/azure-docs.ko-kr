<properties 
	pageTitle="워크플로 디자이너와 고급 인코딩 워크플로 만들기" 
	description="워크플로 디자이너와 고급 인코딩 워크플로를 만드는 방법에 대해 알아봅니다." 
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
	ms.date="07/20/2015" 
	ms.author="juliako"/>


#워크플로 디자이너와 고급 인코딩 워크플로 만들기

##개요
**Workflow Designer**는 **미디어 인코더 Premium 워크플로**를 사용하여 인코딩할 워크플로/그래프를 생성하는 데 사용되는 독립 실행형 도구입니다.

이 도구를 사용하여 [기존 워크플로](media-services-workflow-designer.md#existing_workflows)를 수정할 수도 있습니다.

>[AZURE.NOTE]Workflow Designer 도구의 복사본을 얻으려면 mepd@microsoft.com에 문의하세요.


워크플로 파일을 만든 후 이를 자산으로 업로드한 다음 미디어 파일을 인코딩하는 데 사용할 수 있습니다. **.NET**을 사용하여 **미디어 인코더 Premium 워크플로**에서 인코딩하는 방법에 대한 자세한 내용은 [미디어 인코더 Premium 워크플로 사용하여 고급 인코딩](media-services-encode-with-premium-workflow.md)을 참조하세요.

##<a id="existing_workflows"></a>기존 워크플로 수정

디자이너 도구를 사용하여 기본 워크플로 파일을 수정할 수 있습니다. [여기](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)에서 기본 워크플로 파일을 가져올 수 있습니다. 폴더에는 이러한 파일에 대한 설명도 포함되어 있습니다.

다음 비디오에서는 디자이너를 사용하는 방법을 보여줍니다.

###1일차 – 시작하기

1일차 비디오에서 다루는 내용은 다음과 같습니다.

- 디자이너 개요
- 기본 워크플로 – "Hello World"
- Azure 미디어 서비스 스트리밍에서 사용할 여러 출력 MP4 파일 만들기

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###2일차

2일차 비디오에서 다루는 내용은 다음과 같습니다.

- 다양한 소스 파일 시나리오 – 오디오 처리
- 고급 논리를 사용한 워크플로
- 그래프 단계

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###3일차

3일차 비디오에서 다루는 내용은 다음과 같습니다.

- 워크플로/청사진 내부 스크립팅
- 현재 인코더에 대한 제한 사항
- Q&A
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]

##참고 항목

[Azure Premium 인코더 워크플로 디자이너 교육 비디오](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<!---HONumber=August15_HO6-->