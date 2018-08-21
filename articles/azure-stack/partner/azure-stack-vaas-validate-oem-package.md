---
title: 서비스로 Azure Stack 유효성 검사에 원래 장비 제조업체 (OEM) 패키지 유효성 검사 | Microsoft Docs
description: 서비스 유효성 검사를 사용 하 여 원래 장비 제조업체 (OEM) 패키지를 확인 하는 방법에 알아봅니다.
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
ms.openlocfilehash: a08f439780e0080d8da2cde1531e1580dbdad14f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235371"
---
# <a name="validate-oem-packages"></a>OEM 패키지 유효성 검사

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

펌웨어 또는 드라이버 유효성 검사를 완료 된 솔루션에 대 한 변경 된 경우 새 OEM 패키지를 테스트할 수 있습니다. 패키지를 테스트에 전달 되는 경우에 Microsoft에서 서명 됩니다. 테스트는 드라이버와 Windows Server 로고 및 PC 테스트를 통과 했거나 펌웨어를 사용 하 여 업데이트 된 OEM 확장 패키지를 포함 해야 합니다.

모든 테스트의 결과 사용 하 여 24 시간 내에 완료 **성공**합니다. 있으면 테스트 결과 **못했습니다**에서 버그를 [Microsoft 공동 작업](https://aka.ms/collaborate) 전자 메일을 전송 하 여 Microsoft에 통보 하 고 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)합니다.

## <a name="get-your-oem-package-signed"></a>OEM 패키지의 서명

1. 최신 월별 업데이트에 적용 되었는지 확인 합니다. 최신 버전에서 최신 버전을 참조 하세요 [Azure Stack 연산자 설명서 > 개요 > 릴리스 정보](https://docs.microsoft.com/azure/azure-stack/) 합니다.

    Azure Stack에 Microsoft 소프트웨어 업데이트는 명명 규칙을 사용 하 여 지정 된, 2018 년 3 월에 대 한 예를 들어은 1803 나타내는 업데이트 합니다. 주기 및 릴리스 정보는 사용할 수 있는 Azure Stack update 정책에 대 한 자세한 내용은 [Azure Stack 서비스 정책](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy)합니다.

1. 실행 하 여 시스템 상태를 확인할 **테스트 AzureStack** 으로 설명 [Azure Stack에 대 한 유효성 검사 테스트를 실행 합니다. 모든 테스트를 시작 하기 전에 모든 경고 및 오류를 수정 합니다.

2. 에 [유효성 검사 포털](https://azurestackvalidation.com)를 기존 솔루션을 선택 합니다. 솔루션을 추가 하지 않은 경우 [새 솔루션을 추가](azure-stack-vaas-validate-solution-new.md#add-a-new-solution)합니다.

3. 선택 **시작** 에 **패키지 유효성 검사** 타일을 새 워크플로 시작 합니다.

    ![패키지 유효성 검사](media/image3.png)

4.  진단 연결 문자열을 제공 합니다. 자세한 내용은 [저장소 계정을 설정](azure-stack-vaas-set-up-account.md)합니다.

    OEM 확장 패키지를 실행 하는 모든 패키지 유효성 검사에 대 한 지정 되어야 합니다. Azure Stack 배포 시 솔루션에 설치 된 OEM 패키지를 지정 합니다. 자세한 내용은 [로그를 저장할 Azure 저장소 blob 만들기](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)합니다.

    데이터 항목에는 실수를 방지 하는 실행에 대 한 필수 필드에 대 한 입력을 완료 하려면 환경 변수를 사용 하 여 JSON 파일을 사용 되어야 합니다. 자세한 내용은 [는 Azure Stack 배포의 구성 파일을 가져오는](azure-stack-vaas-parameters.md)합니다.

5. 테스트를 실행 합니다.

6. 모든 테스트를 성공적으로 완료 하 여 솔루션 및 패키지 유효성 검사의 이름을 보내지 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) 패키지 서명 요청을 합니다.

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.
