---
title: 연결 하 고 Windows PowerShell 인터페이스를 통해 Microsoft Azure 데이터 상자 게이트웨이 장치를 관리 합니다. | Microsoft Docs
description: 에 연결한 다음 Windows PowerShell 인터페이스를 통해 데이터 상자 게이트웨이 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 0ca570235ac2a87b62c5d0fcebbd24dc5186e37d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556515"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure 데이터 상자 게이트웨이 장치 관리

데이터 상자 게이트웨이 솔루션을 azure를 사용 하면 Azure로 네트워크를 통해 데이터를 보낼 수 있습니다. 이 문서에서는 데이터 상자 게이트웨이 장치에 대 한 구성 및 관리 작업 중 일부를 설명 합니다. 장치를 관리 하려면 Azure portal, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용할 수 있습니다.

이 문서에서는 PowerShell 인터페이스를 사용 하 여 수행 하는 작업에 중점을 둡니다.

이 문서는 다음 절차에 포함 됩니다.

- PowerShell 인터페이스에 연결
- 지원 세션 시작
- 지원 패키지 만들기
- 인증서 업로드
- DHCP 환경이 아닌에서 부팅
- 장치 정보 보기

> [!IMPORTANT]
> Azure 데이터 상자 게이트웨이 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>지원 세션 시작

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>인증서 업로드

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>DHCP 환경이 아닌에서 부팅

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>장치 정보 보기

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)를 배포합니다.
