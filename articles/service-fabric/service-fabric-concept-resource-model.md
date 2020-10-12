---
title: Azure Service Fabric 응용 프로그램 리소스 모델
description: 이 문서에서는 Azure Resource Manager를 사용 하 여 Azure Service Fabric 응용 프로그램을 관리 하는 개요를 제공 합니다.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7ad0d4f6d92ba8d85383df281bd14681f43bb6d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258737"
---
# <a name="service-fabric-application-resource-model"></a>응용 프로그램 리소스 모델 Service Fabric

Service Fabric 클러스터에 Azure Service Fabric 응용 프로그램을 배포 하기 위한 여러 옵션이 있습니다. Azure Resource Manager를 사용 하는 것이 좋습니다. 리소스 관리자 사용 하는 경우 JSON에서 응용 프로그램 및 서비스를 설명 하 고 클러스터와 동일한 리소스 관리자 템플릿에 배포할 수 있습니다. PowerShell 또는 Azure CLI를 사용 하 여 응용 프로그램을 배포 하 고 관리 하는 것과 달리 리소스 관리자를 사용 하는 경우 클러스터가 준비 될 때까지 기다릴 필요가 없습니다. 응용 프로그램 등록, 프로 비전 및 배포는 모두 한 단계에서 발생할 수 있습니다. 클러스터에서 응용 프로그램 수명 주기를 관리 하는 가장 좋은 방법은 리소스 관리자를 사용 하는 것입니다. 자세한 내용은 [최선의 구현 방법: 코드로 인프라](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)를 참조 하세요.

리소스 관리자의 리소스로 응용 프로그램을 관리 하면 다음과 같은 영역에서 향상 된 기능을 얻을 수 있습니다.

* 감사 내역: 리소스 관리자는 모든 작업을 감사 하 고 자세한 활동 로그를 유지 합니다. 활동 로그는 응용 프로그램 및 클러스터에 대 한 변경 내용을 추적 하는 데 도움이 될 수 있습니다.
* 역할 기반 액세스 제어: 동일한 리소스 관리자 템플릿을 사용 하 여 클러스터 및 클러스터에 배포 된 응용 프로그램에 대 한 액세스를 관리할 수 있습니다.
* 관리 효율성: 리소스 관리자을 사용 하 여 클러스터 및 중요 한 응용 프로그램 배포를 관리 하기 위한 단일 위치 (Azure Portal)를 제공 합니다.

이 문서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 리소스 관리자를 사용 하 여 응용 프로그램 리소스를 배포 합니다.
> * 리소스 관리자를 사용 하 여 응용 프로그램 리소스를 업그레이드 합니다.
> * 응용 프로그램 리소스를 삭제 합니다.

## <a name="deploy-application-resources"></a>응용 프로그램 리소스 배포

리소스 관리자 응용 프로그램 리소스 모델을 사용 하 여 응용 프로그램 및 서비스를 배포 하기 위해 수행 하는 개략적인 단계는 다음과 같습니다.
1. 응용 프로그램 코드를 패키지 합니다.
1. 패키지를 업로드 합니다.
1. 응용 프로그램 리소스로 리소스 관리자 템플릿에서 패키지의 위치를 참조 합니다. 

