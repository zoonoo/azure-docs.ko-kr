---
title: "Visual Studio Online을 사용한 클라우드 서비스의 지속적인 전송 | Microsoft Docs"
description: "서비스 구성 파일에 진단 저장소 키를 저장하지 않고 Azure 클라우드 앱에 대해 지속적인 전송을 설정하는 방법 알아보기"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 148b2959-c5db-4e4a-a7e9-fccb252e7e8a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 7e70f92d4d1333ca6cbac5876e5ccbc763bd915c

---
# <a name="securely-save-cloud-services-diagnostics-storage-key-and-setup-continuous-integration-and-deployment-to-azure-using-visual-studio-online"></a>Visual Studio Online을 사용하여 클라우드 서비스 진단 저장소 키를 안전하게 저장하고 Azure에 대한 지속적인 통합 및 배포 설정
 이 방법은 오늘날의 오픈 소스 프로젝트에 일반적으로 사용됩니다. 구성 파일에 응용 프로그램 암호를 저장하는 방식은 암호가 공개 소스 제어에서 유출되어 보안 취약점에 노출되므로 더 이상 안전하지 않습니다. 클라우드 환경에서 빌드 서버가 공유되는 리소스일 수 있으므로 지속적인 통합 파이프라인의 파일에 일반 텍스트로 암호를 저장하는 것은 안전하지 않은 방법입니다. 이 문서에서는 개발 및 지속적인 통합 프로세스 중에 Visual Studio 및 Visual Studio Online이 보안 문제를 완화시키는 방법을 설명합니다.

## <a name="remove-diagnostics-storage-key-secret-in-project-configuration-file"></a>프로젝트 구성 파일에서 진단 저장소 키 암호 제거
클라우드 서비스 진단 확장에서는 진단 결과를 저장하기 위해 Azure Storage가 필요합니다. 이전에는 저장소 연결 문자열이 클라우드 서비스 구성(.cscfg) 파일에 지정되었으며 소스 제어에서 확인할 수 있었습니다. 최신 Azure SDK 릴리스에서는 키가 토큰으로 교체되어 부분 연결 문자열만 저장하는 방식으로 변경되었습니다. 다음 단계에서는 새 클라우드 서비스 도구 작동 원리에 대해 설명합니다.

### <a name="1-open-the-role-designer"></a>1. 역할 디자이너 열기
* 클라우드 서비스 역할을 두 번 클릭하거나 마우스 오른쪽 단추로 클릭하여 역할 디자이너를 엽니다.

![역할 디자이너 열기][0]

### <a name="2-under-diagnostics-section-a-new-check-box-dont-remove-storage-key-secret-from-project-is-added"></a>2. 진단 섹션에 새 확인란 "프로젝트에서 저장소 키 암호를 제거하지 마세요."가 추가됨
* 로컬 저장소 에뮬레이터를 사용하는 경우 로컬 연결 문자열 UseDevelopmentStorage=true에 대해 관리할 암호가 없으므로 이 확인란이 사용되지 않도록 설정됩니다.

![로컬 저장소 에뮬레이터 연결 문자열은 암호가 아님][1]

* 새 프로젝트를 만드는 경우 기본적으로 이 확인란이 선택되지 않습니다. 이로 인해 선택된 저장소 연결 문자열의 저장소 키 섹션이 토큰으로 대체됩니다. 토큰 값은 현재 사용자의 AppData Roaming 폴더(예: C:\Users\contosouser\AppData\Roaming\Microsoft\CloudService)에서 찾을 수 있습니다.

> user\AppData 폴더는 사용자 로그인에 의해 액세스가 제어되며 개발 비밀 정보를 저장할 안전한 곳으로 간주됩니다.
> 
> 

![저장소 키가 사용자 프로필 폴더에 저장됨][2]

