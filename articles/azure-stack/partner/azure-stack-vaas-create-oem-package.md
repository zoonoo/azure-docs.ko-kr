---
title: Azure Stack 유효성 검사 모범 사례 | Microsoft Docs
description: 이 문서에서는 유효성 검사를 사용 하 여 서비스에 대 한 모범 사례를 설명 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 568817c6e25952f15a396e5748d314187345945b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769049"
---
# <a name="create-an-oem-package"></a>OEM 패키지 만들기

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Azure Stack OEM 확장 패키지에는 OEM으로 특정 콘텐츠 업데이트, 확장 및 대체 필드와 같은 운영 프로세스를 비롯 하 여 배포에 사용 하기 위해 Azure Stack 인프라에 추가 됩니다 메커니즘입니다.

## <a name="creating-the-package"></a>패키지 만들기

를 만들고 유효성을 검사 후 VaaS에서 OEM 확장 패키지를 사용할 수 있습니다.  계속 하기 전에 단계를 완료 한 확인 [OEM 패키지 만들기](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true)합니다. 패키지가 다음 패키지 유효성 검사 워크플로 로그인에 대 한 VaaS 테스트 결과 함께 Microsoft에 제출 됩니다. 다음 단계를 자세히 VaaS 사용할 수 있는 단일 zip 파일에 생성된 된 파일을 번들로 묶는 방법을 설명 합니다.

1. 패키지에 대 한 다음 콘텐츠를 식별 합니다.
    - 패키지 콘텐츠를 포함 한 zip 파일
    - 매니페스트 파일인 `oemMetadata.xml`, 패키지 콘텐츠의 루트에 metadata.xml 파일 콘텐츠에서 동일 해야 합니다.

2. 콘텐츠 파일을 선택 하 고 내용에서 zip 파일을 만듭니다.

    ![Zip 파일 내용을](media/vaas-create-oem-package-1.png) ![항목 내용을 압축](media/vaas-create-oem-package-2.png)

3. 알기 쉽게 식별할 수 있기 때문에 결과 파일을 바꿉니다.

## <a name="verifying-the-contents"></a>콘텐츠를 확인합니다.

Zip 파일의 구조 유효성 검사를 검사 하 고 하위 폴더가 없으므로 있는지 확인 합니다. TLD 내용이 압축 합니다. 유효한 패키지 구조는 다음과 같습니다.
> [!IMPORTANT]
> 내용 대신 부모 폴더를 압축 하면 로그인 실패 하는 패키지 합니다.

![제대로 압축 된 패키지 콘텐츠](media/vaas-create-oem-package-3.png)

이제 zip 파일을 VaaS 업로드 고 패키지 유효성 검사 워크플로를 Microsoft에서 서명 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [OEM 패키지의 유효성 검사](azure-stack-vaas-validate-oem-package.md)
