---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74926241"
---
최신 버전의 Azure SM(서비스 관리) PowerShell 모듈 및 ExpressRoute 모듈을 설치합니다. SM 모듈을 실행 하는 데 Azure CloudShell 환경을 사용할 수 없습니다.

1. [서비스 관리 모듈](/powershell/azure/servicemanagement/install-azure-ps) 설치 문서의 지침을 사용 하 여 Azure 서비스 관리 모듈을 설치 합니다. Az 또는 RM 모듈이 이미 설치 되어 있는 경우 '-AllowClobber '를 사용 해야 합니다.
2. 설치 된 모듈을 가져옵니다. 다음 예제를 사용 하는 경우 설치 된 PowerShell 모듈의 위치와 버전이 반영 되도록 경로를 조정 합니다.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Azure 계정에 로그인 하려면 관리자 권한으로 PowerShell 콘솔을 열고 계정에 연결 합니다. 서비스 관리 모듈을 사용 하 여 연결 하려면 다음 예제를 사용 합니다.

   ```powershell
   Add-AzureAccount
   ```