---
title: 서비스로 Azure Stack 유효성 검사에 원래 장비 제조업체 (OEM) 패키지 유효성 검사 | Microsoft Docs
description: 서비스 유효성 검사를 사용 하 여 원래 장비 제조업체 (OEM) 패키지의 유효성을 검사 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b3a9ee66907b51a40e9f4b0871d9f6ba6e29763a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242402"
---
# <a name="validate-oem-packages"></a>OEM 패키지 유효성 검사

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

펌웨어 또는 드라이버 유효성 검사를 완료 된 솔루션에 대 한 변경 된 경우 새 OEM 패키지를 테스트할 수 있습니다. 패키지를 테스트에 전달 되는 경우에 Microsoft에서 서명 됩니다. 테스트는 드라이버와 Windows Server 로고 및 PC 테스트를 통과 했거나 펌웨어를 사용 하 여 업데이트 된 OEM 확장 패키지를 포함 해야 합니다.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> 검토를 업로드 하거나 패키지를 제출 하기 전에 [OEM 패키지를 만드는](azure-stack-vaas-create-oem-package.md) 예상된 패키지 형식 및 내용에 대 한 합니다.

## <a name="managing-packages-for-validation"></a>유효성 검사에 대 한 패키지 관리

사용 하는 경우는 **패키지 유효성 검사** 패키지의 유효성을 검사 하는 워크플로 대 한 URL을 제공 해야 합니다는 **Azure Storage blob**합니다. 이 blob에는 솔루션 배포 시에 설치 된 OEM 패키지입니다. 설치 중에 만든 Azure Storage 계정을 사용 하 여 blob을 만듭니다 (참조 [유효성 검사 서비스 리소스로 설정](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>필수 조건: 저장소 컨테이너를 프로 비전

패키지 blob에 대 한 저장소 계정의 컨테이너를 만듭니다. 모든 패키지 유효성 검사 실행에 대 한이 컨테이너를 사용할 수 있습니다.

1. 에 [Azure Portal](https://portal.azure.com)에서 만든 저장소 계정으로 이동 [유효성 검사 서비스 리소스로 설정](azure-stack-vaas-set-up-resources.md)합니다.
2. 아래 왼쪽 블레이드에서 **Blob Service**, 선택 **컨테이너**합니다.
3. 선택 **+ 컨테이너** 메뉴에서 가로 막대형 차트 및 예를 들어 컨테이너의 이름을 제공 `vaaspackages`합니다.

### <a name="upload-package-to-storage-account"></a>저장소 계정에 패키지 업로드

1. 유효성을 검사 하려는 패키지를 준비 합니다. 패키지에 여러 파일이 있는 경우 압축에 `.zip` 파일입니다.
2. 에 [Azure Portal](https://portal.azure.com), 패키지 컨테이너를 선택 하 고에서 선택 하 여 패키지를 업로드 **업로드** 메뉴 모음에서.
3. 패키지 선택 `.zip` 파일을 업로드 합니다. 에 대 한 기본값을 유지 **Blob 유형** (즉, **블록 Blob**) 및 **블록 크기**합니다.

> [!NOTE]
> 있는지 확인 하십시오는 `.zip` 콘텐츠 루트에 배치 됩니다는 `.zip` 파일입니다. 패키지에는 하위 폴더가 있어야 합니다.

### <a name="generate-package-blob-url-for-vaas"></a>VaaS 패키지 blob URL 생성

만들 때를 **패키지 유효성 검사** VaaS 포털에서 워크플로 해야 패키지를 포함 하는 Azure Storage blob에 대 한 URL을 제공 합니다.

#### <a name="option-1-generating-an-account-sas-url"></a>옵션 1: 계정 SAS URL을 생성합니다.

1. 에 [Azure portal](https://portal.azure.com/)에 저장소 계정으로 이동 하 고 패키지에 포함 된.zip으로 이동

2. 선택 **SAS 생성** 상황에 맞는 메뉴에서

3. 선택 **읽기** 에서 **권한**

4. 설정할 **시작 시간** 은 현재 시간 및 **종료 시간** 48 시간 이상 **시작 시간**합니다. 동일한 패키지를 사용 하 여 다른 테스트 실행은 늘리는 것이 좋습니다 **종료 시간** 테스트 길이 대 한 합니다. 후 VaaS 통해 예약 된 테스트가 **종료 시간** 는 실패 하 고 새 SAS를를 생성 해야 합니다.

5. 선택 **URL 및 blob SAS 토큰을 생성 합니다.**

사용 하 여 **Blob SAS URL** 새를 시작할 때 **패키지 유효성 검사** VaaS 포털에서 워크플로.

#### <a name="option-2-using-public-read-container"></a>옵션 2: 공용 읽기 컨테이너를 사용 하 여

> [!CAUTION]
> 이 옵션은 익명 읽기 전용 액세스에 대 한 컨테이너를 엽니다.

1. 권한 부여 **공개 읽기 액세스 blob에 대해서만** 섹션의 지침에 따라 패키지 컨테이너에 [컨테이너 및 blob에 익명의 사용자 권한 부여](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)합니다.

2. 패키지 컨테이너에서 속성 창을 열려면 패키지 blob 컨테이너에서 선택 합니다.

3. 복사 합니다 **URL**합니다. 새를 시작할 때이 값을 사용할 **패키지 유효성 검사** VaaS 포털에서 워크플로.

## <a name="apply-monthly-update"></a>월별 업데이트를 적용 합니다.

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>패키지 유효성 검사 워크플로 만들기

1. 에 로그인 합니다 [VaaS 포털](https://azurestackvalidation.com)합니다.

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 선택 **시작** 에 **패키지 유효성 검사** 바둑판식으로 배열 합니다.

    ![패키지 유효성 검사 워크플로 타일](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. 솔루션 배포 시에 설치 된 OEM 패키지에는 Azure Storage blob URL을 입력 합니다. 자세한 내용은 [VaaS 패키지 blob URL 생성](#generate-package-blob-url-for-vaas)합니다.

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 워크플로 만든 후 환경 매개 변수를 수정할 수 없습니다.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    테스트 요약 페이지로 리디렉션됩니다.

## <a name="run-package-validation-tests"></a>패키지 유효성 검사 테스트를 실행 합니다.

1. 에 **패키지 유효성 검사 테스트 요약** 페이지 유효성 검사를 완료 하는 데 필요한 테스트 목록이 표시 됩니다. 이 워크플로에서 테스트는 약 24 시간 동안 실행합니다.

    유효성 검사 워크플로에서 **예약** 워크플로 만드는 동안 지정 된 워크플로 수준 일반적인 매개 변수를 사용 하는 테스트 (참조 [서비스로AzureStack유효성검사에대한워크플로일반매개변수](azure-stack-vaas-parameters.md)). 테스트 매개 변수 값에 유효 하지 않게 됩니다, 경우 있습니다 해야 재충전할 하에 설명 된 대로 [워크플로 매개 변수를 수정](azure-stack-vaas-monitor-test.md#change-workflow-parameters)합니다.

    > [!NOTE]
    > 기존 인스턴스에 대해 유효성 검사 테스트를 예약 이전 인스턴스 대신 새 인스턴스를 포털에서 만들어집니다. 이전 인스턴스에 대 한 로그는 보존할 되지만 포털에서 액세스할 수 없습니다.  
    테스트를 성공적으로 완료 되 면 합니다 **일정** 작업 비활성화 합니다.

2. 테스트를 실행 하는 에이전트를 선택 합니다. 자세한 내용은 추가 하는 방법에 대 한 로컬 테스트 실행 에이전트 참조 하십시오 [로컬 에이전트를 배포](azure-stack-vaas-local-agent.md)합니다.

3. 4 및 5 다음 테스트를 각각에 대 한 단계 수행:
    - OEM 확장 패키지 확인
    - 클라우드 시뮬레이션 엔진

4. 선택 **일정** 테스트 인스턴스 예약에 대 한 프롬프트를 열려면 상황에 맞는 메뉴에서입니다.

5. 테스트 매개 변수를 검토 하 고 선택한 **제출** 실행에 대 한 테스트를 예약 합니다.

모든 테스트가 성공적으로 완료 되 면 VaaS 솔루션 및 패키지 유효성 검사 프로그램의 이름을 보내지 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) 패키지 서명 요청을 합니다.

## <a name="next-steps"></a>다음 단계

- [모니터링 및 VaaS 포털에서 테스트를 관리 합니다.](azure-stack-vaas-monitor-test.md)