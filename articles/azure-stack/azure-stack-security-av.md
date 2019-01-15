---
title: Azure Stack에 Windows Defender 바이러스 백신 업데이트
description: Azure Stack에서 바이러스 백신 하는 방법에 대 한 세부 정보를 최신 상태로 유지 됩니다.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: d47e11be88c4a8ca453475c35e9e0f02d9b531ff
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305131"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Azure Stack에 Windows Defender 바이러스 백신 업데이트

[Windows Defender 바이러스 백신](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) 는 보안 및 바이러스 보호를 제공 하는 맬웨어 방지 솔루션입니다. 모든 Azure Stack 인프라 구성 요소 (Hyper-v 호스트 및 virtual machines) Windows Defender 바이러스 백신을 사용 하 여 보호 됩니다. 최신 보호를 위해 Windows Defender 바이러스 백신 정의 엔진 및 플랫폼에 주기적으로 업데이트 해야 합니다. 업데이트가 적용 되는 방식을 구성에 따라 달라 집니다.

## <a name="connected-scenario"></a>연결 된 시나리오

맬웨어 방지 정 및 엔진 업데이트를 Azure Stack [리소스 공급자 업데이트](azure-stack-updates.md#the-update-resource-provider) 맬웨어 방지 정 및 엔진 업데이트 하루에 여러 번 다운로드 합니다. 각 Azure Stack 인프라 구성 요소 업데이트를 가져오고 업데이트 리소스 공급자에서 업데이트를 자동으로 적용 합니다.

맬웨어 방지 플랫폼 업데이트를 적용 합니다 [월별 Azure Stack 업데이트](azure-stack-apply-updates.md)합니다. 월별 Azure Stack 업데이트에는 해당 월에 대 한 Windows Defender 바이러스 백신 플랫폼 업데이트가 포함 됩니다.

## <a name="disconnected-scenario"></a>연결이 끊긴된 시나리오

 적용 된 [월별 Azure Stack 업데이트](azure-stack-apply-updates.md)합니다. 월별 Azure Stack 업데이트는 Windows Defender 바이러스 백신 정의 엔진 및 월에 대 한 플랫폼 업데이트를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack 보안에 자세히 알아보기](azure-stack-security-foundations.md)
