<properties linkid="develop-java-tutorials-jenkins-continuous-integration" urlDisplayName="Jenkins Continuous Integration" pageTitle="Using Azure Storage with a Jenkins Continuous Integration Solution | Microsoft Azure" metaKeywords="" description="This tutorial show how to use the Azure blob service as a repository for build artifacts created by a Jenkins continuous integration solution." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Jenkins Continuous Integration solution" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Jenkins 연속 통합 솔루션과 함께 Azure 저장소 사용
=================================================

*[Microsoft Open Technologies Inc. 제공](http://msopentech.com)*

다음 정보는 Azure Blob 서비스를 Jenkins CI(연속 통합) 솔루션으로 만든 빌드 아티팩트의 리포지토리로 사용하는 방법을 보여 줍니다. 이 방법은 Agile 개발 환경에서 Java 또는 다른 언어를 사용하여 코딩하고 연속 통합을 기반으로 빌드가 실행되는 환경에서 빌드 아티팩트를 다른 조직 구성원, 고객과 공유하고 보관 파일을 유지 관리하기 위해 빌드 아티팩트 리포지토리가 필요한 경우 등에 유용합니다.

이 자습서에서는 Microsoft Open Technologies, Inc.가 제공하는 Jenkins CI용 Azure 저장소 플러그인을 사용하게 됩니다.

목차
----

-   [Jenkins 개요](#overview)
-   [Blob 서비스 사용의 이점](#benefits)
-   [필수 조건](#prerequisites)
-   [Blob 서비스를 Jenkins CI와 함께 사용하는 방법](#howtouse)
-   [Azure 저장소 플러그인을 설치하는 방법](#howtoinstall)
-   [저장소 계정을 사용하도록 Azure 저장소 플러그인을 구성하는 방법](#howtoconfigure)
-   [빌드 아티팩트를 저장소 계정으로 업로드하는 빌드 후 작업을 만드는 방법](#howtocreatepostbuild)
-   [Blob 서비스에서 사용하는 구성 요소](#components)

개요Jenkins 개요
----------------

Jenkins는 개발자가 손쉽게 코드 변경 사항을 통합하고 빌드를 자동으로 자주 생성할 수 있게 하여 소프트웨어 프로젝트의 연속 통합을 가능케 함으로써 개발자의 생산성을 높여 줍니다. 빌드에는 버전이 지정되고 빌드 아티팩트는 다양한 리포지토리에 업로드될 수 있습니다. 이 항목에서는 Azure Blob 저장소를 빌드 아티팩트의 리포지토리로 사용하는 방법을 보여 줍니다.

Jenkins에 대한 자세한 내용은 [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)에서 찾을 수 있습니다.

이점Blob 서비스 사용의 이점
---------------------------

Blob 서비스를 사용하여 Agile 개발 빌드 아티팩트를 호스트할 경우의 이점은 다음과 같습니다.

-   빌드 아티팩트의 고가용성
-   Jenkins CI 솔루션이 빌드 아티팩트를 업로드할 때의 성능
-   고객 및 파트너가 빌드 아티팩트를 다운로드할 때의 성능
-   익명 액세스, 만료 기반 공유 액세스 서명 액세스, 전용 액세스 중에서 선택하여 사용자 액세스 정책 제어

필수 조건필수 조건
------------------

Blob 서비스를 Jenkins CI 솔루션과 함께 사용하려면 다음 항목이 필요합니다.

-   Jenkins 연속 통합 솔루션

    Jenkins CI 솔루션이 아직 없으면 다음 방법을 통해 Jenkins CI 솔루션을 실행할 수 있습니다.

    1.  Java를 사용할 수 있는 컴퓨터에서 http://jenkins-ci.org 로부터 jenkins.war를 다운로드합니다.
    2.  jenkins.war를 포함하는 폴더로 열려 있는 명령 프롬프트에서 다음을 실행합니다.

        `java -jar jenkins.war`

    3.  브라우저에서 `http://localhost:8080/`을 엽니다. 그러면 Azure 저장소 플러그인을 설치 및 구성하게 될 Jenkins 대시보드가 열립니다.

        일반적인 Jenkins CI 솔루션은 서비스로 실행하도록 설정되지만, 이 자습서에서는 Jenkins war를 명령줄에서 실행하는 것으로 충분합니다.

-   Azure 계정. Azure 계정은 http://www.windowsazure.com 에서 등록할 수 있습니다.

-   Azure 저장소 계정. 저장소 계정이 아직 없으면 [저장소 계정을 만드는 방법](http://go.microsoft.com/fwlink/?LinkId=279823)에 설명된 단계를 따라 계정을 만들 수 있습니다.

-   Jenkins CI 솔루션에 익숙하면 좋지만 반드시 그러해야 하는 것은 아닙니다. 아래에서는 Jenkins CI 빌드 아티팩트를 위한 리포지토리로 Blob 서비스를 사용할 때 필요한 단계를 보여 주기 위해 기본적인 예를 사용합니다.

Blob 서비스를 사용하는 방법Blob 서비스를 Jenkins CI와 함께 사용하는 방법
------------------------------------------------------------------------

Blob 서비스를 Jenkins와 함께 사용하려면 Azure 저장소 플러그인을 설치하고 저장소 계정을 사용하도록 플러그인을 구성한 후, 빌드 아티팩트를 저장소 계정에 업로드하는 빌드 후 작업을 만들어야 합니다. 이와 관련된 단계가 다음 섹션에 설명되어 있습니다.

설치 방법Azure 저장소 플러그인을 설치하는 방법
----------------------------------------------

1.  Jenkins 대시보드 내에서 **Manage Jenkins**를 클릭합니다.
2.  **Manage Jenkins** 페이지에서 **플러그 인 관리**를 클릭합니다.
3.  **사용 가능** 탭을 클릭합니다.
4.  **Artifact Uploaders** 섹션에서 **Azure Storage plugin**을 선택합니다.
5.  **Install without restart** 또는 **Download now and install after restart**를 클릭합니다.
6.  Jenkins를 다시 시작합니다.

구성 방법저장소 계정을 사용하도록 Azure 저장소 플러그인을 구성하는 방법
-----------------------------------------------------------------------

1.  Jenkins 대시보드 내에서 **Manage Jenkins**를 클릭합니다.
2.  **Manage Jenkins** 페이지에서 **시스템 구성**을 클릭합니다.
3.  **Azure Storage Account Configuration** 섹션에서 다음을 수행합니다.
    1.  저장소 계정 이름을 입력합니다. 이 이름은 Azure 포털 https://manage.windowsazure.com 에서 확인할 수 있습니다.
    2.  Azure 포털에서 확인 가능한 저장소 계정 키도 입력합니다.
    3.  공용 Azure 클라우드를 사용 중이면 **Blob Service Endpoint URL**에 기본값을 사용합니다. 다른 Azure 클라우드를 사용하고 있으면 Azure 관리 포털에서 저장소 계정에 대해 지정된 끝점을 사용합니다.
    4.  **Validate Storage Credentials**를 클릭하여 저장소 계정 유효성을 검사합니다.
    5.  [옵션] Jenkins CI에서 더 많은 저장소 계정을 사용할 수 있게 하려면 **Add more Storage Accounts**를 클릭합니다.
    6.  **저장**을 클릭하여 설정을 저장합니다.

빌드 후 작업을 만드는 방법빌드 아티팩트를 저장소 계정으로 업로드하는 빌드 후 작업을 만드는 방법
-----------------------------------------------------------------------------------------------

교육 목적상 먼저 여러 파일을 생성할 작업을 만든 후에 해당 파일을 저장소 계정에 업로드하기 위해 빌드 후 작업을 추가할 것입니다.

1.  Jenkins 대시보드 내에서 **새 작업**을 클릭합니다.
2.  작업 이름을 **MyJob**으로 지정하고 **Build a free-style software project**를 클릭한 후 **확인**을 클릭합니다.
3.  작업 구성의 **빌드** 섹션에서 **Add build step**을 추가하고 **Execute Windows batch command**를 선택합니다.
4.  **명령**에서 다음 명령을 사용합니다.

         md text
         cd text
         echo Hello Azure Storage from Jenkins > hello.txt
         date /t > date.txt
         time /t >> date.txt

5.  작업 구성의 **Post-build Actions** 섹션에서 **Add post-build action**을 클릭하고 **Upload artifacts to Azure Blob storage**를 선택합니다.
6.  **저장소 계정 이름**에 대해 사용할 저장소 계정을 선택합니다.
7.  **컨테이너 이름**에 대해 컨테이너 이름을 지정합니다. 빌드 아티팩트가 업로드될 때 컨테이너가 아직 없으면 컨테이너가 만들어집니다. 환경 변수를 사용할 수 있으며, 이 예제에서는 컨테이너 이름으로 **${JOB\_NAME}**를 입력합니다.

    **팁**

    **Execute Windows batch command**에 대해 스크립트를 입력한 **명령** 섹션 아래에는 Jenkins에 의해 인식되는 환경 변수에 대한 링크가 있습니다. 환경 변수 이름 및 설명을 알아보려면 이 링크를 클릭하십시오. 특수 문자를 포함하는 **BUILD\_URL** 등의 환경 변수는 컨테이너 이름 또는 공통 가상 경로로 허용되지 않습니다.

8.  이 예에 맞게 **Make container public**을 클릭합니다. 개인 컨테이너를 사용하려면 공유 액세스 서명을 만들어 액세스를 허용해야 합니다. 이에 관한 내용은 이 항목의 범위를 벗어납니다. 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명 만들기](http://go.microsoft.com/fwlink/?LinkId=279889)에서 확인할 수 있습니다.
9.  **List of Artifacts to upload**에 대해 **text/\*.txt**를 입력합니다.
10. **Common virtual path for uploaded artifacts**에 대해 **${BUILD\_ID}/${BUILD\_NUMBER}**를 입력합니다.
11. **저장**을 클릭하여 설정을 저장합니다.
12. Jenkins 대시보드에서 **Build Now**를 클릭하여 **MyJob**을 실행합니다. 콘솔 출력을 점검하여 상태를 확인합니다. 빌드 후 작업이 빌드 아티팩트를 업로드하기 시작하면 Azure 저장소 상태 메시지가 콘솔 출력에 포함됩니다.
13. 작업이 성공적으로 완료되면 공용 Blob을 열어 빌드 아티팩트를 점검할 수 있습니다.
    1.  Azure 관리 포털 https://manage.windowsazure.com 에 로그인합니다.
    2.  **저장소**를 클릭합니다.
    3.  Jenkins에 사용한 저장소 계정 이름을 클릭합니다.
    4.  **컨테이너**를 클릭합니다.
    5.  **myjob**이라는 이름의 컨테이너를 클릭합니다. 이 이름은 Jenkins 작업을 만들 때 할당한 작업 이름의 소문자 버전입니다. Azure 저장소에서 컨테이너 이름 및 Blob 이름은 소문자이고 대/소문자를 구분합니다. **myjob** 컨테이너의 Blob 목록 내에서 **hello.txt** 및 **date.txt**가 표시됩니다. 두 항목 중 하나의 URL을 복사하고 브라우저에서 해당 URL을 엽니다. 그러면 업로드한 텍스트 파일이 빌드 아티팩트로 표시됩니다.

Blob 서비스 구성 요소Blob 서비스에서 사용하는 구성 요소
-------------------------------------------------------

아래에는 Blob 서비스 구성 요소에 대한 개요가 나와 있습니다.

-   **저장소 계정**: Azure 저장소에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 이는 Blob 액세스를 위한 가장 높은 수준의 네임스페이스입니다. 전체 크기가 100TB를 초과하지 않을 경우 한 계정에 포함될 수 있는 컨테이너 수는 제한이 없습니다.
-   **컨테이너**: 컨테이너는 Blob 집합 그룹화를 제공합니다. 모든 Blob은 컨테이너에 있어야 합니다. 한 계정에 포함될 수 있는 컨테이너 수에는 제한이 없습니다. 한 컨테이너에 저장될 수 있는 Blob 수에도 제한이 없습니다.
-   **Blob**: 임의 형식 및 크기의 파일입니다. Azure 저장소에 저장할 수 있는 Blob 유형에는 블록 Blob과 페이지 Blob의 두 가지가 있습니다. 대부분의 파일은 블록 Blob입니다. 단일 블록 Blob의 크기는 최대 200GB일 수 있습니다. 이 자습서에서는 블록 Blob을 사용합니다. 다른 Blob 유형인 페이지 Blob의 크기는 최대 1TB일 수 있으며, 파일의 바이트 범위가 자주 수정되는 경우 더 효율적입니다. Blob에 대한 자세한 내용은 [블록 Blob 및 페이지 Blob 이해](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee691964.aspx)를 참조하십시오.
-   **URL 형식**: 다음 URL 형식을 사용하여 Blob에 주소를 지정할 수 있습니다.

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    위의 형식은 공용 Azure 클라우드에 적용됩니다. 다른 Azure 클라우드를 사용하고 있으면 Azure 관리 포털 내의 끝점을 사용하여 URL 끝점을 확인하십시오.

    위의 형식에서 `storageaccount`는 저장소 계정의 이름을 나타내고, `container_name`은 컨테이너의 이름을 나타내고, `blob_name`은 Blob의 이름을 나타냅니다. 컨테이너 이름 내에 슬래시(**/**)로 구분하여 여러 경로를 포함할 수 있습니다. 이 자습서에서 예로 든 컨테이너 이름은 **MyJob**이고 공통 가상 경로에 **${BUILD\_ID}/${BUILD\_NUMBER}**가 사용되었기 때문에 Blob의 URL은 다음과 같이 됩니다.

    `http://example.blob.core.windows.net/myjob/2013-01-28_15-00-35/2/hello.txt`


