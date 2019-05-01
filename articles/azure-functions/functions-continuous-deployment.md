---
title: Azure Functions에 대한 연속 배포 | Microsoft 문서
description: 함수를 게시 하려면 Azure App Service의 연속 배포 기능을 사용 합니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943892"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions에 대한 연속 배포
Azure Functions를 통해 지속적인 통합을 사용 하 여 함수 앱을 배포 하기 쉽습니다. 주요 코드 리포지토리 및 배포 소스를 통합 하는 함수입니다. 이 통합에는 이러한 서비스 트리거 배포 중 하나를 통해 Azure에 대 한 함수 코드를 업데이트 하는 워크플로 수 있습니다. Azure Functions를 처음 접하는 경우 시작 합니다 [Azure Functions 개요](functions-overview.md)합니다.

연속 배포는 여러를 통합 하는 프로젝트에 적합 한 옵션 및 자주 사용 하는 기여입니다. 또한 함수 코드에서 소스 제어를 유지 관리할 수 있습니다. Azure Functions에는 다음 배포 원본을 지원 합니다.

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* 외부 리포지토리(Git 또는 Mercurial)
* [Git 로컬 리포지토리](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

배포는 함수 앱별로 구성됩니다. 연속 배포가 활성화된 후 포털에서 함수 코드에 대한 액세스는 *읽기 전용*으로 설정됩니다.

## <a name="requirements-for-continuous-deployment"></a>연속 배포를 위한 요구 사항

연속 배포를 설정 하기 전에 배포 원본에 구성 된 배포 원본 및 함수 코드가 있어야 합니다. 함수 앱 배포에서 각 함수는 디렉터리 이름은 함수의 이름, 명명 된 하위 디렉터리에 있습니다.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Azure DevOps에서 배포할 수 있으려면 먼저 Azure 구독과 Azure DevOps 조직을 연결해야 합니다. 자세한 내용은 [Set up billing for your Azure DevOps organization](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal)(Azure DevOps 조직에 대한 청구 설정)을 참조하세요.

## <a name="set-up-continuous-deployment"></a>연속 배포 설정
다음 절차를 사용하여 기존 함수 앱에 대한 연속 배포를 구성합니다. 다음 단계에서는 GitHub 리포지토리와의 통합을 보여 주지만 Azure DevOps 또는 기타 배포 서비스에도 유사한 단계가 적용됩니다.

1. 함수 앱에는 [Azure portal](https://portal.azure.com)를 선택 **플랫폼 기능** > **배포 옵션**합니다. 
   
    ![배포 옵션을 열기 위한 선택 항목](./media/functions-continuous-deployment/setup-deployment.png)
 
1. 에 **배포** 블레이드에서 **설치**합니다.
 
    ![배포 블레이드](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. 에 **배포 원본** 블레이드에서 **원본 선택**합니다. 선택한 배포 원본에 대 한 정보를 입력 하 고 선택한 **확인**합니다.
   
    ![배포 원본 선택](./media/functions-continuous-deployment/choose-deployment-source.png)

연속 배포를 설정한 후에 모든 파일 변경 내용 배포 원본에 함수 앱에 복사 되 고 전체 사이트 배포가 트리거됩니다. 원본의 파일이 업데이트될 때 사이트가 다시 배포됩니다.

## <a name="deployment-scenarios"></a>배포 시나리오

일반적인 배포 시나리오에는 스테이징 배포를 만들고 기존 함수를 연속 배포로 이동 포함 됩니다.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>스테이징 배포 만들기

함수 앱 배포 슬롯을 아직 지원 하지 않습니다. 하지만 연속 통합을 사용 하 여 별도 스테이징 및 프로덕션 배포를 계속 관리할 수 있습니다.

구성할 프로세스 및 스테이징 배포 작업은 일반적으로 다음과 같습니다.

1. 구독에 두 개의 함수 앱을 만듭니다: 프로덕션 코드와 스테이징에 대 한 합니다. 

1. 아직 없는 경우 배포 원본을 만듭니다. 이 예제에서는 [GitHub]를 사용합니다.

1. 프로덕션 함수 앱의 경우 [연속 배포 설정](#set-up-continuous-deployment)에서 앞의 단계를 완료하고 GitHub 리포지토리의 마스터 분기에 배포 분기를 설정합니다.
   
    ![배포 분기를 선택 하 여 선택 항목](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. 스테이징 함수 앱에 대 한 3 단계를 반복 하려면 GitHub 리포지토리에서 스테이징 분기를 대신 선택 합니다. 배포 원본이 분기를 지원하지 않는 경우 다른 폴더를 사용합니다.
    
1. 스테이징 분기 또는 폴더의 코드를 업데이트 하 고 스테이징 배포에 이러한 변경 내용을 반영 하는지 확인 합니다.

1. 테스트 후 스테이징 분기의 변경 내용을 마스터 분기로 병합합니다. 이렇게 병합하면 프로덕션 함수 앱으로 트리거 배포가 트리거됩니다. 배포 원본이 분기를 지원하지 않는 경우 스테이징 폴더의 파일로 프로덕션 폴더의 파일을 덮어씁니다.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>기존 함수를 연속 배포로 이동
만들고 FTP를 사용 하 여 함수 코드 파일을 다운로드 해야 하거나 앞에서 설명한 대로 먼저 로컬 Git 리포지토리의 연속 배포 설정 수는 포털에서 유지 관리 하는 기존 함수가 있는 경우. 함수 앱에 대 한 Azure App Service 설정에서 수행할 수 있습니다. 파일을 다운로드 한 후 선택한 연속 배포 원본에 업로드할 수 있습니다.

> [!NOTE]
> 연속 통합을 구성한 후에 Functions 포털에서 소스 파일을 더 이상 편집할 수 없습니다.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>배포 자격 증명 구성
FTP 또는 로컬 Git 리포지토리를 사용 하 여 함수 앱에서 파일을 다운로드할 수 있습니다, 전에 사이트에 액세스 하려면 자격 증명을 구성 해야 합니다. 자격 증명은 함수 앱 수준에서 설정 됩니다. 다음 단계를 사용하여 Azure Portal에서 배포 자격 증명을 설정합니다.

1. 함수 앱에는 [Azure portal](https://portal.azure.com)를 선택 **플랫폼 기능** > **배포 자격 증명**합니다.
   
1. 사용자 이름과 암호를 입력 한 다음 선택 **저장할**합니다. 

   ![로컬 배포 자격 증명을 설정 하 여 선택 항목](./media/functions-continuous-deployment/setup-deployment-credentials.png)

이제 해당 자격 증명을 사용하여 FTP 또는 기본 제공 Git 리포지토리에서 함수 앱에 액세스할 수 있습니다.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>FTP를 사용 하 여 파일을 다운로드 합니다.

1. 함수 앱에는 [Azure portal](https://portal.azure.com)를 선택 **플랫폼 기능** > **속성**합니다. 그런 다음에 대 한 값을 복사 **P/배포 사용자**를 **FTP 호스트 이름**, 및 **FTPS 호스트 이름**합니다.  

   FTP 서버에 적절한 컨텍스트를 제공하기 위해 앱 이름을 포함하여 포털에서 표시한 대로 **FTP/배포 사용자**를 입력해야 합니다.
   
   ![배포 정보를 가져오려는 선택](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. FTP 클라이언트에서 앱에 연결 하 고 함수에 대 한 소스 파일을 다운로드 수집한 연결 정보를 사용 합니다.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>로컬 Git 리포지토리를 사용 하 여 파일을 다운로드 합니다.

1. 함수 앱에는 [Azure portal](https://portal.azure.com)를 선택 **플랫폼 기능** > **배포 옵션**합니다. 
   
    ![배포 옵션을 열기 위한 선택 항목](./media/functions-continuous-deployment/setup-deployment.png)
 
1. 그런 다음 합니다 **배포** 블레이드에서 **설치**합니다.
 
    ![배포 블레이드](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. 에 **배포 원본** 블레이드에서 **로컬 Git 리포지토리** > **확인**합니다.

1. **플랫폼 기능**를 선택 **속성** Git URL의 값을 확인 합니다. 
   
    ![Git URL을 가져오기 위한 선택 항목](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Git 인식 명령 프롬프트 또는 즐겨 찾는 Git 도구를 사용 하 여 로컬 컴퓨터에 리포지토리를 복제 합니다. Git 복제 명령은 다음과 같이 표시됩니다.
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. 다음 예제와 같이 함수 앱의 파일을 로컬 컴퓨터의 복제로 가져옵니다.
   
        git pull origin master
   
    요청된 경우 [구성된 배포 자격 증명](#credentials)을 제공합니다.  

[GitHub]: https://github.com/

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions에 대한 모범 사례](functions-best-practices.md)
