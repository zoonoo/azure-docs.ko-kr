<properties linkid="develop-java-how-to-hudson-ci" urlDisplayName="Hudson Continuous Integration" pageTitle="How to use Hudson with the Azure Blob service | Microsoft Azure" metaKeywords="Hudson, Azure storage, Azure Blob service, Azure storage, Azure hudson" description="Describes how to use Hudson with Azure Blob storage as a repository for build artifacts." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Hudson Continuous Integration solution" authors="waltpo" solutions="" manager="" editor="mollybos" />

Hudson Continuous Integration 솔루션과 함께 Azure 저장소 사용
=============================================================

*[Microsoft Open Technologies Inc.](http://msopentech.com) 제공*

다음 정보는 Azure Blob 서비스를 Hudson CI(Continuous Integration) 솔루션에서 만든 빌드 아티팩트의 리포지토리로 사용하는 방법을 보여 줍니다. 이는 민첩한 개발 환경(Java 또는 다른 언어 사용)에서 코딩하고 빌드가 연속 통합을 기반으로 실행 중이며, 다른 조직 구성원이나 고객과 빌드 아티팩트를 공유하거나 보관 파일을 유지 관리할 수 있도록 빌드 아티팩트의 리포지토리가 필요한 경우와 같은 시나리오에서 유용합니다.

이 자습서에서는 Microsoft Open Technologies, Inc.에서 제공하는 Hudson CI용 Azure 저장소 플러그 인을 사용합니다.

목차
----

-   [Hudson 개요](#overview)
-   [Blob 서비스를 사용할 경우의 이점](#benefits)
-   [필수 조건](#prerequisites)
-   [Hudson CI로 Blob 서비스를 사용하는 방법](#howtouse)
-   [Azure 저장소 플러그 인을 설치하는 방법](#howtoinstall)
-   [저장소 계정을 사용하도록 Azure 저장소 플러그 인을 구성하는 방법](#howtoconfigure)
-   [저장소 계정에 빌드 아티팩트를 업로드하는 빌드 후 작업을 만드는 방법](#howtocreatepostbuild)
-   [Blob 서비스에 사용되는 구성 요소](#components)

개요Hudson 개요
---------------

Hudson은 개발자가 코드 변경을 쉽게 통합하고 자동으로 자주 빌드를 생성하도록 하여 개발자의 생산성을 증가시켜 소프트웨어 프로젝트의 연속 통합을 가능하게 합니다. 빌드는 버전이 있으며 빌드 아티팩트는 다양한 저장소에 업로드할 수 있습니다. 이 항목에서는 Azure Blob 저장소를 빌드 아티팩트의 리포지토리로 사용하는 방법을 보여 줍니다.

Hudson에 관한 자세한 정보는 [Meet Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)(영문)에서 찾을 수 있습니다.

이점Blob 서비스를 사용할 경우의 이점
------------------------------------

Blob 서비스를 사용하여 민첩한 개발 빌드 아티팩트를 호스트하는 경우의 이점은 다음과 같습니다.

-   빌드 아티팩트의 고가용성
-   Hudson CI 솔루션이 빌드 아티팩트를 업로드할 때의 성능
-   고객과 파트너가 빌드 아티팩트를 다운로드할 때의 성능
-   익명 액세스, 만료 기준 공유 액세스 서명 액세스, 개인 액세스 등에서 선택하여 사용자 액세스 정책 제어

필수 조건필수 조건
------------------

Hudson CI 솔루션으로 Blob 서비스를 사용하려면 다음이 있어야 합니다.

-   Hudson 연속 통합 솔루션

    현재 Hudson CI 솔루션이 없는 경우 다음 기술을 사용하여 Hudson CI 솔루션을 실행할 수 있습니다.

    1.  Java 사용이 가능한 컴퓨터에서 http://hudson-ci.org/ (영문)에서 Hudson WAR을 다운로드합니다.
    2.  Hudson WAR이 포함된 폴더로 열리는 명령 프롬프트에서 Hudson WAR을 실행합니다. 예를 들어 버전 3.0.1을 다운로드한 경우는 다음과 같습니다.

        `java -jar hudson-3.0.1.war`

    3.  브라우저에서 `http://localhost:8080/`을 입력합니다. 그러면 Hudson 대시보드가 열립니다.

    4.  Hudson을 처음 사용하는 경우 `http://localhost:8080/`에서 초기 설치를 완료합니다.

    5.  초기 설치를 완료한 후 실행 중인 Hudson WAR의 인스턴스를 취소하고 Hudson WAR을 다시 시작한 다음 Azure 저장소 플러그 인을 설치 및 구성하는 데 사용할 Hudson 대시보드(`http://localhost:8080/`)를 다시 엽니다.

        일반적인 Hudson CI 솔루션은 서비스로 실행되도록 설정되지만 이 자습서에서는 명령줄에서 Hudson WAR을 실행해도 충분합니다.

-   Azure 계정. http://www.windowsazure.com 에서 Azure 계정을 등록할 수 있습니다.

-   Azure 저장소 계정. 저장소 계정이 없으면 [저장소 계정을 만드는 방법](http://go.microsoft.com/fwlink/?LinkId=279823)에 나온 단계를 통해 계정을 만들 수 있습니다.

-   Hudson CI 솔루션은 필수는 아니지만 알아 두는 것이 좋습니다. 다음 내용에서 기본 예제를 통해 Blob 서비스를 Hudson CI 빌드 아티팩트의 리포지토리로 사용할 때 필요한 단계를 보여 주기 때문입니다.

Blob 서비스를 사용하는 방법Hudson CI로 Blob 서비스를 사용하는 방법
------------------------------------------------------------------

Hudson으로 Blob 서비스를 사용하려면 Azure 저장소 플러그 인을 설치하고 저장소 계정을 사용하도록 플러그 인을 구성한 다음 빌드 아티팩트를 저장소 계정에 업로드하는 빌드 후 작업을 만들어야 합니다. 이러한 단계는 다음 섹션에 설명되어 있습니다.

설치하는 방법Azure 저장소 플러그 인을 설치하는 방법
---------------------------------------------------

1.  Hudson 대시보드에서 **Manage Hudson**을 클릭합니다.
2.  **Manage Hudson** 페이지에서 **Manage Plugins**를 클릭합니다.
3.  **Available** 탭을 클릭합니다.
4.  **Others**를 클릭합니다.
5.  **Artifact Uploaders** 섹션에서 **Azure Storage plugin**을 확인합니다.
6.  **Install**을 클릭합니다.
7.  설치가 완료되면 Hudson을 다시 시작합니다.

구성하는 방법저장소 계정을 사용하도록 Azure 저장소 플러그 인을 구성하는 방법
----------------------------------------------------------------------------

1.  Hudson 대시보드에서 **Manage Hudson**을 클릭합니다.
2.  **Manage Hudson** 페이지에서 **Configure System**을 클릭합니다.
3.  **Azure Storage Account Configuration** 섹션에서
    1.  Azure 포털 https://manage.windowsazure.com 에서 얻을 수 있는 저장소 계정 이름을 입력합니다.
    2.  역시 Azure 포털에서 얻을 수 있는 저장소 계정 키를 입력합니다.
    3.  공용 Azure 클라우드를 사용 중이면 **Blob Service Endpoint URL**에 대해 기본값을 사용합니다. 다른 Azure 클라우드를 사용 중이면 저장소 계정의 Azure 관리 포털에 지정된 대로 끝점을 사용합니다.
    4.  **Validate Storage Credentials**를 클릭하여 저장소 계정의 유효성을 검사합니다.
    5.  [옵션] Hudson CI에 사용할 수 있는 추가 저장소 계정이 있으면 **Add more Storage Accounts**를 클릭합니다.
    6.  **Save**를 클릭하여 설정을 저장합니다.

빌드 후 작업을 만드는 방법빌드 아티팩트를 저장소 계정에 업로드하는 빌드 후 작업을 만드는 방법
---------------------------------------------------------------------------------------------

설명할 목적으로 우선 몇 개의 파일을 만들 작업을 만든 다음 그 파일을 저장소 계정에 업로드할 빌드 후 작업을 추가해야 합니다.

1.  Hudson 대시보드에서 **New Job**을 클릭합니다.
2.  **MyJob**이라고 이름을 지정하고 **Build a free-style software job**을 클릭한 후 **OK**를 클릭합니다.
3.  작업 구성의 **Build** 섹션에서 **Add build step**을 클릭하고 **Execute Windows batch command**를 선택합니다.
4.  **Command**에서 다음 명령을 사용합니다.

         md text
         cd text
         echo Hello Azure Storage from Hudson > hello.txt
         date /t > date.txt
         time /t >> date.txt

5.  작업 구성의 **Post-build Actions** 섹션에서 **Upload artifacts to Azure Blob storage**를 클릭합니다.
6.  **Storage Account Name**에서는 사용할 저장소 계정을 선택합니다.
7.  **Container Name**에는 컨테이너 이름을 지정합니다. (컨테이너는 빌드 아티팩트가 업로드될 때 없으면 만들어집니다.) 환경 변수를 사용할 수 있으므로 이 예제의 경우 컨테이너 이름으로 **${JOB\_NAME}**을 입력합니다.

    **팁**

    **Execute Windows batch command** 스크립트를 입력한 아래 **Command** 섹션은 Hudson에서 인식하는 환경 변수에 대한 링크입니다. 환경 변수 이름과 설명을 알아보려면 이 링크를 클릭합니다. **BUILD\_URL** 환경 변수와 같은 특수 문자가 포함된 환경 변수는 컨테이너 이름이나 일반 가상 경로로 사용할 수 없습니다.

8.  이 예제의 경우 **Make container public**을 클릭합니다. (개인 컨테이너를 사용하려는 경우 액세스를 허용하려면 공유 액세스 서명을 만들어야 합니다. 이 내용은 이 항목에서는 다루지 않습니다. [공유 액세스 서명 만들기](http://go.microsoft.com/fwlink/?LinkId=279889)에서 공유 액세스 서명에 대한 자세한 내용을 알아볼 수 있습니다.)
9.  **List of Artifacts to upload**에는 **text/\*.txt**를 입력합니다.
10. **Common virtual path for uploaded artifacts**에는 **${BUILD\_ID}/${BUILD\_NUMBER}**를 입력합니다.
11. **Save**를 클릭하여 설정을 저장합니다.
12. Hudson 대시보드에서 **Build Now**를 클릭하여 **MyJob**을 실행합니다. 상태에 대한 콘솔 출력을 검사합니다. Azure 저장소의 상태 메시지는 빌드 후 작업이 빌드 아티팩트를 업로드하기 시작할 때 콘솔 출력에 포함됩니다.
13. 작업이 성공적으로 완료되었을 때 공용 Blob을 열어 빌드 아티팩트를 검사할 수 있습니다.
    1.  Azure 관리 포털 https://manage.windowsazure.com 에 로그인합니다.
    2.  **저장소**를 클릭합니다.
    3.  Hudson에 사용할 저장소 계정 이름을 클릭합니다.
    4.  **컨테이너**를 클릭합니다.
    5.  Hudson 작업을 만들었을 때 할당한 작업 이름의 소문자 버전인 **myjob**이라는 컨테이너를 클릭합니다. 컨테이너 이름 및 Blob 이름은 Azure 저장소에서 소문자(및 대/소문자 구분)입니다. **myjob** 컨테이너의 Blob 목록에 **hello.txt**와 **date.txt**가 표시됩니다. 이 항목 중 하나의 URL을 복사하여 브라우저에서 엽니다. 빌드 아티팩트로 업로드된 텍스트 파일이 표시됩니다.

Blob 서비스 구성 요소Blob 서비스에 사용되는 구성 요소
-----------------------------------------------------

다음은 Blob 서비스 구성 요소의 개요를 제공합니다.

-   **저장소 계정**: Azure 저장소에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 이는 Blob 액세스를 위한 가장 높은 수준의 네임스페이스입니다. 전체 크기가 100TB를 초과하지 않을 경우 한 계정에 포함될 수 있는 컨테이너 수는 제한이 없습니다.
-   **컨테이너**: 컨테이너는 Blob 집합 그룹화를 제공합니다. 모든 Blob은 컨테이너에 있어야 합니다. 한 계정에 포함될 수 있는 컨테이너 수에는 제한이 없습니다. 한 컨테이너에 저장될 수 있는 Blob 수에도 제한이 없습니다.
-   **Blob**: 임의 형식 및 크기의 파일입니다. Azure 저장소에 저장할 수 있는 Blob 유형에는 블록 Blob과 페이지 Blob의 두 가지가 있습니다. 대부분의 파일은 블록 Blob입니다. 단일 블록 Blob의 크기는 최대 200GB일 수 있습니다. 이 자습서에서는 블록 Blob을 사용합니다. 다른 Blob 유형인 페이지 Blob의 크기는 최대 1TB일 수 있으며, 파일의 바이트 범위가 자주 수정되는 경우 더 효율적입니다. Blob에 대한 자세한 내용은 [블록 Blob 및 페이지 Blob 이해](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee691964.aspx)를 참조하십시오.
-   **URL 형식**: 다음 URL 형식을 사용하여 Blob에 주소를 지정할 수 있습니다.

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (위 형식은 공용 Azure 클라우드에 적용됩니다. 다른 Azure 클라우드를 사용 중이면 Azure 관리 포털 내의 끝점을 사용하여 URL 끝점을 확인합니다.)

    위 형식에서 `storageaccount`는 저장소 계정 이름을 나타내고, `container_name`은 컨테이너 이름을 나타내고, `blob_name`은 Blob 이름을 각각 나타냅니다. 컨테이너 이름에는 슬래시(**/**)로 구분하여 여러 경로를 가질 수 있습니다. 이 자습서에서 컨테이너 이름의 예는 **MyJob**이었고 **${BUILD\_ID}/${BUILD\_NUMBER}**는 일반 가상 경로에 사용되었으므로 Blob의 URL 형식은 다음과 같습니다.

    `http://example.blob.core.windows.net/myjob/2013-06-06_11-56-22/1/hello.txt`


