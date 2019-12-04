---
title: Azure AD Connect 클라우드 프로 비전 변환
description: 이 문서에서는 변환을 사용 하 여 기본 특성 매핑을 변경 하는 방법을 설명 합니다.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794472"
---
# <a name="transformations"></a>변환

변환을 사용 하면 클라우드 프로 비전을 사용 하 여 특성이 Azure AD와 동기화 되는 방식에 대 한 기본 동작을 변경할 수 있습니다.  

이 작업을 수행 하려면 스키마를 편집한 다음 웹 요청을 통해 다시 전송 해야 합니다.

클라우드 프로 비전 특성에 대 한 자세한 내용은 [AZURE AD 스키마 이해](concept-attributes.md)를 참조 하세요.


## <a name="retrieve-the-schema"></a>스키마 검색
스키마를 검색 하려면 [스키마 보기](concept-attributes.md#viewing-the-schema)에 설명 된 단계를 사용 합니다. 


## <a name="custom-attribute-mapping"></a>사용자 지정 특성 매핑
사용자 지정 특성 매핑을 추가 하려면 다음 절차를 따르십시오.

1. [Visual Studio Code](https://code.visualstudio.com/)와 같은 텍스트 또는 코드 편집기에 스키마를 복사 합니다.  
2. 스키마에서 업데이트 하려는 개체를 찾습니다 ![](media/how-to-transformation/transform1.png)</br>
3. 사용자 개체 아래에서 **ExtensionAttribute3** 에 대 한 코드를 찾습니다.

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
 4.  회사 특성이 ExtensionAttribute3에 매핑되도록 코드를 편집 합니다.
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
 5. 스키마를 다시 그래프 탐색기에 복사 하 고 요청 유형을 PUT 및 **실행 쿼리**로 변경 합니다.  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  이제 Azure Portal에서 클라우드 프로 비전 구성으로 이동 하 고 **프로 비전을 다시 시작**합니다.
 ![](media/how-to-transformation/transform3.png)</br>
 7.  잠시 후 그래프 탐색기: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`에서 다음 쿼리를 실행 하 여 특성을 채우는 지 확인 합니다.
 8.  이제 값이 표시 됩니다.
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>함수를 사용 하 여 사용자 지정 특성 매핑
고급 매핑을 위해 데이터를 조작 하 고 조직의 요구에 맞게 특성의 값을 만드는 데 사용할 수 있는 함수를 사용할 수 있습니다.

이 작업을 수행 하려면 위의 단계를 따른 후 최종 값을 생성 하는 데 사용 되는 함수를 편집 하면 됩니다.

식의 구문 및 예제에 대 한 자세한 내용은 [Azure Active Directory 특성 매핑에 대 한 식 작성](reference-expressions.md) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계 

- [프로 비전 이란?](what-is-provisioning.md)
- [클라우드 프로 비전 Azure AD Connect 이란?](what-is-cloud-provisioning.md)
