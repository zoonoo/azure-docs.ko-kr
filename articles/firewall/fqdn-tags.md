---
title: Azure Firewall의 FQDN 태그 개요
description: Azure Firewall의 FQDN 태그에 대해 자세히 알아보기
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 6dc7d20d31d9399355b2b3de90ea90f2f3e07af5
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224650"
---
# <a name="fqdn-tags-overview"></a>FQDN 태그 개요

FQDN 태그는 잘 알려진 Microsoft 서비스와 연결된 FQDN(정규화된 도메인 이름) 그룹을 나타냅니다. 응용 프로그램 규칙에서 FQDN 태그를 사용하여 방화벽을 통해 필요한 아웃바운드 네트워크 트래픽을 허용할 수 있습니다.

예를 들어 방화벽을 통해 Windows 업데이트 네트워크 트래픽을 수동으로 허용하려면 Microsoft 문서에 따라 여러 응용 프로그램 규칙을 만들어야 합니다. FQDN 태그를 사용하여 응용 프로그램 규칙을 만들고 **Windows 업데이트** 태그를 포함할 수 있으며, 이제 Microsoft Windows 업데이트 끝점에 대한 네트워크 트래픽을 방화벽을 통해 전달할 수 있습니다.

고유 FQDN 태그를 만들 수 없으며 태그 내에 포함할 FQDN을 지정할 수도 없습니다. Microsoft는 FQDN 태그에 포함된 FQDN을 관리하고 태그를 FQDN 변경으로 업데이트합니다. 

<!--- screenshot of application rule with a FQDN tag.-->

다음 표는 사용할 수 있는 현재 FQDN 태그를 보여줍니다. Microsoft는 이러한 태그를 유지 관리하며 태그를 주기적으로 추가됩니다.

|FQDN 태그  |설명  |
|---------|---------|
|Windows 업데이트     |[소프트웨어 업데이트에 대한 방화벽 구성 방법](https://technet.microsoft.com/library/bb693717.aspx)에 설명된 대로 Microsoft 업데이트에 대한 아웃바운드 액세스를 허용합니다.|
|Windows 진단|모든 [Windows 진단 끝점](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints)에 대한 아웃바운드 액세스를 허용합니다.|
|MAPS(Microsoft 활성 보호 서비스)|[MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)에 대한 아웃바운드 액세스를 허용합니다.|
|ASE(App Service Environment)|ASE 플랫폼 트래픽에 대한 아웃바운드 액세스를 허용합니다. 이 태그는 ASE에서 만든 고객별 Storage 및 SQL 끝점을 다루지 않습니다. [서비스 끝점](../virtual-network/tutorial-restrict-network-access-to-resources.md)을 통해 사용하도록 설정되거나 수동으로 추가되어야 합니다.|
|Azure Backup|Azure Backup 서비스에 대한 아웃바운드 액세스를 허용합니다.

> [!NOTE]
> 응용 프로그램 규칙에서 FQDN 태그를 선택하는 경우 protocol:port 필드는 **https**로 설정되어야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Firewall을 배포하는 방법을 알아보려면 [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md)을 참조하세요.