### <a name="3-select-a-diagnostics-storage-account"></a>3. 진단 저장소 계정 선택
* "..." 단추를 클릭하여 시작된 대화 상자에서 저장소 계정을 선택합니다.  단추를 선택합니다. 생성된 저장소 연결 문자열에 저장소 계정 키가 어떻게 포함되지 않는지 알아봅니다.
* 예: DefaultEndpointsProtocol=https;AccountName=contosostorage;AccountKey=$(*clouddiagstrg.key*)

### <a name="4-debugging-the-project"></a>4.    프로젝트 디버그
* Visual Studio에서 F5 키를 눌러 디버그를 시작합니다. 모든 과정이 이전과 같은 방식으로 진행되어야 합니다.
  ![로컬로 디버그 시작][3]

### <a name="5-publish-project-from-visual-studio"></a>5. Visual Studio에서 프로젝트 게시
* 게시 대화 상자를 시작하고 로그인 지침에 따라 진행하여 Azure에 응용 프로그램을 게시합니다.

### <a name="6-additional-information"></a>6. 추가 정보
> 참고: 역할 디자이너의 설정 패널은 당분간 그대로 유지됩니다. 진단에 대해 암호 관리 기능을 사용하려는 경우 구성 탭으로 이동합니다.
> 
> 

![설정 추가][4]

> 참고: 사용하도록 설정되면 Application Insights 키가 일반 텍스트로 저장됩니다. 이 키는 중요한 데이터 손상될 위험이 없도록 업로드된 콘텐츠에만 사용됩니다.
> 
> 

## <a name="build-and-publish-a-cloud-services-project-using-visual-studio-online-task-templates"></a>Visual Studio Online 작업 템플릿을 사용하여 클라우드 서비스 프로젝트 빌드 및 게시
* 다음 단계에서는 Visual Studio Online 작업을 사용하여 클라우드 서비스 프로젝트에 대해 지속적인 통합을 설정하는 방법을 보여 줍니다.
  ### <a name="1-obtain-a-vso-account"></a>1.    VSO 계정 얻기
* 아직 없으면 [Visual Studio Online 계정 만들기][Visual Studio Online 계정 만들기]
* Visual Studio Online 계정에 [팀 프로젝트 만들기][팀 프로젝트 만들기]

### <a name="2-setup-source-control-in-visual-studio"></a>2.    Visual Studio에서 소스 제어 설정
* 팀 프로젝트에 연결합니다.

![팀 프로젝트에 연결][5]

![연결할 팀 프로젝트 선택][6]

* 소스 제어에 프로젝트를 추가합니다.

![소스 제어에 프로젝트 추가][7]

![소스 제어 폴더에 프로젝트 매핑][8]

* 팀 탐색기에서 프로젝트 체크 인

![소스 제어에 프로젝트 체크 인][9]

### <a name="3-configure-build-process"></a>3.    빌드 프로세스 구성
* 팀 프로젝트를 찾고 새 빌드 프로세스 템플릿을 추가합니다.

![새 빌드 추가][10]

* 빌드 작업 선택

![빌드 작업 추가][11]

![Visual Studio 빌드 작업 템플릿 선택][12]

* 빌드 작업 입력을 편집합니다. 필요에 따라 빌드 매개 변수를 사용자 지정하세요.

![빌드 작업 구성][13]

`/t:Publish /p:TargetProfile=$(targetProfile) /p:DebugType=None /p:SkipInvalidConfigurations=true /p:OutputPath=bin\ /p:PublishDir="$(build.artifactstagingdirectory)\\"`

* 빌드 변수를 구성합니다.

![빌드 변수를 구성합니다.][14]

* 빌드 저장 위치를 업로드하는 작업을 추가합니다.

![빌드 저장 위치 게시 작업 선택][15]

![빌드 저장 위치 게시 작업 구성][16]

* 빌드를 실행합니다.

![새 빌드 큐 대기][17]

![빌드 요약 보기][18]

* 빌드가 성공하는 경우 아래와 유사한 결과가 표시됩니다.

![빌드 결과][19]

