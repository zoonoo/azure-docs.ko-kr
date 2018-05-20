---
title: Azure Media Services의 Transform 및 Job | Microsoft Docs
description: Media Services를 사용할 때 비디오를 처리하기 위한 규칙이나 사양을 설명하는 변환을 만들어야 합니다. 이 문서에서는 Transform의 개념과 사용법에 대해 간략히 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d256d87548d54951cb77beffb88bba26a1a3de49
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="transforms-and-jobs"></a>Transform 및 Job

## <a name="overview"></a>개요 

Azure Media Services REST API의 최신 버전(v3)에는 비디오 인코딩 및/또는 분석을 위한 템플릿 워크플로 리소스인 **Transform**이 새로 도입되었습니다. **Transforms**은 비디오 인코딩 또는 분석을 위한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **Transform**은 비디오 또는 오디오 파일을 처리하는 작업의 간단한 워크플로를 설명합니다. 

**Transform** 개체는 레시피이며 **Job**은 주어진 입력 비디오 또는 오디오 콘텐츠에 **Transform**을 적용하라는 Azure Media Services에 대한 실제 요청입니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. 비디오의 위치는 HTTP URL, SAS URL 또는 로컬이나 Azure Blob Storage에 있는 파일 경로를 사용하여 지정할 수 있습니다. Azure Media Services 계정에 Transform을 최대 100개까지 포함할 수 있으며 해당 Transform에 따라 Job을 제출할 수 있습니다. 그런 다음 Azure Event Grid 알림 시스템과 직접 통합되는 알림을 사용하여 작업 상태 변경과 같은 이벤트에 가입할 수 있습니다. 

이 API는 Azure Resource Manager에 의해 실행되기 때문에 Resource Manager 템플릿을 사용하여 Media Services 계정에 Transform를 만들고 배포할 수 있습니다. API의 리소스 수준에서 역할 기반 액세스 제어를 설정하여 Transform과 같은 특정 리소스에 대한 액세스를 잠글 수도 있습니다.

## <a name="typical-workflow-and-example"></a>일반적인 워크플로 및 예제

1. Transform 만들기 
2. Transform에 따라 Job 제출 
3. Transform 나열 
4. 나중에 이 "레시피"를 사용할 계획이 아니면 Transform을 삭제합니다. 

모든 비디오의 첫 번째 프레임을 썸네일 이미지로 추출하려는 경우 수행할 단계는 다음과 같습니다. 

1. 처리 규칙을 정의합니다. 예를 들면 비디오의 첫 번째 프레임을 썸네일로 사용합니다. 
2. 그런 다음 서비스에 대한 입력으로 가지고 있는 각 비디오에 대한 다음 사항을 서비스에 알립니다. 
    1. 비디오를 찾을 수 있는 위치 
    2. 출력(예: 썸네일 이미지)을 기록할 위치 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [비디오 파일 스트리밍](stream-files-dotnet-quickstart.md)
