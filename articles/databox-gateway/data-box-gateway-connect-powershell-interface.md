---
title: Windows PowerShell을 사용하여 Azure Data Box Gateway 디바이스에 연결 및 관리
description: Windows PowerShell 인터페이스를 통해 Data Box Gateway에 연결하고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: f3b93bfc9af9bce50c301c10bd372567360d7223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582167"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure Data Box Gateway 디바이스 관리

Azure Data Box Gateway 솔루션을 사용하면 네트워크를 통해 Azure로 데이터를 보낼 수 있습니다. 이 문서에서는 Data Box Gateway 디바이스에 대한 구성 및 관리 작업을 설명합니다. Azure Portal, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용하여 디바이스를 관리할 수 있습니다.

이 문서에서는 PowerShell 인터페이스를 사용하여 수행하는 작업을 중점적으로 설명합니다.

이 문서에는 다음 프로시저가 포함되어 있습니다.

- PowerShell 인터페이스에 연결합니다.
- 지원 패키지 만들기
- 인증서 업로드
- 비 DHCP 환경에서 부팅
- 디바이스 정보 보기

## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결합니다.

[!INCLUDE [Connect to admin runspace](../../includes/data-box-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>인증서 업로드

[!INCLUDE [Upload certificate](../../includes/data-box-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>비 DHCP 환경에서 부팅

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>디바이스 정보 보기

[!INCLUDE [View device information](../../includes/data-box-gateway-view-device-info.md)]

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)를 배포합니다.
