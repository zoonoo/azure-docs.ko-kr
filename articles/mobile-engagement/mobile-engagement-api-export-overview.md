---
title: Mobile Engagement 내보내기 API 개요
description: 사용자 고유의 도구에서 활용하기 위해 사용자의 장치에서 생성된 원시 데이터 내보내기에 대한 기본 사항을 알아봅니다.
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: ''
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: c1dc8a34abf84be3d7f59d41c36d90c2a67e5d32
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="mobile-engagement-export-api-overview"></a>Mobile Engagement 내보내기 API 개요
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

## <a name="introduction"></a>소개
이 문서에서는 사용자 고유의 도구에서 활용하기 위해 사용자의 장치에서 생성된 원시 데이터 내보내기에 대한 기본 사항을 알아봅니다.

## <a name="pre-requisites"></a>필수 조건
Mobile Engagement에서 원시 데이터를 내보내려면 다음이 필요합니다.

* API를 사용하려면 API 인증 설정( [인증 수동 설정](mobile-engagement-api-authentication-manual.md)참조),
* REST API 또는 [.net SDK](mobile-engagement-dotnet-sdk-service-api.md)사용,
* Azure Storage 계정.

> [!NOTE]
> Azure Storage와 상호 작용하기 위한 손쉬운 UI 사용을 제공하므로 개발 단계 중 유용한 [Microsoft Azure Storage 탐색기](http://storageexplorer.com/)를 권장합니다.
> 
> 

## <a name="what-can-be-exported"></a>무엇을 내보낼 수 있습니까?
Mobile Engagement를 통해 사용자는 다양한 유형의 데이터를 수집하고 이러한 여러 유형의 데이터에 적합한 다양한 내보내기 유형을 가질 수 있습니다.
두 가지 필수 내보내기 유형이 있습니다.

* 스냅숏: 상태 및 기록이 없는 Mobile Engagement를 나타내는 데이터를 내보내는 데 일반적으로 사용됩니다. 예를 들어 토큰(앱 정보), 토큰 또는 푸시 캠페인 피드백을 포함합니다. 결과적으로 이러한 유형의 내보내기는 날짜와 관련이 없습니다.
* 기록: 이러한 내보내기 유형은 예를 들어 이벤트 또는 작업과 같은 시간에 따라 누적되는 데이터에 사용됩니다.

다음 표는 모든 가능한 내보내기를 자세히 설명합니다.

| 내보내기 형식 | 데이터 형식 | 설명 |
| --- | --- | --- |
| 스냅숏 |푸시(Push) |장치 ID/사용자 ID 단위별 푸시 캠페인 피드백의 내보내기 생성 |
| 스냅숏 |태그 |각 장치에 연결된 태그(앱 정보)의 내보내기 생성 |
| 스냅숏 |장치 |기술(모델, 로캘, 표준 시간대, ...), 태그, 처음 본 시간 등과 같은 장치에 대한 대부분의 데이터의 내보내기 생성 |
| 스냅숏 |신뢰 |모든 유효한 토큰의 내보내기 생성 |
| 기록 |작업 |지정된 기간 동안 각 장치에 대한 모든 작업의 내보내기 생성 |
| 기록 |행사 |지정된 기간 동안 각 장치에 대한 모든 작업의 내보내기 생성 |
| 기록 |작업 |지정된 기간 동안 각 장치에 대한 모든 작업의 내보내기 생성 |
| 기록 |오류 |지정된 기간 동안 각 장치에 대한 모든 오류의 내보내기 생성 |

## <a name="how-does-it-work"></a>작동 원리
내보내기는 대용량 데이터 파일을 생성할 수 있는 장기 실행 작업입니다. 이런 이유로 다운로드할 파일을 즉시 반환하도록 호출될 수 없습니다.
Mobile Engagement에서 데이터를 내보내려면 일반적으로 다음을 지정한 API를 통해 **내보내기 작업** 을 만들어야 합니다.

* 내보내기 형식(스냅숏 또는 기록),
* 데이터 형식,
* 내보내기의 결과가 작성될 **Azure Storage 컨테이너** (쓰기 액세스가 있는 유효한 SAS 포함).
* 예를 들어 예제 컨테이너 URL 매개 변수는 https://[StorageAccountName].blob.core.windows.net/[ContainerName]?[SASWritePermissionsToken]이 됩니다.  

다음은 실제 사례입니다. https://testazmeexport.blob.core.windows.net/test1234azme?sv=2015-12-11&ss=b&srt=sco&sp=rwdlac&se=2016-12-17T04:59:26Z&st=2016-12-16T20:59:26Z&spr=https&sig=KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q%3D

작업이 시작되려면 몇 분 정도가 걸릴 수 있으며 작은 앱에 대해 몇 초에서 많은 사용자 또는 작업의 앱에 대해 몇 시간 동안 실행될 수 있습니다.

작업이 만들어진 후 해당 상태를 확인하여 여전히 실행 중인지 또는 완료되었는지 볼 수 있습니다.

작업이 성공되면 제공된 저장소 컨테이너에서 결과 데이터 파일을 사용할 수 있습니다.

