
<properties 
   pageTitle="Azure SDK for .NET 2.7 릴리스 정보" 
   description="Azure SDK for .NET 2.7 릴리스 정보" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/20/2015"
   ms.author="juliako"/>


# Azure SDK for .NET 2.7 릴리스 정보

이 문서에는 Azure SDK for .NET 2.7 릴리스의 릴리스 정보가 포함되어 있습니다.

Azure SDK 2.7은 Visual Studio 2015 및 Visual Studio 2013에서만 지원됩니다. [Azure SDK 2.6](http://azure.microsoft.com/downloads/)은 Visual Studio 2012에 대해 마지막으로 지원되는 SDK입니다.

이 릴리스에 대한 자세한 정보는 [Azure SDK 2.7 발표 게시물](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)을 참조하세요.

##Visual Studio 2015의 향상된 로그인 기능

Visual Studio 2015용 Azure SDK 2.7은 Visual Studio 2015의 새 ID 관리 기능을 지원합니다. 역할 기반 액세스 제어, 클라우드 솔루션 공급자, DreamSpark, 기타 계정 및 구독 유형을 통해 Azure에 액세스하는 계정에 대한 지원이 포함되어 있습니다.

이러한 향상된 로그인 기능은 Visual Studio 2015에서만 사용할 수 있습니다. Visual Studio 2013에 대한 지원은 향후 업데이트에 포함됩니다.


##모바일 SDK

최신 [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 및 설치 프로세스가 반영되도록 **모바일 앱** 템플릿을 업데이트합니다.

##서비스 버스 

일반 버그 수정 사항 및 향상된 기능입니다. 업데이트 및 기능에 대한 자세한 내용은 최신 [서비스 버스 NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/)의 릴리스 정보를 참조하세요.

##HDInsight 도구 

이 릴리스에서는 다음 사항이 업데이트되었습니다. 이러한 업데이트는 미리 보기 상태입니다. 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=619108)를 참조하세요.

- Tez 기반 Hive 작업에 대한 Hive 그래프
- 완전한 Hive DML IntelliSense 지원
- Pig 템플릿 지원
- Azure 서비스용 Storm 템플릿

###주요 변경 내용

- 이 버전의 도구를 사용하는 경우 이전 **Storm** 프로젝트를 업그레이드해야 합니다. 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=619108)를 참조하세요.
- Visual Studio Web Express는 더 이상 지원되지 않습니다. 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=619108)를 참조하세요.

##Azure 앱 서비스 도구

이 릴리스에서는 웹 도구 확장에 대해 다음 사항이 업데이트되었습니다. 자세한 내용은 [이](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) 블로그를 참조하세요.

