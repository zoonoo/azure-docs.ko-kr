<properties 
	pageTitle="질문과 대답" 
	description="FAQ(질문과 대답)" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/15/2015" 
	ms.author="juliako"/>


# 질문과 대답  

## 개요

Q: 인덱싱을 확장하려면 어떻게 하나요?

A: 예약 단위는 인코딩 및 인덱싱 태스크에서 동일합니다. [인코딩 예약 단위 크기를 조정하는 방법](media-services-how-to-scale.md) 의 지침에 따르세요. **참고**: 인덱서 성능은 예약 단위 유형의 영향을 받지 않습니다.

Q: 업로드, 인코딩 및 비디오를 게시합니다. 스트리밍하려고 할 때 어떤 이유로 비디오가 재생되지 않는 걸까요? 

A: 가장 일반적인 이유 중 하나는 재생하려고 하는 스트리밍 끝점에 할당된 예약된 스트리밍 단위가 없기 때문입니다.  [인코딩 스트리밍 예약 단위 크기를 조정하는 방법](media-services-how-to-scale.md) 의 지침을 따릅니다.

Q: 라이브 스트림에서 합치기를 수행할 수 있나요? 

A: 라이브 스트림 합치기는 현재 Azure 미디어 서비스에서 제공되지 않으므로, 컴퓨터에 미리 합치기를 수행해야 합니다.

Q: 라이브 스트리밍으로 Azure CDN을 사용할 수 있나요? 

A: 미디어 서비스는 Azure CDN와의 통합을 지원합니다(자세한 내용은 [미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법](media-services-manage-origins.md#enable_cdn)을 참조하세요).  CDN을 사용하여 라이브 스트리밍을 사용할 수 있습니다. Azure 미디어 서비스는 부드러운 스트리밍, HLS 및 MPEG-DASH 출력을 제공합니다. 이러한 형식에서는 HTTP를 데이터 전송에 사용하여 HTTP 캐싱의 이점을 얻을 수 있습니다. 라이브 스트리밍에서 실제 비디오/오디오 데이터가 조각으로 분할되고 이 개별 조각은 CDN에 캐시됩니다. 새로 고쳐야 하는 데이터 요구는 매니페스트 데이터입니다. CDN은 매니페스트 데이터를 주기적으로 새로 고칩니다.

Q: Azure 미디어 서비스는 저장된 이미지를 지원하나요?

A: JPEG 또는 PNG 이미지를 저장하기 위해 찾으려는 경우 Azure Blob 저장소에 유지해야 합니다. 비디오 또는 오디오 자산와 연결된 상태를 유지하려는 경우가 아니면 미디어 서비스 계정에 넣어도 이점은 없습니다. 또는 비디오 인코더에서 오버레이로 이미지를 사용해야 할 수 있습니다. 미디어 서비스 인코더는 비디오 위에서 겹치기 이미지를 지원하며 JPEG 및 PNG를 지원 입력 형식으로 나열합니다. 자세한 내용은 [오버레이 만들기](https://msdn.microsoft.com/library/azure/dn640496.aspx)를 참조하세요.

<!--HONumber=52-->