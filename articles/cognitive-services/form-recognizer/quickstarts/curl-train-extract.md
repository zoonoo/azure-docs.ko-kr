---
title: '빠른 시작: cURL을 사용하여 모델 학습 및 양식 데이터 추출 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 cURL에서 Form Recognizer REST API를 사용하여 모델을 학습시키고 양식에서 데이터를 추출합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: 1990077e6466e08c1b6c463dafe9809b52df6d14
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537611"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>빠른 시작: cURL에서 REST API를 사용하여 Form Recognizer 모델 학습 및 양식 데이터 추출

이 빠른 시작에서는 cURL에서 Azure Form Recognizer REST API를 통해 양식을 학습시키고 채점하여 키-값 쌍 및 테이블을 추출합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건
이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.
- Form Recognizer 제한된 액세스 미리 보기에 대한 액세스 권한. 미리 보기에 액세스하려면 [Form Recognizer 액세스 요청](https://aka.ms/FormRecognizerRequestAccess) 양식을 작성하여 제출하세요.
- [cURL](https://curl.haxx.se/windows/) 설치
- 동일한 형식의 5개 이상 양식으로 구성된 세트. 이 데이터를 사용하여 모델을 학습합니다. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)를 사용할 수 있습니다. Azure Blob Storage 계정의 루트에 데이터를 업로드합니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Form Recognizer 모델 학습

먼저 Azure Storage Blob의 학습 데이터 세트가 필요합니다. 주 입력 데이터와 동일한 형식/구조의 채워진 양식(PDF 문서 및/또는 이미지)이 5개 이상 있어야 합니다. 또는 두 개의 채워진 양식이 있는 단일 빈 양식을 사용할 수 있습니다. 빈 양식의 파일 이름에 "empty"라는 단어가 포함되어야 합니다. 학습 데이터를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 집합 빌드](../build-training-data-set.md)를 참조하세요.

Azure Blob 컨테이너의 문서를 사용하여 Form Recognizer 모델을 학습시키려면 다음 cURL 명령을 실행하여 **Train** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `<Endpoint>`를 Form Recognizer 구독 키에서 가져온 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `<subscription key>`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `<SAS URL>`을 Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URL로 바꿉니다. SAS를 검색하려면 Microsoft Azure Storage Explorer를 열고, 컨테이너를 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기**를 선택합니다. **읽기** 권한과 **목록 사용** 권한이 선택되어 있는지 확인하고 **만들기**를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

다음 JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다.

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

`"modelId"` 값을 적어두세요. 다음 단계에서 필요합니다.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>양식에서 키-값 쌍 및 테이블 추출

다음으로, 문서를 분석하여 키-값 쌍 및 테이블을 추출합니다. 뒤에 나오는 cURL 명령을 실행하여 **모델 - 분석** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `<Endpoint>`를 Form Recognizer 구독 키에서 가져온 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `<modelID>`를 이전 섹션에서 받은 모델 ID로 바꿉니다.
1. `<path to your form>`을 양식의 파일 경로(예: C:\temp\file.pdf)로 바꿉니다.
1. `<file type>`을 파일 형식으로 바꿉니다. 지원되는 형식은 `application/pdf`, `image/jpeg`, `image/png`입니다.
1. `<subscription key>`를 구독 키로 바꿉니다.


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>응답 검사

성공 응답이 JSON으로 반환됩니다. 양식에서 추출된 키-값 쌍 및 테이블을 나타냅니다.

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 cURL에서 Form Recognizer REST API를 사용하여 모델을 학습시키고 샘플 시나리오에서 실행했습니다. 다음으로, 참조 설명서를 통해 Form Recognizer API에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [REST API 참조 설명서](https://aka.ms/form-recognizer/api)
