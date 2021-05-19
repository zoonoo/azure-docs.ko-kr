---
title: 데이터 보기 또는 삭제 - Custom Vision 서비스
titleSuffix: Azure Cognitive Services
description: 데이터에 대한 모든 권한을 유지 관리합니다. 이 문서에서는 Custom Vision 서비스에서 데이터를 보고, 내보내고, 삭제하는 방법을 설명합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90527397"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>Custom Vision에서 사용자 데이터 보기 또는 삭제

Custom Vision은 서비스 운영을 위해 사용자 데이터를 수집하지만, 고객은 Custom Vision [Training API](https://go.microsoft.com/fwlink/?linkid=865446)를 사용하여 해당 데이터의 보기 및 삭제를 완전히 제어합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Custom Vision에서 사용자 데이터를 보고 삭제하는 방법을 알아보려면 다음 표를 참조하세요.

| 데이터 | 작업 보기 | 삭제 작업 |
| ---- | ---------------- | ---------------- |
| 계정 정보(구독 키) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Azure Portal(Azure 구독)을 사용하여 삭제합니다. 또는 CustomVision.ai 설정 페이지에서 "계정 삭제" 단추를 사용합니다(Microsoft 계정 구독). | 
| 반복 세부 정보 | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 반복 성능 세부 정보 | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 반복 목록 | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 프로젝트 및 프로젝트 세부 정보 | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) 및 [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 이미지 태그 | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) 및 [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 이미지 | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)(이미지 다운로드를 위한 URI 제공) 및 [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)(이미지 다운로드를 위한 URI 제공) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 내보낸 반복 | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | 계정 삭제 시 삭제됨 |
