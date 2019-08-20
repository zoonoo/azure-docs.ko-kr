---
title: Azure Service Fabric 응용 프로그램 리소스 모델 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager를 사용 하 여 Azure Service Fabric 응용 프로그램을 관리 하는 개요를 제공 합니다.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: atsenthi
ms.openlocfilehash: 8e39318dcaa31a111908c6be1ae7a51d73eb7478
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623823"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>Service Fabric 응용 프로그램 리소스 모델은 무엇 인가요?
Service Fabric 응용 프로그램은 Azure Resource Manager를 통해 Service Fabric 클러스터에 배포 하는 것이 좋습니다. 이 방법을 사용 하면 JSON에서 응용 프로그램 및 서비스를 설명 하 고 클러스터와 동일한 리소스 관리자 템플릿에 배포할 수 있습니다. PowerShell 또는 Azure CLI를 통해 응용 프로그램을 배포 하 고 관리 하는 것과는 반대로 클러스터가 준비 될 때까지 기다릴 필요가 없습니다. 애플리케이션 등록, 프로비저닝 및 배포 프로세스를 모두 한 단계로 수행할 수 있습니다. 이 방식은 클러스터의 애플리케이션 수명 주기를 관리하는 모범 사례입니다. 자세한 내용은 [모범 사례](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources)를 참조 하세요.

해당하는 경우 애플리케이션을 Resource Manager 리소스로 관리하여 다음을 향상시킬 수 있습니다.
* 감사 내역: 리소스 관리자는 모든 작업을 감사 하 고 이러한 응용 프로그램 및 클러스터에 대 한 변경 내용을 추적 하는 데 도움이 될 수 있는 자세한 *활동 로그* 를 유지 합니다.
* 역할 기반 액세스 제어: 클러스터 및 클러스터에 배포 된 응용 프로그램에 대 한 액세스 관리는 동일한 리소스 관리자 템플릿을 통해 수행할 수 있습니다.
* Azure Resource Manager (Azure Portal를 통해)은 클러스터 및 중요 한 응용 프로그램 배포를 관리 하기 위한 원스톱 상점입니다.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 응용 프로그램 수명 주기 Service Fabric 
이 문서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Resource Manager를 사용 하 여 응용 프로그램 리소스 배포 
> * Azure Resource Manager를 사용 하 여 응용 프로그램 리소스 업그레이드
> * 응용 프로그램 리소스 삭제

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 응용 프로그램 리소스 배포  
Azure Resource Manager 응용 프로그램 리소스 모델을 사용 하 여 응용 프로그램 및 서비스를 배포 하려면 응용 프로그램 코드를 패키지 하 고 패키지를 업로드 한 다음 Azure Resource Manager 템플릿에서 패키지 위치를 응용 프로그램으로 참조 해야 합니다. 리소스나. 자세한 내용은 [응용 프로그램 패키지](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)를 참조 하십시오.
          
그런 다음 Azure Resource Manager 템플릿을 만들고 응용 프로그램 세부 정보를 사용 하 여 매개 변수 파일을 업데이트 한 다음 Service Fabric 클러스터에 배포 합니다. [여기](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)에서 샘플을 참조 하세요.

### <a name="create-a-storage-account"></a>Storage 계정 만들기 
응용 프로그램 이미지를 준비 하려면 리소스 관리자 템플릿에서 응용 프로그램을 배포 하려면 저장소 계정이 필요 합니다. 기존 저장소 계정을 다시 사용 하거나 새 저장소 계정을 만들어 응용 프로그램을 준비할 수 있습니다. 기존 저장소 계정을 사용 하려는 경우이 단계를 건너뛸 수 있습니다. 

![저장소 계정 만들기][CreateStorageAccount]

### <a name="configure-storage-account"></a>저장소 계정 구성 
저장소 계정을 만든 후에는 응용 프로그램이 준비 될 수 있는 blob 컨테이너를 만들어야 합니다. Azure Portal에서 응용 프로그램을 저장 하려는 저장소 계정으로 이동 합니다. **Blob** 블레이드를 선택 하 고 **컨테이너 추가** 단추를 클릭 합니다. Blob 공용 액세스 수준을 사용 하 여 새 컨테이너를 추가 합니다.
   
