<properties 
    pageTitle="논리 앱에서 로깅 및 오류 처리 | Microsoft Azure" 
    description="논리 앱과 함께 고급 오류 처리 및 로깅의 실제 사용 사례 보기" 
    keywords=""
    services="logic-apps" 
    authors="hedidin" 
    manager="" 
    editor="" 
    documentationCenter=""/>
    
<tags 
    ms.service="logic-apps" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/29/2016" 
    ms.author="b-hoedid"/>
    
# 논리 앱에서 로깅 및 오류 처리 

이 문서에서는 논리 앱을 확장하여 예외 처리를 더 잘 지원할 수 있는 방법에 대한 세부 정보를 알아봅니다. 실제 사용 사례 및 **논리 앱이 예외 및 오류 처리를 지원하나요?**라는 질문에 대한 대답입니다.

>[AZURE.NOTE] 논리 앱의 현재 버전은 작업 응답에 대한 표준 템플릿을 제공합니다. API 앱에서 반환된 내부 유효성 검사 및 오류 응답을 모두 포함합니다.

## 사용 사례 및 시나리오의 개요

다음 스토리는 이 문서에 대한 사용 사례입니다.

> 유명한 의료 조직이 Dynamics CRMOL(CRM Online)을 사용하여 환자 포털을 만들기 위해 Azure 솔루션을 만드는 데 참여했습니다. 현재 Salesforce를 사용하고 있으며 CRMOL 환자 포털과 Salesforce 간에 예약 기록을 보내야 했습니다. 모든 환자 기록에 [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) 표준을 사용하도록 요청을 받았습니다.

> 포함해야 하는 두 개의 주요 요구 사항은 다음과 같았습니다.
> -  CRMOL 포털에서 전송한 기록의 로깅
> -  워크플로 내에서 발생한 오류를 보는 방법이 필요했습니다.


## 문제를 해결한 방법

>[AZURE.TIP] [통합 사용자 그룹](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "통합 사용자 그룹")에 대한 높은 수준의 비디오를 볼 수 있습니다.

