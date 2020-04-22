---
title: Azure 서비스 패브릭 응용 프로그램 리소스 모델
description: 이 문서에서는 Azure 리소스 관리자를 사용하여 Azure 서비스 패브릭 응용 프로그램 관리에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682638"
---
# <a name="service-fabric-application-resource-model"></a>서비스 패브릭 응용 프로그램 리소스 모델

서비스 패브릭 클러스터에 Azure 서비스 패브릭 응용 프로그램을 배포하기 위한 여러 옵션이 있습니다. Azure 리소스 관리자를 사용하는 것이 좋습니다. 리소스 관리자를 사용하는 경우 JSON의 응용 프로그램 및 서비스를 설명한 다음 클러스터와 동일한 리소스 관리자 템플릿에 배포할 수 있습니다. PowerShell 또는 Azure CLI를 사용하여 응용 프로그램을 배포하고 관리하는 것과 달리 리소스 관리자를 사용하는 경우 클러스터가 준비될 때까지 기다릴 필요가 없습니다. 응용 프로그램 등록, 프로비저닝 및 배포는 모두 한 단계로 이루어질 수 있습니다. 리소스 관리자를 사용하는 것이 클러스터의 응용 프로그램 수명 주기를 관리하는 가장 좋은 방법입니다. 자세한 내용은 [모범 사례: 인프라를 코드로](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)참조하세요.

리소스 관리자에서 리소스로 응용 프로그램을 관리하면 다음 영역에서 개선사항을 얻을 수 있습니다.

* 감사 추적: 리소스 관리자는 모든 작업을 감사하고 자세한 활동 로그를 유지합니다. 활동 로그를 사용하면 응용 프로그램과 클러스터에 대한 변경 내용을 추적할 수 있습니다.
* 역할 기반 액세스 제어: 동일한 리소스 관리자 템플릿을 사용하여 클러스터 및 클러스터에 배포된 응용 프로그램에 대한 액세스를 관리할 수 있습니다.
* 관리 효율성: 리소스 관리자를 사용하면 클러스터 및 중요한 응용 프로그램 배포를 관리하기 위한 단일 위치(Azure portal)를 제공합니다.

이 문서에서는 다음 방법을 배웁니다.

> [!div class="checklist"]
>
> * 리소스 관리자를 사용하여 응용 프로그램 리소스를 배포합니다.
> * 리소스 관리자를 사용하여 응용 프로그램 리소스를 업그레이드합니다.
> * 응용 프로그램 리소스를 삭제합니다.

## <a name="deploy-application-resources"></a>애플리케이션 리소스 배포

Resource Manager 응용 프로그램 리소스 모델을 사용하여 응용 프로그램 및 해당 서비스를 배포하는 데 걸리는 상위 단계는 다음과 같습니다.
1. 응용 프로그램 코드를 패키지로 합니다.
1. 패키지를 업로드합니다.
1. 리소스 관리자 템플릿에서 패키지 위치를 응용 프로그램 리소스로 참조합니다. 

