---
title: "Visual Studio로 원격 클러스터에 앱 게시 | Microsoft Docs"
description: "Visual Studio를 사용하여 원격 서비스 패브릭 클러스터에 응용 프로그램을 게시하는 방법을 알아봅니다."
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: faecd892-eb54-4d9c-8023-c67442afb8e8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/29/2016
ms.author: cawa
ms.translationtype: HT
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: c440c520d84fc503ff9e705555449e92555d4721
ms.contentlocale: ko-kr
ms.lasthandoff: 07/12/2017

---
<a id="deploy-and-remove-applications-using-visual-studio" class="xliff"></a>

# Visual Studio를 사용하여 응용 프로그램 배포 및 제거
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Visual Studio용 Azure 서비스 패브릭 확장은 서비스 패브릭 클러스터에 응용 프로그램을 게시하는 반복 및 스크립트 가능한 간편한 방법을 제공합니다.

<a id="the-artifacts-required-for-publishing" class="xliff"></a>

## 게시에 필요한 아티팩트
<a id="deploy-fabricapplicationps1" class="xliff"></a>

### Deploy-FabricApplication.ps1
서비스 패브릭 응용 프로그램 게시를 위한 매개 변수로 게시 프로필 경로를 사용하는 PowerShell 스크립트입니다. 이 스크립트는 응용 프로그램의 일부이므로 응용 프로그램 맞게 필요에 따라 편하게 수정할 수 있습니다.

<a id="publish-profiles" class="xliff"></a>

### 게시 프로필
이름이 **PublishProfiles**인 Service Fabric 응용 프로그램 프로젝트의 폴더에는 다음과 같이 응용 프로그램을 게시하기 위해 중요한 정보를 저장하는 XML 파일이 들어있습니다.

* 서비스 패브릭 클러스터 연결 매개 변수
* 응용 프로그램 매개 변수 파일 경로
* 업그레이드 설정

기본적으로 응용 프로그램은 Local.1Node.xml, Local.5Node.xml 및 Cloud.xml의 세 가지 게시 프로필을 포함하게 됩니다. 기본 파일 중 하나를 복사 및 붙여넣기하여 프로필을 더 추가할 수 있습니다.

<a id="application-parameter-files" class="xliff"></a>

### 응용 프로그램 매개 변수 파일
이름이 **ApplicationParameters**인 서비스 패브릭 응용 프로그램 프로젝트의 폴더에는 사용자 특정 응용 프로그램 매니페스트 매개 변수 값에 대한 XML 파일이 들어있습니다. 배포 설정에 다른 값을 사용할 수 잇게 응용 프로그램 매니페스트 파일을 매개 변수화할 수 있습니다. 응용 프로그램을 매개 변수화하는 방법에 대한 자세한 내용은 [서비스 패브릭에서 여러 환경 관리](service-fabric-manage-multiple-environment-app-configuration.md)를 참조하세요.

> [!NOTE]
> 행위자 서비스의 경우 편집기에서 또는 게시 대화 상자를 통해 파일 편집을 시도하기 전에 프로젝트를 먼저 빌드해야 합니다. 매니페스트 파일의 일부가 빌드 중에 생성되기 때문입니다.

<a id="to-publish-an-application-using-the-publish-service-fabric-application-dialog-box" class="xliff"></a>

## Service Fabric 응용 프로그램 게시 대화 상자를 사용하여 응용 프로그램을 게시하려면
다음 단계에서는 Visual Studio Service Fabric 도구에서 제공하는 **Service Fabric 응용 프로그램 게시** 대화 상자를 사용하여 응용 프로그램을 게시하는 방법을 보여 줍니다.

1. Service Fabric 응용 프로그램 프로젝트의 바로 가기 메뉴에서 **게시...**를 선택하여 **Service Fabric 응용 프로그램 게시** 대화 상자를 봅니다.
   
    ![**Service Fabric 응용 프로그램 게시** 대화 상자][0]
   
    **대상 프로필** 드롭다운 목록 상자에서 선택한 파일에 **매니페스트 버전**을 제외한 모든 설정이 저장됩니다. 기존 프로필을 재사용하거나 **대상 프로필** 드롭다운 목록 상자에서 **<프로필 관리…>**를 선택하여 새 프로필을 만듭니다. 게시 프로필을 선택하면 그 내용이 대화 상자의 해당 필드에 표시됩니다. 언제든 변경 사항을 저장하려면 **프로필 저장** 링크를 선택합니다.    
2. **연결 끝점** 섹션에서 로컬 또는 원격 Service Fabric 클러스터의 게시 끝점을 지정합니다. 연결 끝점을 추가 또는 변경하려면 **연결 끝점** 드롭다운 목록을 클릭합니다. 이 목록은 Azure 구독에 따라 게시할 수 있는 사용 가능한 Service Fabric 클러스터 연결 끝점을 표시합니다. Visual Studio에 아직 로그인하지 않은 경우 로그인하라는 메시지가 표시됩니다.
   
    클러스터 선택 대화 상자를 사용하여 사용 가능한 구독 및 클러스터 집합 중에서 선택합니다.
   
    ![**Service Fabric 클러스터 선택** 대화 상자][1]
   
   > [!NOTE]
   > 임의의 끝점(예: 당사 클러스터)에 게시하려는 경우 아래 **임의 클러스터 끝점에 게시** 섹션을 참조하세요.
   > 
   > 
   
    끝점을 선택하면 Visual Studio가 선택한 서비스 패브릭 클러스터에 대해 연결 유효성을 확인합니다. 클러스터에 보안이 없으면 Visual Studio가 즉시 연결할 수 있습니다. 그러나 클러스터가 보안 상태이면 계속하기 전에 로컬 컴퓨터에 인증서를 설치해야 합니다. 자세한 내용은 [보안 연결을 구성하는 방법](service-fabric-visualstudio-configure-secure-connections.md)을 참조하세요. 완료되면 **확인** 단추를 선택합니다. 선택한 클러스터가 **Service Fabric 응용 프로그램 게시** 대화 상자에 표시됩니다.
