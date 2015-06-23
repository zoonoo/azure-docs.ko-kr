<properties 
	pageTitle="소스 제어에 프로젝트 코드 저장 - Azure 모바일 서비스" 
	description="컴퓨터의 로컬 Git 리포지토리에 .NET 백 엔드 프로젝트를 저장하고 게시하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

<div class="dev-center-tutorial-subselector">
	<a href="/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/documentation/articles/mobile-services-store-scripts-source-control/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a>
</div>

# 소스 제어에 프로젝트 코드 저장

이 항목에서는 Azure 모바일 서비스에서 제공하는 소스 제어를 사용하여 .NET 백 엔드 서비스 프로젝트를 저장하는 방법을 보여 줍니다. 프로젝트를 게시하려면 로컬 Git 리포지토리에서 프로덕션 모바일 서비스로 업로드하면 됩니다.

이 자습서에서는 다음 단계를 안내합니다.

1. [모바일 서비스에서 원본 제어를 사용하도록 설정]합니다.
2. [Git을 설치하고 로컬 리포지토리를 만듭니다].
3. [Git를 사용하여 프로젝트 게시]

이 자습서를 완료하려면 기존에 [모바일 서비스 시작] 또는 [기존 앱에 모바일 서비스 추가] 자습서를 완료하여 모바일 서비스를 만들었어야 합니다.

## <a name="enable-source-control"></a>모바일 서비스에서 소스 제어를 사용하도록 설정

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

## <a name="clone-repo"></a>Git을 설치하고 로컬 리포지토리 만들기

1. 로컬 컴퓨터에 Git을 설치합니다. 

	Git를 설치하는 데 필요한 단계는 운영 체제마다 다릅니다. 운영 체제 특정 배포 및 설치 지침은 [Git 설치]를 참조하십시오.

	> [AZURE.NOTE]일부 운영 체제에서는 Git의 명령줄과 GUI 버전을 둘 다 사용할 수 있습니다. 이 문서에서 제공하는 지침은 명령줄 버전을 사용합니다.

2. **GitBash**(Windows) 또는 **Bash**(Unix Shell)와 같은 명령줄을 엽니다. OS X 시스템에서는 **터미널** 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

3. 명령줄에서 스크립트를 저장할 디렉터리로 변경합니다. 예: `cd SourceControl`.

4. 다음 명령을 사용하여 새 Git 리포지토리의 로컬 복사본을 만들고 `<your_git_URL>`을 모바일 서비스용 Git 리포지토리의 URL로 바꿉니다.

		git clone <your_git_URL>

5. 메시지가 표시되면 모바일 서비스에서 원본 제어를 사용하도록 설정할 때 설정한 사용자 이름 및 암호를 입력합니다. 성공적으로 인증된 후에는 다음과 같은 일련의 응답이 표시됩니다.

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. `git clone` 명령을 실행한 디렉터리로 이동하면 모바일 서비스 이름을 사용하여 새 디렉터리가 만들어졌음을 알 수 있습니다. .NET 백 엔드 모바일 서비스의 경우 git 리포지토리는 처음에 비어 있습니다.

로컬 리포지토리를 만들었으므로 이제 이 리포지토리에서 .NET 백 엔드 서비스 프로젝트를 게시할 수 있습니다.

## <a name="deploy-scripts"></a>Git를 사용하여 프로젝트 게시

1. Visual Studio 2013dptj .NET 백 엔드 모바일 서비스 프로젝트를 새로 만들거나 기존 프로젝트를 새 로컬 리포지토리로 이동합니다.  

	간단히 테스트하기 위해 모바일 서비스 퀵 스타트 프로젝트를 다운로드하여 이 폴더에 저장합니다.

2. packages.config 파일은 그대로 두고 NuGet 패키지 폴더를 모두 제거합니다.

	모바일 서비스에서 packages.confign 파일을 기반으로 NuGet 패키지 파일을 자동으로 복원합니다. 패키지 디렉터리가 추가되지 않도록 .gitignore 파일을 정의할 수도 있습니다.
 
3. Git 명령 프롬프트에서 새 스크립트 파일 추적을 시작하는 다음 명령을 입력합니다.

		$ git add .
	
4. 변경 내용을 커밋하는 다음 명령을 입력합니다.

		$ git commit -m "adding the .NET backend service project"

5. 다음 명령을 입력하여 원격 리포지토리에 변경 내용을 업로드한 다음 자격 증명을 제공합니다.

		$ git push origin master
	
	프로젝트가 모바일 서비스에 배포되고 패키지가 추가되고 서비스가 다시 시작되었음을 나타내는 일련의 명령이 표시됩니다.

6. .NET 백 엔드 모바일 서비스의 URL을 찾으면 다음과 같이 표시됩니다.

	![모바일 서비스 시작 페이지](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

이제 모바일 서비스 프로젝트가 소스 제어에서 유지 관리되며 로컬 리포지토리에서 업데이트를 푸시하기만 하면 서비스 업데이트를 게시할 수 있습니다. SQL 데이터베이스를 사용하는 .NET 백 엔드 모바일 서비스에서 데이터 모델을 변경하는 방법에 대한 자세한 내용은 [.NET 백 엔드 모바일 서비스에 대한 데이터 모델 변경 방법]을 참조하세요.

## <a name="next-steps"> </a>다음 단계

이 자습서를 완료했으므로 이제 원본 제어에 스크립트를 지정하는 방법을 알게 되었습니다. 이제 모바일 서비스에 대해 자세히 알아보세요.

+ [.NET 백 엔드 모바일 서비스에 대한 데이터 모델 변경 방법] 
	<br/> Entity Framework Code First 마이그레이션을 사용하여 기존 데이터의 손실 없이 기존 Azure SQL 데이터베이스에 대한 데이터 모델을 변경하는 방법을 보여줍니다. 	

+ [클라이언트에서 사용자 지정 API 호출] 
	<br/> 클라이언트에서 호출할 수 있는 사용자 지정 API를 만드는 방법을 보여줍니다.

<!-- Anchors. -->
[모바일 서비스에서 원본 제어를 사용하도록 설정]: #enable-source-control
[Git을 설치하고 로컬 리포지토리를 만듭니다]: #clone-repo
[Git를 사용하여 프로젝트 게시]: #deploy-scripts

<!-- Images. -->

<!-- URLs. -->
[Git website]: http://git-scm.com
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Git 설치]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[모바일 서비스 시작]: mobile-services-dotnet-backend-ios-get-started.md
[기존 앱에 모바일 서비스 추가]: mobile-services-dotnet-backend-ios-get-started-data.md
[Azure Management Portal]: https://manage.windowsazure.com/
[클라이언트에서 사용자 지정 API 호출]: mobile-services-dotnet-backend-ios-call-custom-api.md
[.NET 백 엔드 모바일 서비스에 대한 데이터 모델 변경 방법]: mobile-services-dotnet-backend-how-to-use-code-first-migrations.md

<!--HONumber=54--> 