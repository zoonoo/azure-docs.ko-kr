---
title: '자습서: 엔드포인트 발화 검토 - LUIS'
description: 이 자습서에서는 LUIS에서 확실하지 않은 LUIS HTTP 엔드포인트를 통해 받은 발화를 확인하거나 수정하여 앱 예측을 향상시킵니다. 의도를 확인해야 하는 발언도 있고, 엔터티를 확인해야 하는 발언도 있습니다.
services: cognitive-services
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 32d43b36910c8fbfd60463f4062b6a00b9272fdb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592579"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>자습서: 엔드포인트 발언을 검토하여 알 수 없는 예측 수정
이 자습서에서는 LUIS에서 확실하지 않은 LUIS HTTPS 엔드포인트를 통해 받은 발화를 확인하거나 수정하여 앱 예측을 향상시킵니다. 예약된 LUIS 유지 관리의 일반적인 부분으로 엔드포인트 발화를 검토해야 합니다.

이 검토 프로세스를 통해 LUIS에서 애플리케이션 도메인을 학습할 수 있습니다. LUIS는 검토 목록에 표시되는 발화를 선택합니다. 이 목록은 다음과 같습니다.

* 앱에만 적용됩니다.
* 앱의 예측 정확도를 높이기 위한 것입니다.
* 정기적으로 검토해야 합니다.

엔드포인트 발언을 검토하여 발언의 예측된 의도를 확인하거나 수정합니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 예제 앱 가져오기
> * 엔드포인트 utterances 검토
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>앱에 대한 JSON 파일 다운로드

[앱 JSON 파일](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true)을 다운로드하고 저장합니다.

## <a name="import-json-file-for-app"></a>앱에 대한 JSON 파일 가져오기

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>엔터티 변경 내용을 앱에 적용하도록 앱 학습

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>HTTP 엔드포인트에서 앱에 액세스할 수 있도록 앱 게시

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>엔드포인트에서 발화 추가

이 앱에는 의도와 엔터티가 있지만 엔드포인트가 사용되지 않습니다. 엔드포인트 발화 검토를 사용하여 앱을 향상시키려면 이 엔드포인트를 사용해야 합니다.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 주소 표시줄의 URL 끝으로 이동하여 _YOUR_QUERY_HERE_를 다음 표의 발화로 바꿉니다. 각 발화에 대해 발화를 제출하고 결과를 가져옵니다. 그런 다음, 끝에 있는 발화를 다음 발화로 바꿉니다.

    |엔드포인트 발화|정렬된 의도|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    적극적으로 편집 중인 버전이나 엔드포인트에서 게시된 앱 버전에 관계없이 검토할 단일 발언 풀이 있습니다.

## <a name="review-endpoint-utterances"></a>엔드포인트 utterances 검토

올바르게 정렬된 의도에 대한 엔드포인트 발화를 검토합니다. 모든 버전에서 검토할 단일 발화 풀이 있지만, 의도를 올바르게 정렬하는 프로세스는 발화 예제를 현재 _활성 모델_에만 추가합니다.

1. 포털에 있는 **빌드** 섹션의 왼쪽 탐색 영역에서 **엔드포인트 발화 검토**를 선택합니다. 목록은 **ApplyForJob** 의도에 대해 필터링됩니다.

    > [!div class="mx-imgBorder"]
    > ![왼쪽 탐색 영역에 있는 엔드포인트 발화 검토 단추의 스크린샷](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    이 발화(`I'm looking for a job with Natural Language Processing`)는 올바른 의도에 있지 않습니다.

1.  이 발화를 정렬하려면 발화 행에서 `GetJobInformation`의 올바르게 **정렬된 의도**를 선택합니다. 확인 표시를 선택하여 변경된 발화를 앱에 추가합니다.

    > [!div class="mx-imgBorder"]
    > ![왼쪽 탐색 영역에 있는 엔드포인트 발화 검토 단추의 스크린샷](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    이 의도의 나머지 발화를 검토하여 필요에 따라 정렬된 의도를 수정합니다. 이 자습서의 초기 발화 표를 사용하여 정렬된 의도를 살펴보세요.

    **엔드포인트 발화 검토** 목록에는 수정된 발화가 더 이상 없어야 합니다. 더 많은 발화가 표시되면 목록이 비어 있을 때까지 정렬된 의도를 수정하면서 목록 작업을 계속 수행합니다.

    의도 세부 정보 페이지에서 의도가 정렬되면 엔터티 레이블 지정을 수정합니다.

1. 앱을 학습하고 다시 게시합니다.

## <a name="get-intent-prediction-from-endpoint"></a>엔드포인트에서 의도 예측 가져오기

올바르게 정렬된 발화 예제를 통해 앱의 예측이 향상되었는지 확인하려면 수정된 발화에 가까운 발화를 시도합니다.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 주소 표시줄의 URL 끝으로 이동하여 _YOUR_QUERY_HERE_를 `Are there any natural language processing jobs in my department right now?`로 바꿉니다.

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   이제 확실하지 않은 발화가 올바르게 정렬되었으므로 **높은 점수**를 사용하여 올바른 의도를 예측했습니다.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>더 많은 발언을 추가하여 검토를 대체할 수 있습니까?
더 많은 예제 발언을 추가하지 않은 이유가 궁금할 것입니다. 엔드포인트 발언 검토의 목적은 무엇입니까? 실제 LUIS 앱에서 엔드포인트 발언은 아직 사용하지 않은 단어 선택 및 배열을 사용하는 사용자로부터 나온 것입니다. 동일한 단어 선택과 배열을 사용한 경우, 원래의 예측은 더 높은 백분율을 가집니다.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>발언 목록에 최고 의도가 있는 이유는 무엇입니까?
일부 엔드포인트 발언은 검토 목록에서 높은 예측 점수를 갖습니다. 여전히 이러한 발언을 검토하고 확인해야 합니다. 그 다음 높은 의도가 최고 의도 점수에 매우 가까운 점수를 가졌기 때문에 이러한 발언이 목록에 있습니다. 최고 의도 두 개는 약 15% 정도 차이가 있는 것이 좋습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 엔드포인트에 제출된 발언 중 LUIS가 알지 못하는 발언을 검토했습니다. 이러한 발언이 확인되고 올바른 의도에 예시 발언으로 이동하면 LUIS가 예측 정확도를 개선합니다.

> [!div class="nextstepaction"]
> [패턴 사용 방법 알아보기](luis-tutorial-pattern.md)
