<properties
   pageTitle="ARM 템플릿을 사용하여 서비스 패브릭 클러스터 설정 | Microsoft Azure"
   description="ARM 템플릿을 사용하여 서비스 패브릭 클러스터 설정."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/13/2015"
   ms.author="chackdan"/>

# ARM 템플릿을 사용하여 서비스 패브릭 클러스터 설정

이 페이지는 ARM 템플릿을 사용한 서비스 패브릭 클러스터 설정을 안내합니다. 사용자의 구독에 이 클러스터를 형성할 IaaS VM을 배포하기에 충분한 코어가 있다고 가정합니다.

## 필수 조건

- 보안 클러스터를 설정하려면, 키 자격 증명 모음에 x509 인증서가 업로드되어 있어야 합니다. 소스 자격 증명 모음 URL, 인증서 URL, 인증서 지문이 필요합니다.
-  자세한 방법은 [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)을 참조하세요.

## 샘플 ARM 템플릿 확보

1. 샘플 ARM 템플릿은 [GitHub의 Azure 빠른 시작 템플릿 갤러리(영문)](https://github.com/Azure/azure-quickstart-templates)에 제공됩니다. 이름이 "service-fabric-cluster-.."로 시작되는 모든 서비스 패브릭 템플릿입니다. 리포지토리에서 "fabric"을 검색하거나 샘플 템플릿 쪽으로 스크롤 다운합니다.
2. 원하는 템플릿을 신속하게 찾을 수 있도록 템플릿 이름이 다음과 같이 지정되어 있습니다.
	1. [service-fabric-cluster-5-node-1-nodetype](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype) 5노드 단일 노드 비보안 클러스터 템플릿을 나타냅니다.
	2. [service-fabric-cluster-5-node-1-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad) WAD를 사용하도록 설정한 5노드 단일 노드 비보안 클러스터 템플릿을 나타냅니다.
	3. [service-fabric-cluster-5-node-secure-1-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-secure-1-nodetype-wad) WAD를 사용하도록 설정한 5노드 단일 노드 보안 클러스터 템플릿을 나타냅니다.
	4. [service-fabric-cluster-10-node-secure-2-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-secure-1-nodetype-wad) WAD를 사용하도록 설정한 10노드 2노드 보안 클러스터 템플릿을 나타냅니다.
	

## 사용자 지정 ARM 템플릿 만들기

2. 다음 두 가지 중에서 선택할 수 있습니다. 
	1. [GitHub의 Azure 빠른 시작 템플릿 갤러리(영문)](https://github.com/Azure/azure-quickstart-templates)에서 샘플 템플릿을 확보하여 변경할 수 있습니다.
	2. Azure 포털에 로그인하고 서비스 패브릭 포털을 사용하여 사용자 지정할 템플릿을 생성합니다. 수행하는 과정이 아래 설명되어 있습니다.
3. Azure 포털 [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)에 로그온합니다.
2. [포털을 통해 서비스 패브릭 클러스터 만들기(영문)](service-fabric-cluster-creation-via-portal.md)의 설명에 따라서 클러스터를 만드는 과정을 진행하되, ****만들기**를 클릭하지 말고 요약으로 이동하여 템플릿을 다운로드합니다. ![DownloadTemplate][DownloadTemplate]

## Azure PS를 사용하여 Azure에 ARM 템플릿 배포

방법에 대한 자세한 지침은 [PS를 사용하여 ARM 템플릿 배포](https://azure.microsoft.com/ko-KR/documentation/articles/resource-group-template-deploy/)를 참조하세요.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
- [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md) 
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md).
- [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=Nov15_HO4-->