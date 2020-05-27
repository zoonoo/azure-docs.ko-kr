---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649039"
---
Azure VM, 스토리지, 네트워크 연결, 웹 프레임워크, 관리 및 통합 기능을 포함한 App Service의 플랫폼 구성 요소는 적극적으로 보호되고 강화됩니다. App Service는 다음 사항을 확인하기 위해 지속적으로 활발한 준수 확인을 수행합니다.

- 사용자의 앱 리소스는 다른 고객의 Azure 리소스로부터 [보호](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)됩니다.
- 새로 검색된 취약성을 처리하기 위해 [VM 인스턴스 및 런타임 소프트웨어가 정기적으로 업데이트됩니다](../articles/app-service/overview-patch-os-runtime.md). 
- 앱과 다른 Azure 리소스(예: [SQL Database](https://azure.microsoft.com/services/sql-database/)) 간의 비밀(예: 연결 문자열) 통신이 Azure 내에서 유지되며 네트워크 경계를 벗어나지 않습니다. 비밀은 저장될 때 항상 암호화됩니다.
- [하이브리드 연결](../articles/app-service/app-service-hybrid-connections.md)과 같은 App Service 연결 기능을 통한 모든 통신이 암호화됩니다. 
- Azure PowerShell, Azure CLI, Azure SDK, REST API와 같은 원격 관리 도구와의 연결이 모두 암호화됩니다.
- 24시간 위협 관리를 통해 맬웨어, DDoS(배포된 서비스 거부), MITM(메시지 가로채기) 및 기타 위협으로부터 인프라와 플랫폼을 보호합니다.

Azure의 인프라 및 플랫폼 보안에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/overview/trusted-cloud/)를 참조하세요.