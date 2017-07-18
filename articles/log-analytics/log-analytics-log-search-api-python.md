---
title: "Azure Log Analytics에서 데이터를 검색하는 Python 스크립트 | Microsoft Docs"
description: "모든 REST API 클라이언트는 Log Analytics 로그 검색 API를 통해 Log Analytics 작업 영역에서 데이터를 검색할 수 있습니다.  이 문서에서는 로그 검색 API를 사용하는 Python 스크립트 예제를 제공합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 56d7c6dc648a01e7b0efc167cb65c94bac5468ec
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017


---

# <a name="retrieve-data-from-log-analytics-with-a-python-script"></a>Python 스크립트로 Log Analytics에서 데이터 검색
모든 REST API 클라이언트는 [Log Analytics 로그 검색 API](log-analytics-log-search-api.md)를 통해 Log Analytics 작업 영역에서 데이터를 검색할 수 있습니다.  이 문서에서는 Log Analytics 로그 검색 API를 사용하는 Python 스크립트 예제를 보여 줍니다.  

## <a name="authentication"></a>인증
이 스크립트는 Azure Active Directory에서 서비스 주체를 사용하여 작업 영역을 인증합니다.  클라이언트 응용 프로그램은 서비스 주체를 통해 클라이언트에 계정이 없는 경우에도 서비스가 계정을 인증하도록 요청합니다. 이 스크립트를 실행하기 전에, [포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 응용 프로그램 및 서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md)에 나와 있는 프로세스를 사용하여 서비스 주체를 만들어야 합니다.  이 스크립트에 응용 프로그램 ID, 테넌트 ID 및 인증 키를 제공해야 합니다. 

> [!NOTE]
> [Azure Automation 계정을 만드는](../automation/automation-create-standalone-account.md) 경우 이 스크립트에 사용하기에 적합한 서비스 주체가 생성됩니다.  Azure Automation에서 생성된 서비스 주체가 이미 있는 경우 새 주체를 만드는 대신 이미 있는 것을 사용할 수 있습니다. 아직 없는 경우에는 [인증 키를 만들어야](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) 할 수 있습니다.

## <a name="script"></a>스크립트
``` python
import adal
import requests
import json
import datetime
from pprint import pprint

# Details of workspace.  Fill in details for your workspace.
resource_group = 'xxxxxxxx'
workspace = 'xxxxxxxx'

# Details of query.  Modify these to your requirements.
query = "Type=Event"
end_time = datetime.datetime.utcnow()
start_time = end_time - datetime.timedelta(hours=24)
num_results = 100  # If not provided, a default of 10 results will be used.

# IDs for authentication.  Fill in values for your service principal.
subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
tenant_id = 'xxxxxxxx-xxxx-xxxx-xxx-xxxxxxxxxxxx'
application_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx'
application_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

# URLs for authentication
authentication_endpoint = 'https://login.microsoftonline.com/'
resource  = 'https://management.core.windows.net/'

# Get access token
context = adal.AuthenticationContext('https://login.microsoftonline.com/' + tenant_id)
token_response = context.acquire_token_with_client_credentials('https://management.core.windows.net/', application_id, application_key)
access_token = token_response.get('accessToken')

# Add token to header
headers = {
    "Authorization": 'Bearer ' + access_token,
    "Content-Type":'application/json'
}

# URLs for retrieving data
uri_base = 'https://management.azure.com'
uri_api = 'api-version=2015-11-01-preview'
uri_subscription = 'https://management.azure.com/subscriptions/' + subscription_id
uri_resourcegroup = uri_subscription + '/resourcegroups/'+ resource_group
uri_workspace = uri_resourcegroup + '/providers/Microsoft.OperationalInsights/workspaces/' + workspace
uri_search = uri_workspace + '/search'

# Build search parameters from query details
search_params = {
        "query": query,
        "top": num_results,
        "start": start_time.strftime('%Y-%m-%dT%H:%M:%S'),
        "end": end_time.strftime('%Y-%m-%dT%H:%M:%S')
        }

# Build URL and send post request
uri = uri_search + '?' + uri_api
response = requests.post(uri,json=search_params,headers=headers)

# Response of 200 if successful
if response.status_code == 200:

    # Parse the response to get the ID and status
    data = response.json()
    search_id = data["id"].split("/")
    id = search_id[len(search_id)-1]
    status = data["__metadata"]["Status"]

    # If status is pending, then keep checking until complete
    while status == "Pending":

        # Build URL to get search from ID and send request
        uri_search = uri_search + '/' + id
        uri = uri_search + '?' + uri_api
        response = requests.get(uri,headers=headers)

        # Parse the response to get the status
        data = response.json()
        status = data["__metadata"]["Status"]

else:

    # Request failed
    print (response.status_code)
    response.raise_for_status()

print ("Total records:" + str(data["__metadata"]["total"]))
print ("Returned top:" + str(data["__metadata"]["top"]))
pprint (data["value"])
```
## <a name="next-steps"></a>다음 단계
- [Log Analytics 로그 검색 API](log-analytics-log-search-api.md)에 대해 자세히 알아보세요.