자세한 내용은 [응용 프로그램 패키지](service-fabric-package-apps.md#create-an-sfpkg)를 참조 하십시오.

그런 다음 리소스 관리자 템플릿을 만들고 응용 프로그램 세부 정보를 사용 하 여 매개 변수 파일을 업데이트 한 다음 Service Fabric 클러스터에 템플릿을 배포 합니다. [샘플을 탐색](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)합니다.

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

리소스 관리자 템플릿에서 응용 프로그램을 배포 하려면 저장소 계정이 있어야 합니다. 저장소 계정은 응용 프로그램 이미지를 준비 하는 데 사용 됩니다. 

기존 저장소 계정을 다시 사용 하거나 응용 프로그램을 준비 하기 위한 새 저장소 계정을 만들 수 있습니다. 기존 저장소 계정을 사용 하는 경우이 단계를 건너뛸 수 있습니다. 

![스토리지 계정 만들기][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>저장소 계정 구성

저장소 계정을 만든 후에는 응용 프로그램이 준비 될 수 있는 blob 컨테이너를 만듭니다. Azure Portal에서 응용 프로그램을 저장 하려는 Azure Storage 계정으로 이동 합니다. **Blob**  >  **컨테이너 추가**를 선택 합니다. 

공용 액세스 수준을 **개인**으로 설정 하 여 클러스터의 리소스를 보호할 수 있습니다. 다음과 같은 여러 가지 방법으로 액세스 권한을 부여할 수 있습니다.

* [Azure Active Directory](../storage/common/storage-auth-aad-app.md)를 사용 하 여 blob 및 큐에 대 한 액세스 권한을 부여 합니다.
* [Azure Portal에서 RBAC](../storage/common/storage-auth-aad-rbac-portal.md)를 사용 하 여 Azure blob에 대 한 액세스 권한을 부여 하 고 데이터를 큐에 부여 합니다.
* [공유 액세스 서명을](/rest/api/storageservices/delegate-access-with-shared-access-signature)사용 하 여 액세스를 위임 합니다.

다음 스크린샷 예제에서는 blob에 대 한 익명 읽기 액세스를 사용 합니다.

![Blob 만들기][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>저장소 계정에서 응용 프로그램 준비

응용 프로그램을 배포 하려면 먼저 blob 저장소에서 응용 프로그램을 준비 해야 합니다. 이 자습서에서는 응용 프로그램 패키지를 수동으로 만듭니다. 이 단계는 자동화할 수 있습니다. 자세한 내용은 [응용 프로그램 패키지](service-fabric-package-apps.md#create-an-sfpkg)를 참조 하세요. 

이 자습서에서는 [투표 샘플 응용 프로그램](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)을 사용 합니다.

1. Visual Studio에서 **투표** 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **패키지**를 선택 합니다.

   ![패키지 응용 프로그램][PackageApplication]  
1. *.\Service-fabric-dotnet-quickstart\Voting\pkg\Debug* 디렉터리로 이동 합니다. 콘텐츠를 *Voting.zip*이라는 파일로 압축 합니다. *ApplicationManifest.xml* 파일은 zip 파일의 루트에 있어야 합니다.

   ![Zip 응용 프로그램][ZipApplication]  
1. 파일의 이름을 변경 하 여 확장명을 .zip에서 *. .sfpkg*로 변경 합니다.

1. Azure Portal의 저장소 계정에 대 한 **앱** 컨테이너에서 **업로드**를 선택 하 고 **.sfpkg**를 업로드 합니다. 

   ![앱 패키지 업로드][UploadAppPkg]

이제 응용 프로그램이 준비 되 고 응용 프로그램을 배포 하는 리소스 관리자 템플릿을 만들 수 있습니다.

### <a name="create-the-resource-manager-template"></a>리소스 관리자 템플릿 만들기

샘플 응용 프로그램에는 응용 프로그램을 배포 하는 데 사용할 수 있는 [Azure Resource Manager 템플릿이](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) 포함 되어 있습니다. 템플릿 파일 이름은 *UserApp.js설정* 되 고 *UserApp.Parameters.js*됩니다.

> [!NOTE]
> 파일 * 의UserApp.Parameters.js* 은 클러스터의 이름으로 업데이트 해야 합니다.
>
>

| 매개 변수              | 설명                                 | 예제                                                      | 주석                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 배포 하는 클러스터의 이름 | sf-cluster123                                                |                                                              |
| 애플리케이션            | 애플리케이션의 이름                 | Voting                                                       |
| applicationTypeName    | 응용 프로그램의 형식 이름입니다.           | VotingType                                                   | 일치 해야 ApplicationManifest.xml                 |
| applicationTypeVersion | 응용 프로그램 유형의 버전입니다.         | 1.0.0                                                        | 일치 해야 ApplicationManifest.xml                 |
| serviceName            | 서비스의 이름입니다.         | 투표 ~ VotingWeb                                             | ApplicationName ~ ServiceType 형식 이어야 합니다.            |
| serviceTypeName        | 서비스의 형식 이름입니다.                | VotingWeb                                                    | 일치 해야 ServiceManifest.xml                 |
| appPackageUrl          | 응용 프로그램의 blob 저장소 URL     | https: \/ /servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Blob storage의 응용 프로그램 패키지 URL (URL을 설정 하는 절차는이 문서의 뒷부분에 설명 되어 있습니다.) |

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

**AzResourceGroupDeployment** cmdlet을 실행 하 여 클러스터를 포함 하는 리소스 그룹에 응용 프로그램을 배포 합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>을 사용 하 여 Service Fabric 응용 프로그램 업그레이드 리소스 관리자

다음과 같은 이유 중 하나로 Service Fabric 클러스터에 이미 배포 된 응용 프로그램을 업그레이드할 수 있습니다.

* 새 서비스가 응용 프로그램에 추가 됩니다. 서비스가 응용 프로그램에 추가 될 때 *service-manifest.xml* 및 *application-manifest.xml* 파일에 서비스 정의를 추가 해야 합니다. 응용 프로그램의 새 버전을 반영 하려면 [UserApp.Parameters.js](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)의 응용 프로그램 유형 버전을 1.0.0에서 1.0.1로 변경 해야 합니다.

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

* 기존 서비스의 새 버전이 응용 프로그램에 추가 됩니다. 응용 프로그램 코드 변경 내용 및 앱 형식 버전 및 이름에 대 한 업데이트를 예로 들 수 있습니다. 이 업그레이드의 경우 다음과 같이 UserApp.Parameters.js업데이트 합니다.

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>응용 프로그램 리소스 삭제

리소스 관리자에서 응용 프로그램 리소스 모델을 사용 하 여 배포 된 응용 프로그램을 삭제 하려면 다음을 수행 합니다.

1. [AzResource](/powershell/module/az.resources/get-azresource?view=azps-2.5.0) cmdlet을 사용 하 여 응용 프로그램에 대 한 리소스 ID를 가져옵니다.

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. [AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) cmdlet을 사용 하 여 응용 프로그램 리소스를 삭제 합니다.

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>다음 단계

응용 프로그램 리소스 모델에 대 한 정보 가져오기:

* [Service Fabric에서 애플리케이션 모델링](service-fabric-application-model.md)
* [Service Fabric 애플리케이션 및 서비스 매니페스트](service-fabric-application-and-service-manifests.md)
* [최선의 구현 방법: 코드로 서의 인프라](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [응용 프로그램 및 서비스를 Azure 리소스로 관리](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
