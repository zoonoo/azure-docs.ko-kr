---
title: 확장을 사용하여 배포 후 구성
description: ARM 템플릿(Azure Resource Manager 템플릿) 확장을 사용하여 배포 후 구성을 위한 방법을 알아봅니다.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: 3d458673163454a6d7914de958cb1d338ed12687
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026671"
---
# <a name="post-deployment-configurations-by-using-extensions"></a>확장을 사용하여 배포 후 구성

ARM 템플릿(Azure Resource Manager 템플릿) 확장은 Azure 리소스에서 배포 후 구성 및 Automation 작업을 제공하는 작은 애플리케이션입니다. 가장 널리 사용되는 확장이 바로 가상 머신 확장입니다. [Windows용 가상 머신 확장 및 기능](../../virtual-machines/extensions/features-windows.md) 또는 [Linux용 가상 머신 확장 및 기능](../../virtual-machines/extensions/features-linux.md)을 참조하세요.

## <a name="extensions"></a>Extensions

기존 확장은 다음과 같습니다.

- [Microsoft.Compute/virtualMachines/extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql servers/databases/extensions](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)
- [Microsoft.Web/sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

사용 가능한 확장을 알아보려면 [템플릿 참조](/azure/templates/)로 이동합니다. **제목으로 필터링** 에서 **확장** 을 입력합니다.

이러한 확장을 사용하는 방법을 알아보려면 다음을 참조하세요.

- [자습서: ARM 템플릿을 사용하여 가상 머신 확장 배포](template-tutorial-deploy-vm-extensions.md).
- [자습서: ARM 템플릿을 사용하여 SQL BACPAC 파일 가져오기](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: ARM 템플릿을 사용하여 가상 머신 확장 배포](template-tutorial-deploy-vm-extensions.md)