### <a name="4-configure-release-process"></a>4.    릴리스 프로세스 구성
* 새 릴리스를 만듭니다.

![새 릴리스 만들기][20]

* Azure 클라우드 서비스 배포 작업을 선택합니다.

![Azure 클라우드 서비스 배포 작업 선택][21]

* 저장소 계정 키가 소스 제어에서 체크 인되지 않으므로 진단 확장을 설정하기 위한 비밀 키를 지정해야 합니다. **새 서비스 만들기에 대한 고급 옵션** 섹션을 확장하고 **진단 저장소 계정 키** 매개 변수 입력을 편집합니다. 이 입력은 **[RoleName]:$(StorageAccountKey)** 형식의 여러 줄의 키-값 쌍을 갖습니다.

> 참고: 진단 저장소 계정이 클라우드 서비스 응용 프로그램을 게시하는 동일한 구독에 포함되면 배포 작업 입력에 키를 입력할 필요가 없습니다. 배포는 구독에서 프로그래밍 방식으로 저장소 정보를 얻습니다.
> 
> 

![클라우드 서비스 배포 작업 구성][22]

* 암호 빌드 변수를 사용하여 저장소 키를 저장합니다. 변수를 암호로 마스크하려면 변수 입력 오른쪽에 있는 잠금 아이콘을 클릭합니다.

![암호 빌드 변수에 저장소 키 저장][23]

* 릴리스를 만들고 Azure에 프로젝트를 배포합니다.

![새 릴리스 만들기][24]

## <a name="next-steps"></a>다음 단계
Azure 클라우드 서비스에 대한 진단 확장을 설정하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 Azure 클라우드 서비스에 진단 사용][PowerShell을 사용하여 Azure 클라우드 서비스에 진단 사용]을 참조하세요.

[Visual Studio Online 계정 만들기]:https://www.visualstudio.com/team-services/
[팀 프로젝트 만들기]: https://www.visualstudio.com/it-it/docs/setup-admin/team-services/connect-to-visual-studio-team-services
[PowerShell을 사용하여 Azure 클라우드 서비스에 진단 사용]:https://azure.microsoft.com/en-us/documentation/articles/cloud-services-diagnostics-powershell/

[0]: ./media/cloud-services-vs-ci/vs-01.png
[1]: ./media/cloud-services-vs-ci/vs-02.png
[2]: ./media/cloud-services-vs-ci/file-01.png
[3]: ./media/cloud-services-vs-ci/vs-03.png
[4]: ./media/cloud-services-vs-ci/vs-04.png
[5]: ./media/cloud-services-vs-ci/vs-05.png
[6]: ./media/cloud-services-vs-ci/vs-06.png
[7]: ./media/cloud-services-vs-ci/vs-07.png
[8]: ./media/cloud-services-vs-ci/vs-08.png
[9]: ./media/cloud-services-vs-ci/vs-09.png
[10]: ./media/cloud-services-vs-ci/vso-01.png
[11]: ./media/cloud-services-vs-ci/vso-02.png
[12]: ./media/cloud-services-vs-ci/vso-03.png
[13]: ./media/cloud-services-vs-ci/vso-04.png
[14]: ./media/cloud-services-vs-ci/vso-05.png
[15]: ./media/cloud-services-vs-ci/vso-06.png
[16]: ./media/cloud-services-vs-ci/vso-07.png
[17]: ./media/cloud-services-vs-ci/vso-08.png
[18]: ./media/cloud-services-vs-ci/vso-09.png
[19]: ./media/cloud-services-vs-ci/vso-10.png
[20]: ./media/cloud-services-vs-ci/vso-11.png
[21]: ./media/cloud-services-vs-ci/vso-12.png
[22]: ./media/cloud-services-vs-ci/vso-13.png
[23]: ./media/cloud-services-vs-ci/vso-14.png
[24]: ./media/cloud-services-vs-ci/vso-15.png



<!--HONumber=Nov16_HO3-->


