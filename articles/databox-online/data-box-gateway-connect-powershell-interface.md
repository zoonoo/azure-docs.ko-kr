---
title: Windows PowerShell을 사용하여 Azure 데이터 박스 게이트웨이 장치에 연결하고 관리
description: Windows PowerShell 인터페이스를 통해 데이터 상자 게이트웨이에 연결하고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260216"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure 데이터 박스 게이트웨이 장치 관리

Azure 데이터 상자 게이트웨이 솔루션을 사용하면 네트워크를 통해 Azure로 데이터를 보낼 수 있습니다. 이 문서에서는 데이터 박스 게이트웨이 장치에 대한 구성 및 관리 작업 중 일부에 대해 설명합니다. Azure 포털, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용하여 장치를 관리할 수 있습니다.

이 문서에서는 PowerShell 인터페이스를 사용하여 수행하는 작업에 중점을 둡니다.

이 문서에는 다음 절차가 포함되어 있습니다.

- PowerShell 인터페이스에 연결
- 지원 패키지 만들기
- 인증서 업로드
- DHCP가 아닌 환경에서 부팅
- 장치 정보 보기

## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>인증서 업로드

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>DHCP가 아닌 환경에서 부팅

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>장치 정보 보기

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)를 배포합니다.
