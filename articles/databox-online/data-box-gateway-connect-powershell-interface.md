---
title: 연결 하 고 Windows PowerShell 인터페이스를 통해 Microsoft Azure 데이터 상자 게이트웨이 장치를 관리 합니다. | Microsoft Docs
description: 에 연결한 다음 Windows PowerShell 인터페이스를 통해 데이터 상자 게이트웨이 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64415157"
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

## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

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
