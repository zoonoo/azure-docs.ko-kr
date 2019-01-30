---
title: REST API를 사용하여 Azure 리소스에 대한 사용자 지정 메트릭을 Azure Monitor 메트릭 저장소로 보내기
description: REST API를 사용하여 Azure 리소스에 대한 사용자 지정 메트릭을 Azure Monitor 메트릭 저장소로 보내기
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: ed810726a0709c80034412eba437c05e76f65758
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54460382"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>REST API를 사용하여 Azure 리소스에 대한 사용자 지정 메트릭을 Azure Monitor 메트릭 저장소로 보내기

이 문서에서는 REST API를 통해 Azure 리소스에 대한 사용자 지정 메트릭을 Azure Monitor 메트릭 저장소로 보내는 방법을 보여 줍니다. Azure Monitor에 메트릭이 있으면 표준 메트릭으로 수행하는 모든 작업을 해당 메트릭으로 수행할 수 있습니다. 예를 들어, 차트 작성, 경고, 다른 외부 도구로 라우팅 등이 있습니다.  

>[!NOTE]  
>REST API는 Azure 리소스에 대한 사용자 지정 메트릭 보내기만 허용합니다. 다른 환경이나 온-프레미스 리소스에 대한 메트릭을 보내려면 [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md)를 사용할 수 있습니다.    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>서비스 주체를 만들고 메트릭 내보내기 권한 부여 

[서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 지침을 사용하여 Azure Active Directory 테넌트에 서비스 주체를 만듭니다. 

이 프로세스를 진행하는 동안 다음 사항에 유의하세요. 

- 로그인 URL에 대한 URL을 입력할 수 있습니다.  
- 이 앱에 대한 새 클라이언트 암호를 만듭니다.  
- 이후 단계에서 사용하기 위해 키와 클라이언트 ID를 저장합니다.  

1단계에서 생성된 앱에 메트릭을 내보내려는 리소스에 대한 모니터링 메트릭 게시자 권한을 부여합니다. 앱을 사용하여 여러 리소스에 대한 사용자 지정 메트릭을 내보내려는 경우 리소스 그룹 또는 구독 수준에서 이러한 권한을 부여할 수 있습니다. 

## <a name="get-an-authorization-token"></a>권한 부여 토큰 가져오기
명령 프롬프트를 열고 다음 명령을 실행합니다.

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
응답에서 액세스 토큰을 저장합니다.

![액세스 토큰](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>REST API를 통해 메트릭을 내보냅니다. 

1. 다음 JSON을 파일에 붙여넣고 로컬 컴퓨터에  **custommetric.json**으로 저장합니다. JSON 파일의 시간 매개 변수를 업데이트합니다. 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. 명령 프롬프트 창에서 메트릭 데이터를 게시합니다. 
    - **azureRegion**. 메트릭을 내보내는 리소스의 배포 지역과 일치해야 합니다. 
    - **resourceID**.  메트릭을 추적 중인 Azure 리소스의 리소스 ID입니다.  
    - **AccessToken**. 이전에 확보한 토큰을 붙여넣습니다.

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. JSON 파일의 타임스탬프 및 값을 변경합니다. 
1. 이전 두 단계를 몇 번 반복하여 몇 분간의 데이터를 저장합니다.

## <a name="troubleshooting"></a>문제 해결 
프로세스의 일부로 오류 메시지가 수신되면 다음 문제 해결 정보를 고려합니다.

1. 구독 또는 리소스 그룹에 대한 메트릭을 Azure 리소스로 실행할 수 없습니다. 
1. 20분 이상 경과된 메트릭을 저장소에 저장할 수 없습니다. 메트릭 저장소는 경고 및 실시간 차트 작성에 최적화되어 있습니다. 
2. 차원 이름 수는 값과 일치해야 하고, 그 반대의 경우도 마찬가지입니다. 값을 확인합니다. 
2. 사용자 지정 메트릭을 지원하지 않는 지역에 대해 메트릭을 내보내는 경우가 발생할 수 있습니다. [지원되는 지역](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions)을 참조하세요. 



## <a name="view-your-metrics"></a>메트릭 보기 

1. Azure 포털에 로그인합니다. 

1. 왼쪽 메뉴에서 **모니터**를 선택합니다. 

1. **모니터** 페이지에서 **메트릭**을 선택합니다. 

   ![메트릭 선택](./media/metrics-store-custom-rest-api/metrics.png) 

1. 집계 기간을 **지난 30분**으로 변경합니다.  

1. **리소스** 드롭다운 메뉴에서 메트릭을 내보낸 리소스를 선택합니다.  

1. **네임스페이스** 드롭다운 메뉴에서 **QueueProcessing**을 선택합니다. 

1. **메트릭** 드롭다운 메뉴에서 **QueueDepth**를 선택합니다.  

 
## <a name="next-steps"></a>다음 단계
- [사용자 지정 메트릭](../../azure-monitor/platform/metrics-custom-overview.md)에 대해 자세히 알아보세요.

