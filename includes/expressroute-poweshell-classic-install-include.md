---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926241"
---
최신 버전의 Azure SM(서비스 관리) PowerShell 모듈 및 ExpressRoute 모듈을 설치합니다. Azure CloudShell 환경을 사용하여 SM 모듈을 실행할 수 없습니다.

1. [서비스 관리 모듈 설치의](/powershell/azure/servicemanagement/install-azure-ps) 지침을 사용하여 Azure 서비스 관리 모듈을 설치합니다. Az 또는 RM 모듈이 이미 설치되어 있는 경우 '-AllowClobber'를 사용해야 합니다.
2. 설치된 모듈을 가져옵니다. 다음 예제를 사용하는 경우 설치된 PowerShell 모듈의 위치와 버전을 반영하도록 경로를 조정합니다.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Azure 계정에 로그인하려면 PowerShell 콘솔을 높은 권한으로 열고 계정에 연결합니다. 다음 예제를 사용하여 서비스 관리 모듈을 사용하여 연결할 수 있습니다.

   ```powershell
   Add-AzureAccount
   ```