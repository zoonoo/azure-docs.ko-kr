---
title: Anomaly Detector 다변량 API 문제 해결
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API를 사용하는 경우 일반적인 오류 코드를 수정하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 변칙 검색, 기계 학습, 알고리즘
ms.openlocfilehash: 7894f2a2e588a41dcaf09f7c1e3e338f892f7d8a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318834"
---
# <a name="troubleshooting-the-multivariate-api"></a>다변량 API 문제 해결

이 문서에서는 다변량 API를 사용하는 경우 일반적인 HTTP 오류 메시지와 관련된 문제를 해결하고 해당 메시지를 수정하는 방법에 관한 지침을 제공합니다.

### <a name="multivariate-error-codes"></a>다변량 오류 코드

| 메서드                                       | HTTP 오류 코드 | 오류 메시지                                                                                                            | 수행할 작업                                                                                                                                                                                                         |
|----------------------------------------------|-----------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 다변량 변칙 검색 모델 학습 | 400             | `The 'source' field is required in the request.`                                                                         | “source”라는 키워드가 올바르게 지정되지 않았습니다. 형식은 `"{\"source\": \|" <SAS   URL>\"}"`이어야 합니다.                                                                                          |
| 다변량 변칙 검색 모델 학습 | 400             | `The source field must be a valid sas blob url`                                                                          | 원본 필드는 유효한 Blob 컨테이너 SAS URL이어야 합니다.                                                                                                                                                             |
| 다변량 변칙 검색 모델 학습 | 400             | `The 'startTime' field is required in the request.`                                                                      | 요청에 startTime을 추가합니다.                                                                                                                                                                                         |
| 다변량 변칙 검색 모델 학습 | 400             | `The 'endTime' field is required in the request.`                                                                        | 요청에 endTime을 추가합니다.                                                                                                                                                                                          |
| 다변량 변칙 검색 모델 학습 | 400             | `Invalid Timestamp format.`                                                                                              | 원본 URL에서 압축된 csv 파일의 타임스탬프 형식이 잘못되었거나 “startTime”, “endTime” 형식이 잘못되었습니다.                                                                                 |
| 다변량 변칙 검색 모델 학습 | 400             | `The displayName length exceeds maximum allowed length 24.`                                                              | DisplayName은 사용자가 다양한 모델을 구별하는 데 사용되는 선택적 매개 변수입니다. 유효한 displayName은 24자 미만이어야 합니다.                                                                    |
| 다변량 변칙 검색 모델 학습 | 400             | `The 'slidingWindow' field must be an integer between 28 and 2880.`                                                      | 슬라이딩 윈도우는 유효한 범위에 있어야 합니다.                                                                                                                                                                             |
| 다변량 변칙 검색 모델 학습 | 401             | `Unable to download blobs on the Azure Blob storage account.`                                                            | URL에 적절한 권한이 없습니다. 목록 플래그가 설정되지 않았습니다.   고객은 SAS URL을 다시 만들고 읽기 및 목록 플래그가 선택되었는지 확인해야 합니다(예: Storage Explorer 사용). |
| 다변량 변칙 검색 모델 학습 | 413             | `Unable to process the dataset. Number of variables exceed the limit (300).`                                             | Blob 컨테이너의 데이터가 현재 300개 변수 한도를 초과합니다. 고객은 변수 크기를 줄이도록 가리켜야 합니다.                                                                                |
| 다변량 변칙 검색 모델 학습 | 413             | `Valid Timestamps in the dataset exceeds the limit (1 million points), please change   startTime or endTime parameters.` | 최대 지점 수를 사용하여 100만 개 지점을 학습시킬 수 있습니다. 고객은 변수 크기를 줄이거나 startTime 또는 endTime을 변경할 수 있습니다.                                                                                     |
| 다변량 변칙 검색 모델 학습 | 413             | `Unable to process dataset. Size of dataset exceeds size limit (2GB).`                                                   | Blob 컨테이너의 데이터가 현재 4MB 한도를 초과합니다. 고객은 더 작은 데이터를 포함하는 Blob을 가리켜야 합니다.                                                                                          |
| 다변량 변칙 검색                  | 404             | `The model does not exist.`                                                                                              | 모델 ID가 잘못되었습니다. 고객은 모델을 사용하기 전에 학습시켜야 합니다.                                                                                                                                            |
| 다변량 변칙 검색                  | 400             | `The model is not ready yet.`                                                                                            | 모델이 아직 준비되지 않았습니다. 고객은 다변량 모델 가져오기 API를 호출하여 모델 상태를 확인해야 합니다.                                                                                                               |
| 다변량 변칙 검색                  | 400             | `The 'source' field is required in the request.`                                                                         | “source”라는 키워드가 올바르게 지정되지 않았습니다. 형식은 `"{\"source\": \|" <SAS   URL>\"}"`이어야 합니다.                                                                                          |
| 다변량 변칙 검색                  | 400             | `The source field must be a valid sas blob url`                                                                          | 원본 필드는 유효한 Blob 컨테이너 SAS URL이어야 합니다.                                                                                                                                                             |
| 다변량 변칙 검색                  | 400             | `The 'startTime' field is required in the request.`                                                                      | 요청에 startTime을 추가합니다.                                                                                                                                                                                         |
| 다변량 변칙 검색                  | 400             | `The 'endTime' field is required in the request.`                                                                        | 요청에 endTime을 추가합니다.                                                                                                                                                                                          |
| 다변량 변칙 검색                  | 400             | `Invalid Timestamp format.`                                                                                              | 원본 URL에서 압축된 csv 파일의 타임스탬프 형식이 잘못되었거나 “startTime”, “endTime” 형식이 잘못되었습니다.                                                                                 |
| 다변량 변칙 검색                  | 400             | `The corresponding file of the variable does not exist.`                                                                 | 한 변수가 학습에 사용되었지만 고객이 해당 모델을 사용하여 검색을 수행할 때 해당 변수를 찾을 수 없습니다. 고객은 이 변수를 추가한 후 검색 요청을 제출해야 합니다.         |
| 다변량 변칙 검색                  | 413             | `Unable to process the dataset. Number of variables exceed the limit (300).`                                             | Blob 컨테이너의 데이터가 현재 300개 변수 한도를 초과합니다. 고객은 변수 크기를 줄이도록 가리켜야 합니다.                                                                                |
| 다변량 변칙 검색                  | 413             | `The limit timestamps of one detection request is 2880, please change startTime or endTime parameters.`                | 한 검색 요청에서 검색할 최대 타임스탬프는 2,880개이며, 고객은 startTime 또는 endTime을 변경한 후 검색 요청을 제출해야 합니다.                                                           |
| 다변량 변칙 검색                  | 413             | `Unable to process dataset. Size of dataset exceeds size limit (2GB).`                                                   | Blob 컨테이너의 데이터가 현재 4MB 한도를 초과합니다. 고객은 더 작은 데이터를 포함하는 Blob을 가리켜야 합니다.                                                                                          |
| 다변량 모델 가져오기                       | 404             | `Model with 'id=<input model ID>' not found.`                                                                            | ID가 유효한 모델 ID가 아닙니다. 모델 가져오기를 사용하여 유효한 모든 모델 ID를 찾습니다.                                                                                                                               |
| 다변량 모델 가져오기                       | 404             | `Model with id=<input model ID>' not found.`                                                                            | ID가 유효한 모델 ID가 아닙니다. 모델 가져오기를 사용하여 유효한 모든 모델 ID를 찾습니다.                                                                                                                               |
| 다변량 변칙 검색 결과 가져오기    | 404             | `Result   with 'id=<input result ID>' not found.`                                                                          | ID가 유효한 결과 ID가 아닙니다. 검색 요청을 다시 제출합니다.                                                                                                                                   |
| 다변량 모델 삭제                    | 404             | `Location   for model with 'id=<input model ID>' not found.`                                                               | ID가 유효한 모델 ID가 아닙니다. 모델 가져오기를 사용하여 유효한 모든 모델 ID를 찾습니다.                                                                                                                               |