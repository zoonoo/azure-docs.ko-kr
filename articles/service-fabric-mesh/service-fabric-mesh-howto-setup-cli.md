---
title: Azure Service Fabric Mesh CLI 설정
description: Service Fabric Mesh CLI(명령줄 인터페이스)는 Azure Service Fabric Mesh에서 리소스를 로컬로 배포하고 관리하는 데 필요합니다. 설정 방법은 다음과 같습니다.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: f8a9c26e65ef911ad85806c72c7946947379ab72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104613344"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI 설정

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

Service Fabric Mesh CLI(명령줄 인터페이스)는 Azure Service Fabric Mesh에서 리소스를 로컬로 배포하고 관리하는 데 필요합니다. 설정 방법은 다음과 같습니다.

사용할 수 있는 CLI에는 세 가지 유형이 있으며 아래 표에 요약되어 있습니다.

| CLI 모듈 | 대상 환경 |  Description | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | Azure Service Fabric Mesh 환경에 대해 애플리케이션을 배포하고 리소스를 관리할 수 있게 해주는 기본 CLI입니다. 
| sfctl | 로컬 클러스터 | 로컬 클러스터에 대해 Service Fabric CLI 리소스를 배포 및 테스트할 수 있도록 해주는 Service Fabric CLI입니다.  
| Maven CLI | 로컬 클러스터 및 Azure Service Fabric Mesh | Java 개발자가 로컬 및 Azure 개발 환경에 익숙한 명령줄 환경을 사용할 수 있게 해주는 `az mesh` 및 `sfctl`에 대한 래퍼입니다.  

미리 보기를 위해 Azure CLI에 대한 확장으로 Azure Service Fabric Mesh CLI가 작성되었습니다. Azure Cloud Shell에서 설치하거나 Azure CLI의 로컬 설치에서 설치할 수 있습니다. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.67 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Azure Service Fabric Mesh CLI 설치

아직 설치하지 않은 경우 다음 명령을 사용하여 Azure Service Fabric Mesh CLI 확장 모듈을 설치합니다. 
 
```azurecli-interactive
az extension add --name mesh
```

이미 설치되어 있는 경우 다음 명령을 사용하여 기존 Azure Service Fabric Mesh CLI 모듈을 업데이트합니다.

```azurecli-interactive
az extension update --name mesh
```

## <a name="install-the-service-fabric-cli-sfctl"></a>Service Fabric CLI(sfctl) 설치 

[Service Fabric CLI 설치](../service-fabric/service-fabric-cli.md)의 지침을 따르세요. **sfctl** 모듈은 로컬 머신의 Service Fabric 클러스터에 대한 리소스 모델을 기반으로 애플리케이션을 배포하는 데 사용할 수 있습니다. 

## <a name="install-the-maven-cli"></a>Maven CLI 설치 

Maven CLI를 사용하려면 다음을 머신에 설치해야 합니다. 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI(az mesh) - Azure Service Fabric Mesh를 대상으로 지정 
* SFCTL(sfctl) - 로컬 클러스터를 대상으로 지정 

Service Fabric용 Maven CLI는 아직 미리 보기 버전입니다. 

Maven Java 앱에서 Maven 플러그 인을 사용하려면 pom.xml 파일에 다음 코드 조각을 추가합니다.

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

자세한 사용 방법을 알아보려면 [Maven CLI 참조](service-fabric-mesh-reference-maven.md)를 읽어보세요.

## <a name="next-steps"></a>다음 단계

[Windows 개발 환경](service-fabric-mesh-howto-setup-developer-environment-sdk.md)을 설정할 수도 있습니다.

[일반적인 질문 및 문제](service-fabric-mesh-faq.md)에 대한 답변을 찾습니다.

[azure-cli-install]: /cli/azure/install-azure-cli
