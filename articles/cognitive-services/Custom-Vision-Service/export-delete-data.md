---
title: 데이터 내보내기 또는 삭제 - Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Custom Vision Service에서 데이터를 내보내거나 삭제하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 4c395a062b1132710f888cc5a315529db082a805
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850029"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Custom Vision에서 사용자 데이터 내보내기 또는 삭제

Custom Vision 서비스를 운영 하는 사용자 데이터를 수집 하지만 고객은 보기, 내보내기 및 삭제 사용자 지정 비전을 사용 하 여 해당 데이터를 완전히 제어할 [교육 Api](https://go.microsoft.com/fwlink/?linkid=865446)합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

내보내기 및 사용자 지정 비전의 사용자 데이터를 삭제 하는 방법에 알아보려면 다음 표를 참조 합니다.

| Data | 내보내기 작업 | 삭제 작업 |
| ---- | ---------------- | ---------------- |
| 계정 정보(구독 키) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Azure Portal(Azure 구독)을 사용하여 삭제합니다. 또는 CustomVision.ai 설정 페이지에서 "계정 삭제" 단추를 사용합니다(Microsoft 계정 구독). | 
| 반복 세부 정보 | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 반복 성능 세부 정보 | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 반복 목록 | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 프로젝트 및 프로젝트 세부 정보 | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) 및 [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 이미지 태그 | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) 및 [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 이미지 | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)(이미지 다운로드를 위한 URI 제공) 및 [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)(이미지 다운로드를 위한 URI 제공) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 내보낸 모델 | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | 계정 삭제 시 삭제됨 |
