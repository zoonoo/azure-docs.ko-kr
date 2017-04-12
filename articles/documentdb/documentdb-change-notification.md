---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-change-feed-hl7-fhir-logic-apps
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 1b5ec3cb1c2aba86570c6f1753d9142c0d0349ce
ms.lasthandoff: 03/29/2017


---
# <a name="notifications-for-new-or-changed-documentdb-resources-using-logic-apps"></a>논리 앱을 사용하여 새롭거나 변경된 DocumentDB 리소스에 대한 알림
이 문서는 Azure DocumentDB 커뮤니티 포럼 중 하나에 게시된 질문에서 가져온 것입니다. 질문은 **DocumentDB가 수정된 리소스에 대해 알림을 지원하는가**?였습니다.

여러 해 동안 BizTalk Server로 작업하면서 [WCF LOB 어댑터](https://msdn.microsoft.com/library/bb798128.aspx)를 사용할 때 매우 일반적인 시나리오입니다. 따라서 DocumentDB에서 새롭거나 수정된 문서에 대해 이 기능을 복제할 수 있는지 알아보기로 했습니다.

이 문서에서는 [트리거](documentdb-programming.md#trigger) 및 [논리 앱](../logic-apps/logic-apps-what-are-logic-apps.md)을 포함하는 변경 알림 솔루션의 구성 요소에 대한 개요를 제공합니다. 중요한 코드 조각이 인라인으로 제공되며 전체 솔루션은 [GitHub](https://github.com/HEDIDIN/DocDbNotifications)에서 사용할 수 있습니다.

## <a name="use-case"></a>사용 사례
다음 스토리는 이 문서에 대한 사용 사례입니다.

DocumentDB는 HL7(Health Level Seven International) FHIR(Fast Healthcare Interoperability Resources) 문서를 위한 리포지토리입니다. API 및 논리 앱이 결합된 DocumentDB 데이터베이스가 HL7 FHIR Server를 구성한다고 가정해 보겠습니다.  의료 시설에서는 환자 데이터를 DocumentDB "Patients" 데이터베이스에 저장합니다. 환자 데이터베이스 내에는 Clinical, Identification 등의 여러 컬렉션이 있습니다. 환자 정보는 Identification에 포함됩니다.  "Patient"라는 컬렉션이 있습니다.

심장병 부서에서 개인 건강 및 운동 데이터를 추적하고 있습니다. 새로운 또는 수정된 환자 레코드를 검색하는 것은 시간이 많이 걸립니다. IT 부서에 새로운 또는 수정된 환자 레코드에 대한 알림을 받을 수 있는 방법이 있는지 문의했습니다.  

IT 부서에서는 쉽고 제공할 수 있다고 답했습니다. 또한 문서를 [Azure Blob 저장소](https://azure.microsoft.com/services/storage/) 에 푸시하여 심장병 부서에서 쉽게 액세스할 수 있도록 하는 것도 가능하다고 했습니다.

## <a name="how-the-it-department-solved-the-problem"></a>IT 부서에서 문제를 해결하는 방법
이 응용 프로그램을 만들기 위해 IT 부서는 이를 먼저 모델링하기로 결정했습니다.  BPMN(Business Process Model and Notation) 사용 시 좋은 점은 기술자와 비기술자 모두 쉽게 이해할 수 있다는 점입니다. 이 전체 알림 프로세스는 비즈니스 프로세스로 간주됩니다. 

## <a name="high-level-view-of-notification-process"></a>알림 프로세스의 상위 수준 보기
1. 타이머 트리거가 있는 논리 앱에서 시작합니다. 트리거는 기본적으로 1시간 마다 실행됩니다.
2. 그런 다음 논리 앱에 HTTP POST를 수행합니다.
3. 논리 앱이 모든 작업을 수행합니다.

![상위 수준 보기](./media/documentdb-change-notification/high-level-view.png)

### <a name="lets-take-a-look-at-what-this-logic-app-does"></a>이 논리 앱이 수행하는 작업을 살펴보겠습니다.
다음 그림을 보면 논리 앱 워크플로에 여러 단계가 있습니다.

![기본 논리 프로세스](./media/documentdb-change-notification/main-logic-app-process.png)

단계는 다음과 같습니다.

1. API 앱에서 현재 UTC DateTime을 가져와야 합니다.  기본값은 1시간 이전입니다.
2. UTC DateTime을 Unix Timestamp 형식으로 변환합니다. DocumentDB에서 타임스탬프에 대한 기본 형식입니다.
3. DocumentDB 쿼리를 수행하는 API 앱에 값을 게시(POST)합니다. 값은 쿼리에 사용됩니다.
   
    ```SQL
         SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```
   
   > [!NOTE]
   > _ts는 모든 DocumentDB 리소스에 대한 TimeStamp 메타데이터를 나타냅니다.
   > 
   > 
4. 발견된 문서가 있는 경우 응답 본문이 Azure Blob 저장소에 전송됩니다.
   
   > [!NOTE]
   > Blob 저장소를 사용하려면 Azure 저장소 계정이 필요합니다. Azure Blob 저장소 계정을 프로비전하고 새 Blob 명명된 환자를 추가해야 합니다. 자세한 내용은 [Azure Storage 계정 정보](../storage/storage-create-storage-account.md) 및 [Azure Blob Storage 시작](../storage/storage-dotnet-how-to-use-blobs.md)을 참조하세요.
   > 
   > 
5. 마지막으로 받는 사람에게 발견된 문서 수를 알리는 전자 메일이 보내집니다. 문서가 발견되지 않은 경우 전자 메일 본문은 "0개 문서 찾음"이 됩니다. 

이제 워크플로를 파악했으므로 이를 구현하는 방법을 살펴보겠습니다.

### <a name="lets-start-with-the-main-logic-app"></a>기본 논리 앱부터 시작해 보겠습니다
논리 앱에 대해 잘 모르는 경우 [Azure Marketplace](https://portal.azure.com/)에서 사용할 수 있으며 [논리 앱이란 무엇인가요?](../logic-apps/logic-apps-what-are-logic-apps.md)에서 자세히 알아볼 수 있습니다.

새 논리 앱을 만들 때 **어떻게 시작하시겠습니까?**

텍스트 상자 내부를 클릭하면 이벤트를 선택할 수 있습니다. 이 논리 앱의 경우 아래 표시된 것처럼 **수동 - HTTP 요청을 받을 때**를 선택합니다.

![시작](./media/documentdb-change-notification/starting-off.png)

### <a name="design-view-of-your-completed-logic-app"></a>완료된 논리 앱의 디자인 보기
DocDB라는 논리 앱에 대한 완료된 디자인 보기로 이동하여 살펴보겠습니다.

![논리 앱 워크플로](./media/documentdb-change-notification/workflow-expanded.png)

논리 앱 디자이너에서 동작을 편집할 때 아래 sendMail 동작에 표시된 것처럼 HTTP 요청 또는 이전 동작에서 **출력** 을 선택하는 옵션이 있습니다.

![출력 선택](./media/documentdb-change-notification/choose-outputs.png)

워크플로의 각 동작 이전에 다음 그림에 나와 있는 것처럼 **작업 추가** 또는 **조건 추가**를 결정할 수 있습니다.

![결정](./media/documentdb-change-notification/add-action-or-condition.png)

**조건 추가**를 선택하는 경우 다음 그림에 나와 있는 것처럼 논리를 입력할 양식이 표시됩니다.  이것은 기본적으로 비즈니스 규칙입니다.  필드 안을 클릭하면 이전 동작에서 매개 변수를 선택할 수 있습니다. 또한 값을 직접 입력할 수도 있습니다.

![조건 추가](./media/documentdb-change-notification/condition1.png)

> [!NOTE]
> 또한 코드 보기에서 모든 항목을 입력하는 기능도 있습니다.
> 
> 

코드 보기에서 완료된 논리 앱을 살펴보겠습니다.  

```JSON

       "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }

```

코드에서 어떤 다른 섹션이 나타날지 잘 모르는 경우 [논리 앱 워크플로 정의 언어](http://aka.ms/logicappsdocs) 설명서를 볼 수 있습니다.

이 워크플로에서는 [HTTP Webhook 트리거](https://sendgrid.com/blog/whats-webhook/)를 사용합니다. 위의 코드를 보면 매개 변수는 다음 예제와 같이 표시됩니다.

```C#

    =@{triggerBody()['Subject']}

```

`triggerBody()` 는 REST POST의 본문에 포함되는 매개 변수를 논리 앱 REST API로 나타냅니다. `()['Subject']` 는 이 필드를 나타냅니다. 이러한 모든 매개 변수가 JSON 형식의 본문을 구성합니다. 

> [!NOTE]
> 웹 후크를 사용하여 트리거 요청의 헤더 및 본문을 완전히 액세스할 수 있습니다. 이 응용 프로그램에서는 본문을 원합니다.
> 
> 

앞에서 설명한 대로 디자이너를 사용하여 매개 변수를 할당하거나 코드 보기에서 작업을 수행할 수 있습니다.
코드 보기에서 수행하는 경우 다음 코드 샘플에 표시된 대로 값이 필요한 속성을 정의합니다. 

```JSON

    "triggers": {
        "manual": {
            "inputs": {
            "schema": {
                "properties": {
            "Subject": {
                "type" : "String"    

            }
            },
                "required": [
            "Subject"
                 ],
                "type": "object"
            }
            },
            "type": "Manual"
        }
        }
```

현재 수행하는 작업은 HTTP POST의 본문에서 전달할 JSON 스키마를 만드는 것입니다.
트리거를 실행하려면 콜백 URL이 필요합니다.  이 자습서 뒷부분에서 생성하는 방법을 알아봅니다.  

## <a name="actions"></a>작업
논리 앱에서 각 동작이 어떤 일을 하는지 살펴보겠습니다.

### <a name="getutcdate"></a>GetUTCDate
**디자이너 뷰**

![](./media/documentdb-change-notification/getutcdate.png)

**코드 보기**

```JSON

    "GetUtcDate": {
            "conditions": [],
            "inputs": {
            "method": "get",
            "queries": {
                "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
            },
            "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
            "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
            },
            "type": "Http"
        },

```

이 HTTP 동작은 GET 작업을 수행합니다.  API 앱 GetUtcDate 메서드를 호출합니다. Uri는 트리거 본문에 전달된 'GetUtcDate_HoursBack' 속성을 사용합니다.  'GetUtcDate_HoursBack' 값은 첫 번째 논리 앱에 설정됩니다. 이 자습서의 뒷부분에 나오는 트리거 논리 앱에 대해 자세히 알아봅니다.

이 동작은 UTC 날짜 문자열 값을 반환하기 위한 API 앱을 호출합니다.

#### <a name="operations"></a>작업
**요청**

```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
        "method": "get",
        "queries": {
          "hoursBack": "24"
        }
    }

```

**응답**

```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": "Fri, 15 Jan 2016 23:47:33 GMT"
    }

```

다음 단계는 UTC DateTime 값을 .NET 실수(Double) 형식인 Unix TimeStamp로 변환합니다.

### <a name="conversion"></a>변환
##### <a name="designer-view"></a>디자이너 뷰
![변환](./media/documentdb-change-notification/conversion.png)

##### <a name="code-view"></a>코드 보기
```JSON

    "Conversion": {
        "conditions": [
        {
            "dependsOn": "GetUtcDate"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "currentDateTime": "@{body('GetUtcDate')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

이 단계에서는 GetUTCDate에서 반환된 값을 전달합니다.  GetUTCDate 동작을 성공적으로 완료해야 함을 의미하는 dependsOn 조건이 있습니다. 그렇지 않은 경우 이 동작을 건너뜁니다. 

이 동작은 변환을 처리하기 위한 API 앱을 호출합니다.

#### <a name="operations"></a>작업
##### <a name="request"></a>요청
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
        "method": "post",
        "queries": {
        "currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
        }
    }   
```

##### <a name="response"></a>응답
```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": 1452901653
    }
```

다음 동작에서는 API 앱에 대해 POST 작업을 수행합니다.

### <a name="getdocuments"></a>GetDocuments
##### <a name="designer-view"></a>디자이너 뷰
![문서 가져오기](./media/documentdb-change-notification/getdocuments.png)

##### <a name="code-view"></a>코드 보기
```JSON

    "GetDocuments": {
        "conditions": [
        {
            "dependsOn": "Conversion"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "unixTimeStamp": "@{body('Conversion')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

GetDocuments 동작의 경우 Conversion 동작에서 응답 본문을 전달하려고 합니다. Uri에 있는 매개 변수입니다.

```C#

    unixTimeStamp=@{body('Conversion')}

```

QueryDocuments 동작은 API 앱에 대해 HTTP POST 작업을 수행합니다. 

호출된 메서드는 **QueryForNewPatientDocuments**입니다.

#### <a name="operations"></a>작업
##### <a name="request"></a>요청
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
        "method": "post",
        "queries": {
        "unixTimeStamp": "1452901653"
        }
    }
```

##### <a name="response"></a>응답
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "Microsoft-IIS/8.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
            "status": "generated",
            "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
            {
                "use": "usual",
                "type": {
                "coding": [
                    {
                    "system": "http://hl7.org/fhir/v2/0203",
                    "code": "MR"
                    }
                ]
                },
                "system": "urn:oid:2.16.840.1.113883.19.5",
                "value": "12345"
            }
            ],
            "active": true,
            "name": [
            {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

다음 동작은 문서를 [Azure 블로그 저장소](https://azure.microsoft.com/services/storage/)에 저장하는 것입니다. 

> [!NOTE]
> Blob 저장소를 사용하려면 Azure 저장소 계정이 필요합니다. Azure Blob 저장소 계정을 프로비전하고 새 Blob 명명된 환자를 추가해야 합니다. 자세한 내용은 [Azure Blob 저장소 시작](../storage/storage-dotnet-how-to-use-blobs.md)을 참조하세요.
> 
> 

### <a name="create-file"></a>파일 만들기
##### <a name="designer-view"></a>디자이너 뷰
![파일 만들기](./media/documentdb-change-notification/createfile.png)

##### <a name="code-view"></a>코드 보기
```JSON

    {
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

코드는 디자이너의 동작에서 생성됩니다. 코드를 수정할 필요가 없습니다.

Azure Blob API 사용에 대해 잘 모르는 경우 [Azure Blob 저장소 API 시작](../connectors/connectors-create-api-azureblobstorage.md)을 참조하세요.

#### <a name="operations"></a>작업
##### <a name="request"></a>요청
```JSON

    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### <a name="response"></a>응답
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:36 GMT",
        "set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
        "server": "Microsoft-HTTPAPI/2.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": {
        "Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
        "Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "LastModified": "2016-02-26T15:47:36.215Z",
        "Size": 65647,
        "MediaType": "application/octet-stream",
        "IsFolder": false,
        "ETag": "\"c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ\"",
        "FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
        }
    }
```

마지막 단계는 전자 메일 알림을 보내는 것입니다.

### <a name="sendemail"></a>sendEmail
##### <a name="designer-view"></a>디자이너 뷰
![전자 메일 보내기](./media/documentdb-change-notification/sendemail.png)

##### <a name="code-view"></a>코드 보기
```JSON


    "sendMail": {
        "conditions": [
        {
            "dependsOn": "GetDocuments"
        }
        ],
        "inputs": {
        "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
        "headers": {
            "Content-type": "application/x-www-form-urlencoded"
        },
        "method": "POST",
        "uri": "https://api.sendgrid.com/api/mail.send.json"
        },
        "type": "Http"
    }
```

이 동작에서 전자 메일 알림을 보냅니다.  [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29)를 사용하고 있습니다.   

이 경우 코드는 [101-logic-app-sendgrid GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid)에 있는 SendGrid 및 논리 앱을 위한 템플릿을 사용하여 생성되었습니다.

HTTP 작업은 POST입니다. 

권한 부여 매개 변수는 트리거 속성에 있습니다.

```JSON

    },
        "sendgridPassword": {
             "type": "SecureString"
         },
         "sendgridUsername": {
            "type": "String"
         }

        In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
        },
        "toAddress": {
            "defaultValue": "XXXX@XXXX.com",
            "type": "String"
        },
        "fromAddress": {
            "defaultValue": "XXX@msn.com",
            "type": "String"
        },
        "emailBody": {
            "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
            "type": "String"
        },

```

emailBody는 쿼리에서 반환된 문서 수("0" 이상일 수 있음)와 "레코드 찾음"을 연결합니다. 나머지 매개 변수는 트리거 매개 변수에서 설정됩니다.

이 동작은 **GetDocuments** 동작에 따라 달라집니다.

#### <a name="operations"></a>작업
##### <a name="request"></a>요청
```JSON

    {
        "uri": "https://api.sendgrid.com/api/mail.send.json",
        "method": "POST",
        "headers": {
        "Content-type": "application/x-www-form-urlencoded"
        },
        "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
    }

```

##### <a name="response"></a>응답
```JSON

    {
        "statusCode": 200,
        "headers": {
        "connection": "keep-alive",
        "x-Frame-Options": "DENY,DENY",
        "access-Control-Allow-Origin": "https://sendgrid.com",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "nginx"
        },
        "body": {
        "message": "success"
        }
    }
```

마지막으로 Azure 포털에서 논리 앱의 결과를 확인할 수 있습니다. 이렇게 하기 위해 출력 섹션에 매개 변수를 추가합니다.

```JSON

    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
        }

```

그러면 전자 메일 본문에 전송된 것과 동일한 값을 반환합니다. 다음 그림은 "29개의 레코드 찾음"이 있는 예를 보여줍니다.

![결과](./media/documentdb-change-notification/logic-app-run.png)

## <a name="metrics"></a>메트릭
포털에서 기본 논리 앱에 대한 모니터링을 구성할 수 있습니다. 그러면 다음 그림처럼 실행 대기 시간 및 기타 이벤트를 볼 수 있습니다.

![](./media/documentdb-change-notification/metrics.png)

## <a name="docdb-trigger"></a>DocDb 트리거
이 논리 앱은 기본 논리 앱에서 워크플로를 시작하는 트리거입니다.

다음 그림에서는 디자이너 뷰를 보여줍니다.

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

    {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
        "actions": {
        "Http": {
            "conditions": [],
            "inputs": {
            "body": {
                "EmailTo": "XXXXXX@XXXXX.net",
                "GetUtcDate_HoursBack": "24",
                "Subject": "New Patients",
                "sendgridPassword": "********",
                "sendgridUsername": "azureuser@azure.com"
            },
            "method": "POST",
            "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
            },
            "type": "Http"
        }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {
        "Results": {
            "type": "String",
            "value": "@{body('Http')['status']}"
        }
        },
        "parameters": {},
        "triggers": {
        "recurrence": {
            "recurrence": {
            "frequency": "Hour",
            "interval": 24
            },
            "type": "Recurrence"
        }
        }
    }

```

트리거는 24시간 되풀이에 대해 설정됩니다. 작업은 기본 논리 앱에 대한 콜백 URL을 사용하는 HTTP POST입니다. 본문에는 JSON 스키마에 지정된 매개 변수가 포함됩니다. 

#### <a name="operations"></a>작업
##### <a name="request"></a>요청
```JSON

    {
        "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
        "method": "POST",
        "body": {
        "EmailTo": "XXXXXX@XXXXX.net",
        "GetUtcDate_HoursBack": "24",
        "Subject": "New Patients",
        "sendgridPassword": "********",
        "sendgridUsername": "azureuser@azure.com"
        }
    }

```

##### <a name="response"></a>응답
```JSON

    {
        "statusCode": 202,
        "headers": {
        "pragma": "no-cache",
        "x-ms-ratelimit-remaining-workflow-writes": "7486",
        "x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
        "x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
        "cache-Control": "no-cache",
        "date": "Thu, 25 Feb 2016 21:01:06 GMT"
        }
    }
```

이제 API 앱을 살펴보겠습니다.

## <a name="docdbnotificationapi"></a>DocDBNotificationApi
앱에 여러 작업이 있지만 세 개만 사용할 예정입니다.

* GetUTCDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### <a name="docdbnotificationapi-operations"></a>DocDBNotificationApi 작업
Swagger 설명서를 살펴보겠습니다.

> [!NOTE]
> 작업을 외부로 호출하도록 하려면 다음 그림처럼 API 앱의 설정에서 CORS 허용 원본 값 "*"(따옴표 없이)을 추가해야 합니다.
> 
> 

![Cors 구성](./media/documentdb-change-notification/cors.png)

#### <a name="getutcdate"></a>GetUTCDate
![G](./media/documentdb-change-notification/getutcdateswagger.png)

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
![UTC 날짜 가져오기](./media/documentdb-change-notification/converion-swagger.png)

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
![쿼리](./media/documentdb-change-notification/patientswagger.png)

이 작업 뒤에 있는 코드를 살펴보겠습니다.

#### <a name="getutcdate"></a>GetUTCDate
```C#

    /// <summary>
    /// Gets the current UTC Date value
    /// </summary>
    /// <returns></returns>
    [H ttpGet]
    [Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
    [SwaggerOperation("GetUtcDate")]
    [SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
    [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
    public string GetUtcDate(
       [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
    {


        return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
    }
```

이 작업은 단순히 현재 UTC DateTime에서 HoursBack 값을 뺀 값을 반환합니다.

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

이 작업은 GetUtcDate 작업에서 double 값으로 응답을 변환합니다.

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
```C#

        /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
    }

```

이 작업은 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 를 사용하여 문서 쿼리를 만듭니다. 

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

ConvertToTimeStamp 작업(unixTimeStamp)의 응답이 전달됩니다. 이 작업은 문서 목록 `IList<Document>`를 반환합니다.

이전에 콜백 URL에 대해 언급했습니다. 기본 논리 앱에서 워크플로를 시작하려면 콜백 URL을 사용하여 호출해야 합니다.

## <a name="callbackurl"></a>콜백 URL
시작하려면 Azure AD 토큰이 필요합니다.  이 토큰을 가져오기가 어려울 수 있습니다. 간편한 방법을 찾고 있었으며 Azure 논리 앱 프로그램 관리자인 Jeff Hollan이 PowerShell에서 [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) 를 사용하는 방법을 권장했습니다.  제공된 지침에 따라 설치할 수 있습니다.

사용하려는 작업은 로그인 및 ARM API 호출입니다.

로그인: Azure 포털에 로그인하는 데 동일한 자격 증명을 사용합니다. 

ARM Api 호출 작업은 콜백 URL을 생성할 작업입니다.

PowerShell에서 다음과 같이 호출합니다.    

```powershell

    ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

결과는 다음과 같습니다.

```powershell

    https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

[postman](http://www.getpostman.com/) 과 같은 도구를 사용하여 다음 그림과 같이 기본 논리 앱을 테스트할 수 있습니다.

![postman](./media/documentdb-change-notification/newpostman.png)

다음 표에서는 DocDB 트리거 논리 앱의 본문을 구성하는 트리거 매개 변수를 나열합니다.

| 매개 변수 | 설명 |
| --- | --- |
| GetUtcDate_HoursBack |검색 시작 날짜에 대한 시간을 설정하는 데 사용 |
| sendgridUsername |검색 시작 날짜에 대한 시간을 설정하는 데 사용 |
| sendgridPassword |SendGrid 전자 메일에 대한 사용자 이름 |
| EmailTo |전자 메일 알림을 받을 전자 메일 주소 |
| 제목 |전자 메일 제목 |

## <a name="viewing-the-patient-data-in-the-azure-blob-service"></a>Azure Blob 서비스에서 환자 데이터 보기
Azure 저장소 계정으로 이동한 후 다음 그림에 나와 있는 것처럼 서비스 아래에서 Blob를 선택합니다.

![저장소 계정](./media/documentdb-change-notification/docdbstorageaccount.png) 

아래와 같이 환자 Blob 파일 정보를 볼 수 있습니다.

![Blob 서비스](./media/documentdb-change-notification/blobservice.png)

## <a name="summary"></a>요약
이 연습에서는 다음을 알아보았습니다.

* DocumentDB에서 알림을 구현할 수 있습니다.
* 논리 앱을 사용하여 프로세스를 자동화할 수 있습니다.
* 논리 앱을 사용하여 응용 프로그램을 제공하는 데 걸리는 시간을 줄일 수 있습니다.
* HTTP를 사용하여 논리 앱 내에서 API 앱을 쉽게 사용할 수 있습니다.
* HTTP 수신기를 대체하는 콜백 URL을 쉽게 만들 수 있습니다.
* 논리 앱 디자이너에서 사용자 지정 워크플로를 쉽게 만들 수 있습니다.

워크플로를 미리 계획하고 모델링하는 것이 중요합니다.

## <a name="next-steps"></a>다음 단계
[GitHub](https://github.com/HEDIDIN/DocDbNotifications)에 제공된 논리 앱 코드를 다운로드하여 사용하세요. 이제 응용 프로그램에 빌드하고 리포지토리에 변경 내용을 제출해보세요. 

DocumentDB에 대해 자세히 알아보려면 [학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/)를 참조하세요.