자세한 내용은 [응용 프로그램 패키지](service-fabric-package-apps.md#create-an-sfpkg)를 참조하십시오.

그런 다음 리소스 관리자 템플릿을 만들고, 응용 프로그램 세부 정보로 매개 변수 파일을 업데이트하고, 서비스 패브릭 클러스터에 템플릿을 배포합니다. [샘플을 탐색합니다.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

리소스 관리자 템플릿에서 응용 프로그램을 배포하려면 저장소 계정이 있어야 합니다. 저장소 계정은 응용 프로그램 이미지를 스테이지화하는 데 사용됩니다. 

기존 저장소 계정을 다시 사용하거나 응용 프로그램을 스테이징하기 위해 새 저장소 계정을 만들 수 있습니다. 기존 저장소 계정을 사용하는 경우 이 단계를 건너뛸 수 있습니다. 

![스토리지 계정 만들기][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>저장소 계정 구성

저장소 계정을 만든 후 응용 프로그램을 스테이징할 수 있는 Blob 컨테이너를 만듭니다. Azure 포털에서 응용 프로그램을 저장할 Azure 저장소 계정으로 이동합니다. Blob 컨테이너**추가**를 **선택합니다.** >  

공용 액세스 수준을 **비공개로**설정하여 클러스터의 리소스를 보호할 수 있습니다. 다음과 같은 여러 가지 방법으로 액세스 권한을 부여할 수 있습니다.

* [Azure Active Directory](../storage/common/storage-auth-aad-app.md)를 사용하여 Blob 및 큐에 대한 액세스 권한을 부여합니다.
* [Azure 포털에서 RBAC를](../storage/common/storage-auth-aad-rbac-portal.md)사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한을 부여합니다.
* [공유 액세스 서명을](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)사용하여 액세스 권한을 위임합니다.

다음 스크린샷의 예제에서는 Blob에 대한 익명 읽기 액세스를 사용합니다.

![Blob 만들기][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>저장소 계정에서 응용 프로그램 단계

응용 프로그램을 배포하려면 먼저 Blob 저장소에서 응용 프로그램을 단계화해야 합니다. 이 자습서에서는 응용 프로그램 패키지를 수동으로 만듭니다. 이 단계를 자동화할 수 있습니다. 자세한 내용은 [응용 프로그램 패키지](service-fabric-package-apps.md#create-an-sfpkg)를 참조하십시오. 

이 자습서에서는 투표 [샘플 응용 프로그램을](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)사용합니다.

1. Visual Studio에서 **투표** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **패키지를**선택합니다.

   ![패키지 응용 프로그램][PackageApplication]  
1. *.\서비스 패브릭-도트넷-퀵스타트\Voting\pkg\디버그* 디렉토리로 이동합니다. 내용을 *Voting.zip이라는*파일로 압축합니다. *ApplicationManifest.xml* 파일은 zip 파일의 루트에 있어야 합니다.

   ![지퍼 응용 프로그램][ZipApplication]  
1. 파일 이름을 변경하여 확장명을 .zip에서 *.sfpkg으로*변경합니다.

1. Azure 포털에서 저장소 계정에 대한 **앱** 컨테이너에서 **업로드를**선택한 다음 **Voting.sfpkg**을 업로드합니다. 

   ![앱 패키지 업로드][UploadAppPkg]

이제 응용 프로그램이 준비되고 리소스 관리자 템플릿을 만들어 응용 프로그램을 배포할 수 있습니다.

### <a name="create-the-resource-manager-template"></a>리소스 관리자 템플릿 만들기

샘플 응용 프로그램에는 응용 프로그램을 배포하는 데 사용할 수 [있는 Azure 리소스 관리자 템플릿이](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) 포함되어 있습니다. 템플릿 파일 이름은 *UserApp.json* 및 *UserApp.Parameters.json*입니다.

> [!NOTE]
> *UserApp.Parameters.json* 파일은 클러스터 이름으로 업데이트되어야 합니다.
>
>

| 매개 변수              | Description                                 | 예제                                                      | 주석                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 배포할 클러스터의 이름 | sf-클러스터123                                                |                                                              |
| 애플리케이션            | 애플리케이션의 이름                 | Voting                                                       |
| 응용 프로그램유형 이름    | 응용 프로그램의 형식 이름           | 투표 유형                                                   | 응용 프로그램 매니페스트.xml일치해야 합니다.                 |
| 응용 프로그램유형버전 | 응용 프로그램 형식의 버전         | 1.0.0                                                        | 응용 프로그램 매니페스트.xml일치해야 합니다.                 |
| serviceName            | 서비스 이름         | 투표~투표웹                                             | 형식에 있어야 합니다 응용 프로그램 이름 ~ 서비스 유형            |
| 서비스 유형 이름        | 서비스의 형식 이름                | VotingWeb                                                    | 서비스 매니페스트.xml과 일치해야 합니다.                 |
| 앱패키지Url          | 응용 프로그램의 Blob 저장소 URL     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Blob 저장소의 응용 프로그램 패키지의 URL(URL을 설정하는 절차는 문서의 후반부설명) |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>애플리케이션 배포

새 **AzResourceGroupDeploy** cmdlet을 실행하여 클러스터를 포함하는 리소스 그룹에 응용 프로그램을 배포합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>리소스 관리자를 사용하여 서비스 패브릭 응용 프로그램 업그레이드

다음 이유 중 하나로 서비스 패브릭 클러스터에 이미 배포된 응용 프로그램을 업그레이드할 수 있습니다.

* 새 서비스가 응용 프로그램에 추가됩니다. 서비스가 응용 프로그램에 추가될 때 서비스 정의는 *service-manifest.xml* 및 *응용 프로그램 매니페스트.xml* 파일에 추가되어야 합니다. 응용 프로그램의 새 버전을 반영하려면 [UserApp.Parameters.json에서](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)응용 프로그램 형식 버전을 1.0.0에서 1.0.1로 변경해야 합니다.

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* 기존 서비스의 새 버전이 응용 프로그램에 추가됩니다. 예를 들어 응용 프로그램 코드 변경 및 앱 유형 버전 및 이름에 대한 업데이트가 있습니다. 이 업그레이드의 경우 다음과 같이 UserApp.Parameters.json을 업데이트하십시오.

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>응용 프로그램 리소스 삭제

리소스 관리자에서 응용 프로그램 리소스 모델을 사용하여 배포된 응용 프로그램을 삭제하려면 다음을 수행합니다.

1. [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) cmdlet을 사용하여 응용 프로그램의 리소스 ID를 가져옵니다.

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. [제거-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) cmdlet을 사용하여 응용 프로그램 리소스를 삭제합니다.

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>다음 단계

응용 프로그램 리소스 모델에 대한 정보를 가져옵니다.

* [Service Fabric에서 애플리케이션 모델링](service-fabric-application-model.md)
* [Service Fabric 애플리케이션 및 서비스 매니페스트](service-fabric-application-and-service-manifests.md)
* [모범 사례: 코드로서의 인프라](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [응용 프로그램 및 서비스를 Azure 리소스로 관리](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