![BLOB 만들기][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>저장소 계정의 스테이지 응용 프로그램
응용 프로그램을 배포 하려면 먼저 blob 저장소에서 준비 해야 합니다. 이 자습서에서는 응용 프로그램 패키지를 수동으로 만들게 되지만이 단계를 자동화할 수 있습니다.  자세한 내용은 [응용 프로그램 패키지](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg)를 참조 하십시오. 다음 단계에서는 [투표 샘플 응용 프로그램](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) 을 사용 합니다.

1. Visual Studio에서 투표 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 패키지를 선택 합니다.   
![패키지 응용 프로그램][PackageApplication]  
2. 방금 만든 **.\service-fabric-dotnet-quickstart\Voting\pkg\Debug** 디렉터리를 열고 해당 콘텐츠를 **응답 .zip** 이라는 파일에 압축 합니다 .이 파일은 applicationmanifest이 zip 파일의 루트에 있습니다.  
![Zip 응용 프로그램][ZipApplication]  
3. 파일 확장명을 .zip에서 **. .sfpkg**로 바꿉니다.
4. Azure Portal의 저장소 계정에 대 한 **앱** 컨테이너에서 **업로드** 및 업로드 **.sfpkg**를 클릭 합니다.  
![앱 패키지 업로드][UploadAppPkg]

이제 응용 프로그램이 준비 되었습니다. 이제 응용 프로그램을 배포 하는 Azure Resource Manager 템플릿을 만들 준비가 되었습니다.      
   
### <a name="create-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 만들기
응용 프로그램 예제에는 응용 프로그램을 배포 하는 데 사용할 수 있는 [Azure Resource Manager 템플릿이](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) 포함 되어 있습니다. 템플릿 파일의 이름은 **Userapp. json** 및 **userapp**입니다. 

> [!NOTE] 
> **Userapp** 파일은 클러스터의 이름으로 업데이트 해야 합니다.
>
>

| 매개 변수              | 설명                                 | 예제                                                      | 주석                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 배포 하는 클러스터의 이름 | sf-cluster123                                                |                                                              |
| 애플리케이션            | 애플리케이션의 이름                 | 투표                                                       |
| applicationTypeName    | 응용 프로그램의 형식 이름입니다.           | VotingType                                                   | ApplicationManifest .xml의 내용과 일치 해야 합니다.                 |
| applicationTypeVersion | 응용 프로그램 유형의 버전입니다.         | 1.0.0                                                        | ApplicationManifest .xml의 내용과 일치 해야 합니다.                 |
| serviceName            | 서비스 서비스의 이름입니다.         | 투표 ~ VotingWeb                                             | ApplicationName ~ ServiceType 형식 이어야 합니다.            |
| serviceTypeName        | 서비스의 형식 이름입니다.                | VotingWeb                                                    | Servicemanifest.xml에 있는 것과 일치 해야 합니다.                 |
| appPackageUrl          | 응용 프로그램의 blob 저장소 URL     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Blob storage에 있는 응용 프로그램 패키지의 URL (아래에서 설명 하는 절차는 아래에 설명 되어 있습니다.) |
       
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
응용 프로그램을 배포 하려면 AzResourceGroupDeployment를 실행 하 여 클러스터를 포함 하는 리소스 그룹에 배포 합니다.
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 Service Fabric 응용 프로그램 업그레이드
Service Fabric 클러스터에 이미 배포 된 응용 프로그램은 다음과 같은 이유로 업그레이드 될 예정입니다.

1. 새 서비스가 응용 프로그램에 추가 됩니다. Service-manifest 및 application-manifest 파일에 서비스 정의를 추가 해야 합니다. 그런 다음 응용 프로그램의 새 버전을 반영 하려면 1.0.0의 응용 프로그램 유형 버전을 1.0.1 [Userapp. parameters](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)로 업데이트 해야 합니다.

    ```
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
2. 기존 서비스의 새 버전이 응용 프로그램에 추가 됩니다. 여기에는 응용 프로그램 코드 변경 내용과 앱 형식 버전 및 이름 업데이트가 포함 됩니다.

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>응용 프로그램 리소스 삭제
Azure Resource Manager에서 응용 프로그램 리소스 모델을 사용 하 여 배포 된 응용 프로그램은 아래 단계를 사용 하 여 클러스터에서 삭제할 수 있습니다.

1) [AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) 명령을 사용 하 여 응용 프로그램에 대 한 리소스 id 가져오기  

    #### <a name="get-resource-id-for-application"></a>응용 프로그램에 대 한 리소스 ID 가져오기
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) [AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) 를 사용 하 여 응용 프로그램 리소스를 삭제 합니다.  

    #### <a name="delete-application-resource-using-its-resource-id"></a>리소스 ID를 사용 하 여 응용 프로그램 리소스 삭제
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>다음 단계
응용 프로그램 리소스 모델에 대 한 정보 가져오기:

* [Service Fabric에서 애플리케이션 모델링](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [응용 프로그램 및 서비스 매니페스트 Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png