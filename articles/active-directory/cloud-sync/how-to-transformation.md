---
title: Azure AD Connect 클라우드 동기화 관련 변환
description: 이 문서에서는 변환을 사용하여 기본 특성 매핑을 변경하는 방법을 설명합니다.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 1c81ee0ebace65e50cdab5b5b223d5e5eae4ff9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613574"
---
# <a name="transformations"></a>변환

변환을 사용하면 클라우드 동기화를 사용하여 특성이 Azure AD(Azure Active Directory)와 동기화되는 기본 동작을 변경할 수 있습니다.

이 작업을 수행하려면 스키마를 편집한 다음 웹 요청을 통해 다시 제출해야 합니다.

클라우드 동기화 특성에 대한 자세한 내용은 [Azure AD 스키마 이해](concept-attributes.md)를 참조하세요.


## <a name="retrieve-the-schema"></a>스키마 검색
스키마를 검색하려면 [스키마 보기](concept-attributes.md#view-the-schema)의 단계를 수행합니다. 

## <a name="custom-attribute-mapping"></a>사용자 지정 특성 매핑
사용자 지정 특성 매핑을 추가하려면 다음 단계를 수행합니다.

1. 스키마를 텍스트 또는 코드 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))에 복사합니다.
1. 스키마에서 업데이트하려는 개체를 찾습니다.

   ![스키마의 개체](media/how-to-transformation/transform-1.png)</br>
1. 사용자 개체 아래에서 `ExtensionAttribute3`에 대한 코드를 찾습니다.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. 회사 특성이 `ExtensionAttribute3`에 매핑되도록 코드를 편집합니다.

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. 스키마를 다시 Graph Explorer에 복사하고 **요청 형식** 을 **PUT** 로 변경한 다음 **쿼리 실행** 을 선택합니다.

    ![쿼리 실행](media/how-to-transformation/transform-2.png)

 1. 이제 Azure Portal에서 클라우드 동기화 구성으로 이동하여 **프로비저닝 다시 시작** 을 선택합니다.

    ![프로비저닝 다시 시작](media/how-to-transformation/transform-3.png)

 1. 잠시 후 Graph Explorer에서 `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` 쿼리를 실행하여 특성이 채워지는지 확인합니다.
 1. 이제 값이 표시됩니다.

    ![값이 나타납니다.](media/how-to-transformation/transform-4.png)

## <a name="custom-attribute-mapping-with-function"></a>함수를 통해 사용자 지정 특성 매핑
고급 매핑의 경우 조직의 요구에 맞게 데이터를 조작하고 특성에 대한 값을 만들 수 있는 함수를 사용할 수 있습니다.

이 작업을 수행하려면 이전 단계를 수행한 다음, 최종값을 구성하는 데 사용되는 함수를 편집합니다.

식의 구문 및 예제에 대한 자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](reference-expressions.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
