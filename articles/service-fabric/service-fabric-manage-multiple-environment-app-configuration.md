---
title: "Service Fabric에서 여러 환경 관리 | Microsoft Docs"
description: "한 컴퓨터에서 수천 개의 컴퓨터에 이르는 클러스터에서 서비스 패브릭 응용 프로그램을 실행할 수 있습니다. 어떤 경우에 해당하는 다양한 환경에 대해 다르게 응용 프로그램을 구성하려 합니다. 이 문서에서는 환경 당 다른 응용 프로그램 매개 변수를 정의하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: cd256c403cc8094a135157cdc69dbdd3971978ca
ms.openlocfilehash: 9f8a898f265bc27fc47ea2e3d00d123f3f47dad6


---
# <a name="manage-application-parameters-for-multiple-environments"></a>여러 환경에 대한 응용 프로그램 매개 변수 관리
하나의 컴퓨터에서 수천개의 컴퓨터를 사용하여 Azure 서비스 패브릭 클러스터를 만들 수 있습니다. 응용 프로그램 이진 파일은 다양한 환경에 걸쳐 수정하지 않고 실행할 수 있는 반면 응용 프로그램은 배포하는 컴퓨터의 수에 따라 다르게 구성하려는 경우가 많습니다.

간단한 예로 상태 비저장 서비스에 대해 `InstanceCount` 를 고려합니다. Azure에서 응용 프로그램을 실행할 때 일반적으로 이 매개 변수를 "-1"이라는 특수 값으로 설정하고자 합니다. 이는 서비스가 클러스터의 모든 노드(또는 배치 제약 조건을 설정한 경우 노드 형식의 모든 노드)에서 실행하도록 합니다. 그러나 단일 컴퓨터의 동일한 끝점에서 수신하는 여러 프로세스를 가질 수 없으므로 이 구성은 단일 컴퓨터 클러스터에 적합하지 않습니다. 대신 일반적으로 `InstanceCount` 를 "1"로 설정합니다.

## <a name="specifying-environment-specific-parameters"></a>환경 특정 매개 변수 지정
이 구성 문제에 대한 해법은 지정된 환경에 대한 해당 매개 변수 값을 입력하는 매개 변수가 있는 기본 서비스 및 응용 프로그램 매개 변수 파일의 집합입니다. 기본 서비스 및 응용 프로그램 매개 변수는 응용 프로그램 및 서비스 매니페스트에서 구성됩니다. ServiceManifest.xml 및 ApplicationManifest.xml에 대한 스키마 정의는 Service Fabric SDK 및 도구와 함께 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*에 설치됩니다.

### <a name="default-services"></a>기본 서비스
서비스 패브릭 응용 프로그램은 서비스 인스턴스의 컬렉션으로 이루어 집니다. 빈 응용 프로그램을 만들고 모든 서비스 인스턴스를 동적으로 만들 수 있지만 응용 프로그램 인스턴스화될 때 대부분의 응용 프로그램에는 항상 만들 수 있는 핵심 서비스 집합이 있습니다. 이 서비스를 "기본 서비스" 라고 합니다. 예비 대괄호에 포함된 환경 단위 구성에 대한 자리 표시자와 함께 응용 프로그램 매니페스트에서 지정됩니다.

```xml
    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>
```

각각의 명명된 매개 변수는 응용 프로그램 매니페스트의 매개 변수 요소 내에서 정의되어야 합니다.

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

DefaultValue 특성은 지정된 환경에 보다 구체적인 매개 변수가 없는 경우 사용될 값을 지정합니다.

> [!NOTE]
> 모든 서비스 인스턴스 매개 변수가 환경 단위 구성에 적합하지는 않습니다. 파티션 범위가 환경이 아닌 데이터 도메인의 함수이기 때문에 위의 예에서 서비스의 파티션 구성표에 대한 LowKey 및 HighKey 값은 서비스의 모든 인스턴스에 대해 명시적으로 정의됩니다.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>환경 단위 서비스 구성 설정
[서비스 패브릭 응용 프로그램 모델](service-fabric-application-model.md) 을 사용하면 서비스가 런타임에 읽을 수 있는 사용자 지정 키-값 쌍을 포함하는 구성 패키지를 포함할 수 있습니다. 또한 이러한 설정 값은 응용 프로그램 매니페스트에서 `ConfigOverride` 를 지정하여 환경으로 구별될 수 있습니다.

`Stateful1` 서비스에 대한 Config\Settings.xml 파일에 다음 설정이 있다고 가정합니다.

```xml
    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>
```
특정 응용 프로그램/환경 쌍에 대해 이 값을 재정의하려면 응용 프로그램 매니페스트에서 서비스 매니페스트를 가져올 때 `ConfigOverride` 을 만듭니다.

```xml
    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
이 매개 변수는 위에 표시된 대로 환경에서 구성될 수 있습니다. 매개 변수 섹션에서 해당 매개 변수를 선언하고 응용 프로그램 매개 변수 파일에서 환경 특정 값을 지정하여 수행할 수 있습니다.

> [!NOTE]
> 서비스 구성 설정의 경우 키의 값을 설정할 수 있는 세 가지 위치는 다음과 같습니다. 서비스 구성 패키지, 응용 프로그램 매니페스트 및 응용 프로그램 매개 변수 파일. 서비스 패브릭은 (지정된 경우) 항상 응용 프로그램 매개 변수 파일에서 먼저 선택한 다음 응용 프로그램 매니페스트 및 마지막으로 구성 패키지에서 선택합니다.
> 
> 

### <a name="application-parameter-files"></a>응용 프로그램 매개 변수 파일
서비스 패브릭 응용 프로그램 프로젝트는 하나 이상의 응용 프로그램 매개 변수 파일을 포함할 수 있습니다. 각각은 응용 프로그램 매니페스트에서 정의된 매개 변수에 특정 값을 정의합니다.

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
기본적으로 새 응용 프로그램은 Local.1Node.xml, Local.5Node.xml 및 Cloud.xml라는 세 응용 프로그램 매개 변수 파일을 포함합니다.

![솔루션 탐색기에서 응용 프로그램 매개 변수 파일][app-parameters-solution-explorer]

새 매개 변수 파일을 만들려면 기존 매개 변수 파일을 복사하고 붙여 넣은 다음 새 이름을 지정합니다.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>배포하는 동안 환경 단위 매개 변수 식별
배포 시 적절한 매개 변수 파일을 선택하여 응용 프로그램에 적용해야 합니다. Visual Studio의 게시 대화 상자 또는 PowerShell을 통해 이를 수행할 수 있습니다.

### <a name="deploy-from-visual-studio"></a>Visual Studio에서 배포
Visual Studio에서 응용 프로그램을 게시하는 경우 사용 가능한 매개 변수 파일의 목록에서 선택할 수 있습니다.

![게시 대화 상자에서 매개 변수 파일 선택][publishdialog]

### <a name="deploy-from-powershell"></a>PowerShell에서 배포
응용 프로그램 프로젝트 템플릿에 포함된 `Deploy-FabricApplication.ps1` PowerShell 스크립트는 매개 변수로 게시 프로필을 수락하고 게시 프로필은 응용 프로그램 매개 변수 파일에 대한 참조를 포함합니다.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>다음 단계
이 토픽에서 설명하는 핵심 개념 중 일부에 대해 자세히 알아보려면 [서비스 패브릭 기술 개요](service-fabric-technical-overview.md)를 참조하세요. Visual Studio에서 사용할 수 있는 다른 앱 관리 기능에 대한 정보는 [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md)를 참조하세요.

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png



<!--HONumber=Dec16_HO2-->


