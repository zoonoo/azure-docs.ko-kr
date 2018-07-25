---
title: Azure Service Fabric Mesh 용어 | Microsoft Docs
description: Azure Service Fabric Mesh의 일반적인 용어에 대해 알아봅니다.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 7c3ce5571c54d6c613114ea49999e450934c8ff4
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136201"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric Mesh 용어

Azure Service Fabric Mesh는 개발자가 가상 머신, 저장소 또는 네트워킹을 관리하지 않고 마이크로 서비스 응용 프로그램을 배포할 수 있는 완전히 관리되는 서비스입니다. 이 문서에서는 문서에 사용된 용어를 이해할 수 있도록 Azure Service Fabric Mesh에서 사용되는 용어를 자세히 설명합니다.

## <a name="service-fabric"></a>Service Fabric

Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 오픈 소스 시스템 플랫폼입니다. Service Fabric은 Service Fabric Mesh를 작동하는 오케스트레이터입니다. Service Fabric은 마이크로 서비스 응용 프로그램을 빌드하고 실행할 수 있는 방법에 대한 옵션을 제공합니다. 임의 프레임워크를 사용하여 서비스를 작성하고, 여러 환경 선택 항목 중에서 응용 프로그램을 실행할 위치를 선택할 수 있습니다.

## <a name="deployment-and-application-models"></a>배포 및 응용 프로그램 모델 

서비스를 배포하려면 어떻게 실행할지를 설명해야 합니다. Service Fabric은 다음 세 가지 배포 모델을 지원합니다.

### <a name="resource-model"></a>리소스 모델
리소스는 응용 프로그램, 서비스, 네트워크 및 볼륨을 포함하여 Service Fabric에 개별적으로 배포할 수 있는 모든 항목입니다. 리소스는 Azure 리소스 모델 스키마를 사용하여 YAML 파일 또는 JSON 파일을 통해 정의됩니다.

리소스 모델은 Service Fabric 응용 프로그램을 설명하는 가장 간단한 방법입니다. 주로 컨테이너화된 서비스의 간단한 배포 및 관리에 중점을 둡니다.

Service Fabric이 실행되기만 하면 어디든지 리소스를 배포할 수 있습니다.

### <a name="native-model"></a>네이티브 모델
네이티브 응용 프로그램 모델은 Service Fabric에 대한 모든 하위 수준 액세스를 응용 프로그램에 제공합니다. 응용 프로그램 및 서비스는 XML 매니페스트 파일에서 등록된 유형으로 정의됩니다.

네이티브 모델은 C# 및 Java로 작성된 Service Fabric 런타임 API 및 클러스터 관리 API에 대한 액세스를 제공하는 Reliable Services 프레임워크를 지원합니다. 네이티브 모델은 임의 컨테이너 및 실행 파일도 지원합니다.

Service Fabric Mesh 환경에서는 네이티브 모델이 지원되지 않습니다.

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/)는 Docker 프로젝트의 일부입니다. Service Fabric은 Docker Compose 모델을 사용한 응용 프로그램 배포를 제한적으로 지원합니다.

## <a name="environments"></a>환경

Service Fabric은 여러 서비스 및 제품의 기초가 되는 오픈 소스 플랫폼 기술입니다. Microsoft에서는 다음과 같은 옵션을 제공합니다.

 - **Service Fabric Mesh**: Microsoft Azure에서 Service Fabric 응용 프로그램을 실행하기 위한 완전히 관리되는 서비스입니다.
 - **Azure Service Fabric**: Azure 호스팅 Service Fabric 클러스터 제품입니다. Service Fabric 클러스터의 업그레이드 및 구성 관리와 함께 Service Fabric과 Azure 인프라 간의 통합을 제공합니다.
 - **Service Fabric 독립 실행형**: [임의 위치(온-프레미스 또는 임의 클라우드 공급자)에 Service Fabric 클러스터를 배포](/azure/service-fabric/service-fabric-deploy-anywhere)하기 위한 설치 및 구성 도구 집합입니다. Azure에서 관리되지 않습니다.
 - **Service Fabric 개발 클러스터**: Service Fabric 응용 프로그램 개발을 위해 Windows, Linux 또는 Mac에서 로컬 개발 환경을 제공합니다.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>환경, 프레임워크 및 배포 모델 지원 매트릭스
프레임워크 및 배포 모델에 대한 지원 수준은 환경마다 다릅니다. 다음 표에서는 지원되는 프레임워크 및 배포 모델 조합에 대해 설명합니다.

| 응용 프로그램 유형 | 설명 방법 | Azure Service Fabric Mesh | Azure 서비스 클러스터(모든 OS)| 로컬 클러스터 - Windows | 로컬 클러스터 - Linux | 로컬 클러스터 - Mac | 독립 실행형 클러스터(Windows)
|---|---|---|---|---|---|---|---|---|---|
| Service Fabric Mesh 응용 프로그램 | 리소스 모델(YAML 및 JSON) | 지원됨 |지원되지 않음 | 지원됨 |지원되지 않음 | 지원되지 않음 | 지원되지 않음 |
|Service Fabric 네이티브 응용 프로그램 | 네이티브 응용 프로그램 모델(XML) | 지원되지 않음| 지원됨|지원됨|지원됨|지원됨|지원됨|

다음 표에서는 다양한 응용 프로그램 모델 및 Service Fabric에 대한 응용 프로그램 모델의 도구에 대해 설명합니다.

| 응용 프로그램 유형 | 설명 방법 | Visual Studio 2017 | Visual Studio 2015 | (예: Eclipse | VS 코드 | SFCTL | AZ CLI | PowerShell
|---|---|---|---|---|---|---|---|---|---|
| Service Fabric Mesh 응용 프로그램 | 리소스 모델(YAML 및 JSON) | 지원됨 |지원되지 않음 |지원되지 않음 |지원되지 않음 |지원되지 않음 | 지원됨 - Mesh 환경만 | 지원되지 않음
|Service Fabric 네이티브 응용 프로그램 | 네이티브 응용 프로그램 모델(XML) | 지원됨| 지원됨|지원됨|지원됨|지원됨|지원됨|지원됨|

## <a name="next-steps"></a>다음 단계

Service Fabric Mesh에 대한 자세한 내용은 다음 개요를 참조하세요.
- [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)
