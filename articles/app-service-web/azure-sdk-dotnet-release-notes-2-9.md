<properties 
   pageTitle="Azure SDK for .NET 2.9 릴리스 정보" 
   description="Azure SDK for .NET 2.9 릴리스 정보" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-.net-2.9-release-notes"></a>Azure SDK for .NET 2.9 릴리스 정보

##<a name="overview"></a>개요

이 문서에는 Azure SDK for .NET 2.9 릴리스의 릴리스 정보가 포함되어 있습니다. 

이 릴리스의 업데이트에 대한 자세한 정보는 [Azure SDK 2.9 발표 게시물](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)을 참조하세요.

## <a name="azure-sdk-2.9-for-visual-studio-2015-update-2-and-visual-studio-"15"-preview"></a>Visual Studio 2015 업데이트 2 및 Visual Studio "15" Preview용 Azure SDK 2.9
 
이 업데이트는 다음 버그 수정을 포함합니다.

- "알 수 없는 형식" 문자열이 코드 생성 폴더의 이름 및/또는 생성된 코드에 포함될 네임스페이스 이름으로 나타나는 REST API 클라이언트 생성과 관련된 문제
- 인증 정보가 스케줄러 프로비전 프로세스로 전달되지 못하는 예약된 WebJobs와 관련된 문제

이 업데이트는 다음 새로운 기능을 포함합니다.

- 앱 서비스 프로비전 대화 상자의 "서비스" 탭에서 보조 앱 서비스에 대한 지원 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a> Visual Studio 2015 업데이트 2용 Azure Data Lake 도구
 
이 업데이트는 다음을 포함합니다.

- **Azure Data Lake 도구** 가 Azure SDK for .NET 릴리스에 병합됩니다. 이 도구는 Azure SDK를 설치할 때 자동으로 설치됩니다. 

    이 도구는 자주 업데이트되며 업데이트를 받으려면 [여기](http://aka.ms/datalaketool) 로 이동하세요.

- **서버 탐색기** 를 통해 모든 항목을 보고 일부 U-SQL 메타데이터 엔터티를 만들 수 있습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/documentation/services/data-lake-analytics/) 를 참조하세요.


##<a name="hdinsight-tools"></a>HDInsight 도구 

**HDInsight 도구** 에서 Tez 그래프 표시 및 기타 언어 수정을 포함하여 HDInsight 버전 3.3을 지원합니다.


##<a name="azure-resource-manager"></a>Azure 리소스 관리자 

이 릴리스는 ARM 템플릿에 대한 [KeyVault](../resource-manager-keyvault-parameter.md) 지원을 추가합니다.

##<a name="see-also"></a>참고 항목

[Azure SDK 2.9 발표 게시물](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)



<!--HONumber=Oct16_HO2-->


