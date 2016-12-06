리소스 그룹에 태그를 추가하려면 **azure group 집합**을 사용합니다. 리소스 그룹에 기존 태그가 없다면 태그에 전달합니다.

    azure group set -n tag-demo-group -t Dept=Finance

태그는 전체적으로 업데이트됩니다. 기존 태그가 있는 리소스 그룹에 태그를 추가하려면 모든 태그를 전달합니다. 

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade

태그는 리소스 그룹에 있는 리소스에 의해 상속되지 않습니다. 리소스에 태그를 추가하려면 **azure group 집합**을 사용합니다. 태그를 추가하는 리소스 유형의 경우에는 API 버전 번호를 전달해야 합니다. API 버전을 검색해야 할 경우 설정하는 유형에 대해 리소스 공급자와 함께 다음 명령을 사용합니다.

    azure provider show -n Microsoft.Storage --json

결과에서 원하는 리소스 유형을 찾아봅니다.

    "resourceTypes": [
    {
      "resourceType": "storageAccounts",
      ...
      "apiVersions": [
        "2016-01-01",
        "2015-06-15",
        "2015-05-01-preview"
      ]
    }
    ...

이제 API 버전, 리소스 그룹 이름, 리소스 이름, 리소스 유형 및 태그 값을 매개 변수로 제공합니다.

    azure resource set -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -t Dept=Finance -o 2016-01-01

태그는 리소스 및 리소스 그룹에 직접 존재합니다. 기존 태그를 보려면 단지 **azure group show**를 사용하여 리소스 그룹과 해당 리소스를 가져오면 됩니다.

    azure group show -n tag-demo-group --json

리소스 그룹에 적용된 태그와 함께 리소스 그룹에 대한 메타데이터를 반환합니다.

    {
      "id": "/subscriptions/4705409c-9372-42f0-914c-64a504530837/resourceGroups/tag-demo-group",
      "name": "tag-demo-group",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "location": "southcentralus",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production",
        "Project": "Upgrade"
      },
      ...

특정 리소스에 대한 태그를 **azure resource show**를 사용하여 볼 수 있습니다.

    azure resource show -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -o 2016-01-01 --json

어떤 태그 값을 가진 모든 리소스를 검색하려면 다음을 사용합니다.

    azure resource list -t Dept=Finance --json

어떤 태그 값을 가진 모든 리소스를 검색하려면 다음을 사용합니다.

    azure group list -t Dept=Finance

구독에 있는 기존 태그는 다음 명령 사용하여 볼 수 있습니다.

    azure tag list



<!--HONumber=Nov16_HO3-->


