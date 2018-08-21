---
title: 서비스로 Azure Stack 유효성 검사에서 Microsoft의 소프트웨어 업데이트의 유효성을 검사 | Microsoft Docs
description: 서비스 유효성 검사를 사용 하 여 Microsoft의 소프트웨어 업데이트의 유효성을 검사 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a9e1bb2f134e0e51803266f5e7f94c05ab6966f2
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235407"
---
# <a name="validate-software-updates-from-microsoft"></a>Microsoft의 소프트웨어 업데이트의 유효성을 검사합니다

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft은 Azure Stack 소프트웨어 업데이트를 주기적으로 해제 됩니다. 이러한 업데이트는 해당 솔루션에 대 한 업데이트의 유효성을 검사 하 고 Microsoft에 피드백을 제공할 수 있도록 공개적으로 제공 되 전에 Azure Stack 공동 엔지니어링 파트너에 게 제공 됩니다.

## <a name="test-an-existing-solution"></a>기존 솔루션 테스트

1. 에 로그인 합니다 [유효성 검사 포털](https://azurestackvalidation.com)합니다.

2. Microsoft에서 업데이트 된 배포한 선택한 기존 솔루션 선택 **시작** 에 **패키지 유효성 검사** 타일입니다.

    ![패키지 유효성 검사](media/image3.png)

3. 유효성 검사 코드를 입력 합니다.

4. 솔루션 배포 시에 설치 된 OEM 패키지에 URL을 입력 합니다. Azure blob service에 저장 된 패키지에 대 한 URL을 사용 합니다. 자세한 내용은 [로그를 저장할 Azure 저장소 blob 만들기](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)합니다.

5. 선택 **업로드** 배포 구성 파일을 추가 합니다. 참조 된 [새 Azure Stack 솔루션의 유효성을 검사](azure-stack-vaas-validate-solution-new.md) 배포 구성 파일을 업로드 하는 방법은 합니다.

6. 배포 구성 파일을 다음으로 해야 올바른 환경 매개 변수 파일을 사용 하 여 사용자 지정을 참조 하세요 [환경 매개 변수](azure-stack-vaas-parameters.md#environment-parameters) 추가 세부 정보에 대 한 합니다.

    > [!Note]   
    > 배포 구성 파일을 일반 테스트 매개 변수를 추가 하 여 추가로 사용자 지정할 수 있습니다. 자세한 내용은 참조 하세요. [서비스로 Azure Stack 유효성 검사에 대 한 워크플로 일반 매개 변수](azure-stack-vaas-parameters.md)

7. 사용자 이름 및 테 넌 트 사용자, 서비스 관리자 및 클라우드 관리자에 대 한 암호를 수동으로 입력 해야 합니다.

8. 진단 로그를 저장할 Azure Storage blob URL을 제공 합니다. 자세한 내용은 [로그를 저장할 Azure 저장소 blob 만들기](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)합니다.

    > [!Note]  
    > 워크플로 레이블을 설명 태그를 입력할 수 있습니다.

10. 선택 **제출** 는 워크플로를 저장 합니다.

솔루션 워크플로 약 24 시간 동안 실행 됩니다. 테스트를 예약에 대 한 링크 또는 명령을 추가 합니다. 지우기 도구에서.

유효성 검사의 진행률을 모니터링 하는 방법은 실행을 참조 하십시오 [테스트를 모니터링 ](azure-stack-vaas-monitor-test.md)합니다.

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.