3. **응용 프로그램 매개 변수 파일** 드롭다운 목록에서 응용 프로그램 매개 변수 파일로 이동합니다. 응용 프로그램 매개 변수 파일은 응용 프로그램 매니페스트 파일의 매개 변수에 대한 사용자 특정 값을 담고 있습니다. 매개 변수를 추가 또는 변경하려면 **편집** 단추를 선택합니다. **매개 변수** 표에서 매개 변수의 값을 입력하거나 변경합니다. 완료되면 **저장** 단추를 선택합니다.
   
    ![**매개 변수 편집** 대화 상자][2]
4. **응용 프로그램 업그레이드** 확인란을 사용하여 이 게시 작업이 업그레이드인지 여부를 지정합니다. 업그레이드 게시 작업은 일반적인 게시 작업과 다릅니다. 차이점 목록은 [서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)를 참조하세요. 업그레이드 설정을 구성하려면 **업그레이드 설정 구성** 링크를 선택합니다. 업그레이드 매개 변수 편집기가 나타납니다. 업그레이드 매개 변수에 대한 자세한 내용은 [서비스 패브릭 응용 프로그램의 업그레이드 구성](service-fabric-visualstudio-configure-upgrade.md)을 참조하세요.
5. **매니페스트 버전...** 버튼을 선택하여 **버전 편집** 대화 상자를 봅니다. 업그레이드를 수행하려면 응용 프로그램 및 서비스 버전을 업데이트해야 합니다. 응용 프로그램과 서비스 매니페스트 버전이 업그레이드 프로세스에 어떻게 영향을 미치는지 확인하려면 [Service Fabric 응용 프로그램 업그레이드 자습서](service-fabric-application-upgrade-tutorial.md)를 참조하세요.
   
    ![**버전 편집** 대화 상자][3]
   
    응용 프로그램 및 서비스 버전이 1.0.0과 같은 의미 체계 버전 관리나 1.0.0.0 형식의 숫자 값을 사용할 경우 **응용 프로그램 및 서비스 버전을 자동으로 업데이트** 옵션을 선택합니다. 이 옵션을 선택하면 코드, 구성 또는 데이터 패키지 버전이 업데이트될 때마다 서비스 및 응용 프로그램 버전 번호가 자동으로 업데이트됩니다. 버전을 수동으로 편집하려면 확인란의 선택을 취소하여 이 기능을 사용하지 않습니다.
   
   > [!NOTE]
   > 행위자 프로젝트에 대해 모든 패키지 항목을 표시하려면 먼저 서비스 매니페스트 파일에 항목을 생성하는 프로젝트를 빌드합니다.
   > 
   > 
6. 모든 필요한 설정을 지정한 후에는 **게시** 단추를 선택하여 응용 프로그램을 선택한 서비스 패브릭 클러스터에 게시합니다. 지정한 설정이 게시 프로세스에 적용됩니다.

<a id="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters" class="xliff"></a>

## 임의 클러스터 끝점(당사 클러스터 포함)에 게시
Visual Studio 게시 환경은 Azure 구독 중 하나와 연결된 원격 클러스터에 게시하도록 최적화되어 있습니다. 그러나 게시 프로필 XML을 직접 편집하여 임의 끝점(예: 서비스 패브릭 당사 클러스터)에 게시할 수 있습니다. 위에서 설명한 대로, 기본적으로 **Local.1Node.xml**, **Local.5Node.xml** 및 **Cloud.xml**이라는 세 게시 프로필이 제공되지만 다양한 환경에 맞는 추가 프로필을 만들 수 있습니다. 예를 들어 **Party.xml**라는 당사 클러스터에 게시하기 위한 프로필을 만들 수 있습니다.

비보안 클러스터에 연결하는 경우 `partycluster1.eastus.cloudapp.azure.com:19000`과 같은 클러스터 연결 끝점만 있으면 됩니다. 이 경우 게시 프로필의 연결 끝점은 다음과 같습니다.

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  보안 클러스터에 연결하는 경우 로컬 저장소에서 인증에 사용할 클라이언트 인증서에 대한 세부 정보를 제공해야 합니다. 자세한 내용은 [Service Fabric 클러스터에 대한 보안 연결 구성](service-fabric-visualstudio-configure-secure-connections.md)을 참조하세요.

  게시 프로필을 설정하면 아래와 같이 게시 대화 상자에서 이를 참조할 수 있습니다.

  ![게시 대화 상자에서 새 게시 프로필][4]

  이 경우 새 게시 프로필이 기본 응용 프로그램 매개 변수 파일 중 하나를 가리킵니다. 이것은 동일한 응용 프로그램 구성을 다양한 환경에 게시하려는 경우 적합합니다. 반면에 게시할 각 환경에 대해 서로 다른 구성을 포함하려는 경우 해당하는 응용 프로그램 매개 변수 파일을 만드는 것이 좋습니다.

<a id="next-steps" class="xliff"></a>

## 다음 단계
연속 통합 환경에서 게시 프로세스를 자동화하는 방법은 [Service Fabric 연속 통합 설정](service-fabric-set-up-continuous-integration.md)을 참조하세요.

[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png