**[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Azure DocumentDB")**를 로그 및 오류 기록에 대한 리포지토리로 사용하기로 했습니다. (DocumentDB에서는 기록을 문서로 참조합니다.) 논리 앱에 모든 응답에 대한 표준 템플릿이 있으므로 사용자 지정 스키마를 만들 필요가 없습니다. 오류 및 로그 기록에 대한 삽입 및 쿼리에 API 앱을 만들 수 있습니다. API 앱 내에서 각각에 대한 스키마를 정의할 수도 있습니다.

어느 날 이후 기록을 제거하는 또 다른 요구 사항을 받았습니다. DocumentDB에는 TTL([Time-To-Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time-To-Live"))속성이 있으며 이를 사용하면 각 기록 또는 전체 컬렉션에 **Time-to-Live** 값을 설정할 수 있습니다. DocumentDB에서 기록을 수동으로 삭제할 필요성이 없어집니다.

### 접근 방식

첫 번째 단계는 논리 앱을 너무 만들고 디자이너에 로드합니다. 이 예제에서는 부모-자식 논리 앱을 사용합니다. 부모를 이미 만들었다고 가정해 보겠습니다. 여기서는 하나의 자식 논리 앱을 만들 예정입니다.

CRMOL에서 나오는 기록을 기록해야 하므로 위쪽에서 시작하겠습니다. 부모 논리 앱이 이 자식을 트리거하므로 요청 트리거를 사용해야 합니다.

> [AZURE.IMPORTANT] 이 자습서를 완료하기 위해 DocumentDB 데이터베이스와 두 개의 컬렉션(로깅 및 오류)을 만들어야 합니다.

### 논리 앱 트리거 

**아래와 같이 요청 트리거를 사용하고 있습니다.**

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### 단계 

로깅을 사용하여 시작해 보겠습니다. CRMOL 포털에서 환자 기록의 원본(요청)에 로그인해야 합니다.

1. 먼저 CRMOL에서 **새 예약 기록을 가져와야** 합니다. CRM에서 오는 트리거는 **CRM PatentId**, **기록 종류**, **신규 또는 업데이트된 기록**(새로운 또는 업데이트된 부울 값) 및 **SalesforceId**를 제공합니다. 업데이트를 위해서만 사용되기 때문에 SalesforceId는 null일 수 있습니다. CRM PatientID 및 기록 종류를 사용하여 CRM 기록을 얻게 됩니다.
1. 다음으로 DocumentDB API 앱 **InsertLogEntry** 작업을 아래 그림에 나와 있는 것처럼 추가해야 합니다.


#### 로그 항목 디자이너 보기 삽입

![로그 항목 삽입](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### 오류 항목 디자이너 보기 삽입
![로그 항목 삽입](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### 조건 - 기록 만들기 실패에 대한 확인 

![조건](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## 논리 앱 소스 코드 

>[AZURE.NOTE]  다음은 예제만 해당합니다. 자습서가 현재 프로덕션에서 실제 구현에 따라 다른 특성으로 인해 **원본 노드**의 값은 예약 일정에 관련된 속성을 표시하지 않을 수 있습니다.

### 로깅
다음 논리 앱 코드 샘플은 로그를 처리하는 방법을 보여 줍니다.

#### 로그 항목 삽입
로그 항목을 삽입하기 위한 논리 앱 소스 코드입니다.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### 로그 요청

API 앱에 게시된 로그 요청 메시지입니다.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
	    "date": "Fri, 10 Jun 2016 22:31:56 GMT",
	    "operation": "New Patient",
	    "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "providerId": "",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}"
    	}
    }
    
```


#### 로그 응답

API 앱의 로그 응답 메시지입니다.

``` json
{
    "statusCode": 200,
    "headers": {
	    "Pragma": "no-cache",
	    "Cache-Control": "no-cache",
	    "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
	    "Server": "Microsoft-IIS/8.0",
	    "X-AspNet-Version": "4.0.30319",
	    "X-Powered-By": "ASP.NET",
	    "Content-Length": "964",
	    "Content-Type": "application/json; charset=utf-8",
	    "Expires": "-1"
    },
    "body": {
	    "ttl": 2592000,
	    "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
	    "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
	    "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
	    "_ts": 1465597936,
	    "_etag": ""0400fc2f-0000-0000-0000-575b3ff00000"",
	    "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "timestamp": "2016-06-10T22:31:56Z",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}",
	    "operation": "New Patient",
	    "salesforceId": "",
	    "expired": false
    }
}
    
```

이제 오류 처리 단계를 살펴보겠습니다.

----------    
    
### 오류 처리

다음 논리 앱 코드 샘플은 오류 처리를 구현할 수 있는 방법을 보여 줍니다.

#### 오류 기록 만들기

오류 기록을 만들기 위한 논리 앱 소스 코드입니다.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter": 
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}  	       
```

#### DocumentDB의 삽입 오류 - 요청

``` json
  
{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MasterID_mp__c":"","C_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","ONY_ID__c":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "statusCode": "400"
    }
}
```

#### DocumentDB의 삽입 오류 - 응답


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": ""0c00eaac-0000-0000-0000-575b3fdc0000"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c":"DO - Degree level is DO","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MterID_mp__c":"","Medicis_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","XXXXXXX":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### Salesforce 오류 응답

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}
    
```

### 부모 논리 앱에 응답 반환

응답을 설정하면 부모 논리 앱으로 다시 전달할 수 있습니다.

#### 부모 논리 앱에 성공 응답 반환

``` json
"SuccessResponse": {
    "runAfter": 
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "	Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### 부모 논리 앱에 오류 응답 반환

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}
  
```

----------
  
## DocumentDB 리포지토리 및 포털

[DocumentDB](https://azure.microsoft.com/services/documentdb)에 추가 기능을 추가한 솔루션

### 오류 관리 포털

오류를 보기 위해 DocumentDB에서 오류 기록을 표시하는 MVC 웹앱을 만들 수 있습니다. 현재 버전은 **목록**, **세부 정보**, **편집** 및 **삭제** 작업이 포함됩니다.

> [AZURE.NOTE] 편집 작업: DocumentDB는 전체 문서 바꾸기를 수행하지 않습니다. 목록 및 세부 정보 보기에 표시된 기록은 예제만 해당됩니다. 실제 환자 예약 기록이 없습니다
        
다음은 위의 접근 방식을 사용한 MVC 앱 세부 정보의 예입니다.

#### 오류 관리 목록

![오류 목록](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)
        
#### 오류 관리 세부 정보 보기

![오류 세부 정보](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### 로그 관리 포털

로그를 보기 위해 MVC 웹앱도 만들었습니다. 다음은 위의 접근 방식을 사용한 MVC 앱 세부 정보의 예입니다.

#### 샘플 로그 세부 정보 보기

![로그 세부 정보 보기](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)
    
### API 앱 세부 정보

#### 논리 앱 예외 관리 API

오픈 소스 논리 앱 예외 관리 API 앱은 다음 기능을 제공했습니다.

두 개의 컨트롤러가 있습니다.
- ErrorController는 DocumentDB 컬렉션에 오류 기록(문서)을 삽입합니다.
- LogController는 DocumentDB 컬렉션에 로그 기록(문서)을 삽입합니다.

> [AZURE.TIP] 두 컨트롤러를 모두 `async Task<dynamic>` 작업을 사용합니다. 이를 통해 작업의 본문에서 DocumentDB 스키마를 만들 수 있도록 작업이 런타임 시 해결되어야 합니다.

DocumentDB의 모든 문서에는 고유한 ID가 있어야 합니다. `PatientId`을 사용하고 Unix 타임스탬프 값(double)으로 변환되는 타임스탬프를 추가합니다. 잘라서 소수 자릿수 값을 제거합니다.

[여기 GitHub에서](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic 앱 예외 관리 API/컨트롤러/ErrorController.cs) 오류 컨트롤러 API의 소스 코드를 볼 수 있습니다

다음 구문을 사용하여 논리 앱에서 API를 호출합니다.

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

위의 코드 예제에서 식은 **실패**한 *Create\_NewPatientRecord* 상태를 확인합니다.

## 요약 

- 논리 앱에서 로깅 및 오류 처리를 쉽게 구현할 수 있습니다.
- DocumentDB를 로그 및 오류 기록(문서)에 대한 저장소로 활용할 수 있습니다.
- 로그 및 오류 기록을 표시하려면 포털을 만들려면 MVC를 사용할 수 있습니다.

### 소스 코드
논리 앱 예외 관리 API 응용 프로그램에 대한 소스 코드는 이 [GitHub 리포지토리](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "논리 앱 예외 관리 API")에서 사용할 수 있습니다.


## 다음 단계 
- [논리 앱 예제 및 시나리오 더 보기](app-service-logic-examples-and-scenarios.md)
- [논리 앱 모니터링 도구에 대해 알아보기](app-service-logic-monitor-your-logic-apps.md)
- [논리 앱 자동화횐 배포 템플릿 만들기](app-service-logic-create-deploy-template.md)

<!---HONumber=AcomDC_0803_2016-->