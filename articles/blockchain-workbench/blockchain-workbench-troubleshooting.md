---
title: Azure Blockchain Workbench 문제 해결
description: Azure Blockchain Workbench 응용 프로그램 문제를 해결하는 방법.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8a2715666c4fff490f5184b7b8719b412952b9bf
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Azure Blockchain Workbench 문제 해결

개발자 디버깅 또는 지원을 돕는 데 PowerShell 스크립트를 사용할 수 있습니다. 스크립트는 요약을 생성하고 문제 해결을 위한 자세한 로그를 수집합니다. 수집된 로그에는 다음이 포함됩니다.

* Ethereum과 같은 블록체인 네트워크
* Blockchain Workbench 마이크로 서비스
* Application Insights
* Azure 모니터링(OMS)

다음 단계를 확인하고 문제의 근본 원인을 확인하는 데 정보를 사용할 수 있습니다. 

## <a name="troubleshooting-script"></a>문제 해결 스크립트

GitHub에서 PowerShell 문제 해결 스크립트를 사용할 수 있습니다. GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/blockchain/archive/master.zip)하거나 샘플을 복제합니다.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>스크립트 실행
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

`collectBlockchainWorkbenchTroubleshooting.ps1` 스크립트를 실행하여 로그를 수집하고 문제 해결 정보의 폴더가 포함된 ZIP 파일을 만듭니다. 예: 

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
스크립트는 다음 매개 변수를 수락합니다.

| 매개 변수  | 설명 | 필수 |
|---------|---------|----|
| 구독 ID | 모든 리소스를 만들거나 찾는 SubscriptionID입니다. | 예 |
| ResourceGroupName | Blockchain Workbench가 배포된 Azure 리소스 그룹의 이름입니다. | 예 |
| OutputDirectory | 출력 ZIP 파일을 생성하는 경로입니다. 지정하지 않으면 기본값은 현재 디렉터리입니다. | 아니오
| OmsSubscriptionId | OMS가 배포되는 구독 ID입니다. 블록체인 네트워크에 대한 OMS가 Blockchain Workbench의 리소스 그룹 외부로 배포되는 경우에만 이 매개 변수를 전달합니다.| 아니오 |
| OmsResourceGroup |OMS가 배포되는 리소스 그룹입니다. 블록체인 네트워크에 대한 OMS가 Blockchain Workbench의 리소스 그룹 외부로 배포되는 경우에만 이 매개 변수를 전달합니다.| 아니오 |
| OmsWorkspaceName | OMS 작업 영역 이름입니다. 블록체인 네트워크에 대한 OMS가 Blockchain Workbench의 리소스 그룹 외부로 배포되는 경우에만 이 매개 변수를 전달합니다. | 아니오 |

## <a name="what-is-collected"></a>수집되는 항목은?

출력 ZIP 파일에는 다음 폴더 구조가 포함됩니다.

| 폴더 \ 파일 | 설명  |
|---------|---------|
| \Summary.txt | 시스템의 요약 |
| \metrics\blockchain | 블록체인에 대한 메트릭 |
| \metrics\workbench | 워크벤치에 대한 메트릭 |
| \details\blockchain | 블록체인에 대한 자세한 로그 |
| \details\workbench | 블록체인에 대한 자세한 로그 |

요약 파일에는 응용 프로그램의 전반적인 상태와 응용 프로그램의 상태에 대한 스냅숏을 제공합니다. 요약에서는 실행하는 서비스에 대한 권장되는 작업, 강조 표시된 최상위 오류 및 메타데이터를 제공합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 아키텍처](blockchain-workbench-architecture.md)