- DreamSpark 계정에 대한 지원 추가
- 새 Azure 리소스 관리 API를 지원하도록 전체 Azure Tools 변경
- [Cloud Explorer](azure-sdk-dot-net-release-notes-2_7.md#cloud_explorer)에 Azure 앱 서비스에 대한 지원 추가

###알려진 문제

웹앱 배포 슬롯 노드가 서버 탐색기의 슬롯 노드 아래에 표시되지 않으며, 웹앱 배포 슬롯 자식 노드가 Cloud Explorer 아래에 로드되지 않습니다. 이 문제는 해결되었으며 다음 SDK 릴리스에 포함되도록 준비되었습니다.


##<a id="cloud_explorer"></a>Visual Studio 2015용 Cloud Explorer

Azure SDK 2.7에는 Azure 리소스를 보고, 해당 속성을 검사하고, Visual Studio 내에서 핵심 개발자 작업을 수행할 수 있는 Visual Studio 2015용 Cloud Explorer가 포함되어 있습니다.

Cloud Explorer는 다음을 지원합니다.

- Azure 리소스에 대한 리소스 그룹 및 리소스 종류 뷰 
- 이름별 리소스 검색(리소스 종류 뷰에서 사용 가능)
- RBAC(역할 기반 액세스 제어)가 적용되는 구독 및 리소스에 대한 지원 
- 선택한 리소스와 관련된 개발자 중심 작업을 보여주는 통합 작업 패널. 예: 리소스 관리자 스택을 사용하여 만든 가상 컴퓨터에 대한 원격 디버거 연결, 가상 컴퓨터에 대한 진단 데이터 보기 등
- 개발/테스트 중 일반적으로 필요한 개발자 중심 속성을 보여주는 통합 속성 패널 
- 리소스 열거 시 사용할 계정의 빠른 전환(도구 모음에서 설정 명령 사용) 
- 리소스 열거 시 사용할 구독 필터링(도구 모음에서 설정 명령 사용) 
- 리소스 및 리소스 그룹 관리를 위한 Azure Preview 포털 딥 링크 
 
 
##Azure 리소스 관리자 도구 

Azure 리소스 관리자 도구가 RBAC(역할 기반 액세스 제어) 및 새 구독 유형과 작동하도록 업데이트되었습니다. 기존 저장소 외에도 배포하는 동안 새 저장소 계정을 사용하여 아티팩트를 저장하는 기능이 변경 내용에 포함되어 있습니다.

SDK 2.7과 함께 이전 버전의 SDK에서 Azure 리소스 그룹 프로젝트를 사용하는 경우 새 배포 스크립트는 기존 저장소가 아닌 새 저장소 계정을 사용하여 배포해야 합니다. 프로젝트가 변경되기 전에 새 스크립트를 추가하라는 메시지가 표시됩니다. 이전 스크립트는 이름이 변경되며 새 스크립트는 수동으로 수정해야 합니다.
 
 
##저장소 탐색기 도구 

- 추가 Blob 보기 지원입니다. 자세한 내용은 [이 블로그 게시물](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx)에 있습니다. 
- 서버 탐색기를 통한 프리미엄 저장소 계정 보기 지원입니다. 서버 탐색기는 프리미엄 저장소 계정에 대해 페이지 Blob만 표시합니다. 프리미엄 저장소 계정에 대해 지원되는 유일한 형식이기 때문입니다.

##Visual Studio용 Azure Data Factory 도구 

Visual Studio용 **Azure Data Factory 도구**를 소개합니다. 사용할 수 있는 기능은 다음과 같습니다. 자세한 내용은 [이 블로그](http://go.microsoft.com/fwlink/?LinkId=617530)를 참조하세요.

- **템플릿 기반 작성**: 사용 사례 기반 템플릿, 데이터 이동 템플릿 또는 데이터 처리 템플릿을 선택하여 종단 간 데이터 통합 솔루션을 배포하고 Data Factory 실습을 빠르게 시작합니다. 
- **Data Factory 엔터티 작성 및 배포를 위해 솔루션 탐색기와 통합**: 파이프라인 및 관련 엔터티를 Visual Studio 프로젝트로 만들고 배포합니다. 
- **작성 중 시각적 상호 작용을 위해 다이어그램 뷰와 통합**: 다이어그램 뷰 지원을 통해 시각적으로 파이프라인 및 데이터 집합을 작성합니다. 
- **이미 배포된 엔터티 찾아보기 및 상호 작용을 위해 서버 탐색기와 통합**: 서버 탐색기를 활용하여 이미 배포된 데이터 팩터리 및 해당 엔터티를 찾아봅니다. 배포된 데이터 팩터리 또는 엔터티(파이프라인, 연결된 서비스, 데이터 집합)를 프로젝트로 가져옵니다. 
- **스키마 유효성 검사와 다양한 기능의 Intellisense를 사용하는 JSON 편집**: 다양한 기능의 Intellisense와 스키마 유효성 검사를 사용하여 Data Factory 엔터티의 JSON 문서를 효율적으로 구성하고 편집합니다. 
- **다중 환경 게시**: 환경마다 별도의 구성 파일을 만들어 개발, 테스트 또는 프로덕션 환경에 작성된 파이프라인을 게시합니다.
- **Pig, Hive 및 .Net 기반 데이터 처리 지원**: Data Factory 프로젝트의 Pig 및 Hive 스크립트에 대한 지원입니다. .NET 작업 관리에 필요한 C# 프로젝트 참조에 대한 지원입니다.

##참고 항목

[Azure SDK 2.7 발표 게시물](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

<!---HONumber=July15_HO4-->