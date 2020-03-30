---
title: Azure AD Connect 클라우드 프로비저닝 변환
description: 이 문서에서는 변환을 사용하여 기본 특성 매핑을 변경하는 방법을 설명합니다.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549298"
---
# <a name="transformations"></a>변형

변환을 사용하면 클라우드 프로비저닝을 사용하여 특성이 Azure Active Directory(Azure AD)와 동기화되는 방식의 기본 동작을 변경할 수 있습니다.

이 작업을 수행하려면 스키마를 편집한 다음 웹 요청을 통해 다시 제출해야 합니다.

클라우드 프로비저닝 특성에 대한 자세한 내용은 [Azure AD 스키마 이해를](concept-attributes.md)참조하십시오.


## <a name="retrieve-the-schema"></a>스키마 검색
스키마를 검색하려면 [스키마 보기의](concept-attributes.md#view-the-schema)단계를 따릅니다. 

## <a name="custom-attribute-mapping"></a>사용자 지정 특성 매핑
사용자 지정 특성 매핑을 추가하려면 다음 단계를 따르십시오.

1. 스키마를 [Visual Studio 코드와](https://code.visualstudio.com/)같은 텍스트 또는 코드 편집기로 복사합니다.
1. 스키마에서 업데이트할 개체를 찾습니다.

   ![스키마의 개체](media/how-to-transformation/transform1.png)</br>
1. 사용자 개체 `ExtensionAttribute3` 아래에 대한 코드를 찾습니다.

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
1. 회사 특성이 에 매핑되도록 코드를 편집합니다. `ExtensionAttribute3`

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
 1. 스키마를 그래프 탐색기로 다시 복사하고 **요청 유형을** **PUT으로**변경하고 **쿼리 실행을**선택합니다.

    ![쿼리 실행](media/how-to-transformation/transform2.png)

 1. 이제 Azure 포털에서 클라우드 프로비저닝 구성으로 이동하여 **프로비저닝 다시 시작을 선택합니다.**

    ![프로비저닝 다시 시작](media/how-to-transformation/transform3.png)

 1. 잠시 후 그래프 탐색기에서 다음 쿼리를 실행하여 특성이 채워지고 있는지 `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`확인합니다.
 1. 이제 값이 표시됩니다.

    ![값이 나타납니다.](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>함수가 있는 사용자 지정 특성 매핑
고급 매핑의 경우 데이터를 조작하고 조직의 요구에 맞게 특성에 대한 값을 만들 수 있는 함수를 사용할 수 있습니다.

이 작업을 수행하려면 이전 단계를 수행한 다음 최종 값을 생성하는 데 사용되는 함수를 편집합니다.

표현식의 구문 및 예제에 대한 자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성을](reference-expressions.md)참조하십시오.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
