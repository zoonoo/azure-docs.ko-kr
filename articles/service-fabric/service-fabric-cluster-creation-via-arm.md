<properties
   pageTitle="Azure 리소스 관리자 템플릿을 사용하여 서비스 패브릭 클러스터 설정 | Microsoft Azure"
   description="Azure 리소스 관리자 템플릿을 사용하여 서비스 패브릭 클러스터를 설정합니다."
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
   ms.date="05/02/2016"
   ms.author="chackdan"/>


# Azure 리소스 관리자 템플릿을 사용하여 서비스 패브릭 클러스터를 설정합니다.

이 페이지에서 Azure 리소스 관리자 템플릿을 사용하여 Azure 서비스 패브릭 클러스터를 설정할 수 있습니다. 이렇게 하려면 사용자의 구독에 이 클러스터를 형성할 IaaS VM을 배포하기에 충분한 코어가 있어야 합니다.

## 필수 조건

- 보안 클러스터를 설정하려면 먼저 주요 자격 증명 모음에 X.509 인증서를 업로드해야 합니다. 원본 자격 증명 모음 URL, 인증서 URL 및 인증서 지문이 필요합니다.
- 보안 클러스터를 설정하는 자세한 방법은 [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)을 참조하세요.

## 샘플 리소스 관리자 템플릿 가져오기

샘플 리소스 관리자 템플릿은 [GitHub의 Azure 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-quickstart-templates)에 제공됩니다. 모든 서비스 패브릭 템플릿 이름은 "service-fabric.."으로 시작합니다. "fabric"에 대한 리포지토리를 검색하거나 샘플 템플릿 집합을 아래로 스크롤할 수 있습니다. 원하는 템플릿을 신속하게 찾을 수 있도록 템플릿 이름이 다음과 같이 지정되어 있습니다.

- [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) 5노드 단일 노드 비보안 클러스터 템플릿을 나타냅니다.

- [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) WAD가 사용하도록 설정된 5노드 단일 노드 보안 클러스터 템플릿을 나타냅니다.

- [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) WAD가 사용하도록 설정된 10노드 2노드의 보안 클러스터 템플릿을 나타냅니다.

## 사용자 지정 리소스 관리자 템플릿 만들기

사용자 지정 리소스 관리자 템플릿은 두 가지 방법으로 만들 수 있습니다.

1. [GitHub의 Azure 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-quickstart-templates)에서 샘플 템플릿을 가져와 변경할 수 있습니다.

2. Azure 포털에 로그인하고 서비스 패브릭 포털을 사용하여 사용자 지정할 템플릿을 생성할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요.

    a. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

    b. [포털을 통해 Azure 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)의 설명에 따라 클러스터를 만드는 과정을 진행하되, **만들기**를 클릭하지 않습니다. 대신, 다음 스크린샷과 같이 **요약**으로 이동하고 템플릿을 다운로드합니다.

 ![리소스 관리자 템플릿의 다운로드 링크를 표시하는 서비스 패브릭 클러스터 페이지의 스크린샷][DownloadTemplate]

## Azure PowerShell을 사용하여 리소스 관리자 템플릿을 Azure에 배포합니다.

PowerShell을 사용하여 템플릿을 배포하는 방법에 대한 자세한 참고 자료는 [PowerShell을 사용하여 리소스 관리자 템플릿 배포](../resource-group-template-deploy.md)를 참조하세요.

>[AZURE.NOTE] 가용성을 유지하고 상태를 보존하기 위해 서비스 패브릭 클러스터에서 특정 수의 노드가 항상 작동 상태를 유지해야 하며, 이 숫자를 "유지 관리 쿼럼"이라고 합니다. 따라서 [상태 전체 백업](service-fabric-reliable-services-backup-restore.md)을 처음으로 수행하는 경우를 제외하고 일반적으로 클러스터의 모든 컴퓨터를 종료하는 행위는 안전하지 않습니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
- [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md).
- [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_0511_2016-->