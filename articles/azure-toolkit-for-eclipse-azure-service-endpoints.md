---
title: "Azure 서비스 끝점"
description: "Eclipse용 Azure 도구 키트에서 Azure 서비스 끝점 설정을 설명합니다."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9c6125ec-7278-461e-b69c-ed56e844f742
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 9f664b07566fe78d93b1e35a2e0f7e5513960fad
ms.lasthandoff: 01/05/2017


---
# <a name="azure-service-endpoints"></a>Azure 서비스 끝점
Azure 서비스 끝점은 응용 프로그램이 글로벌 Azure 플랫폼에서 배포되고 관리되는지, 중국의 21Vianet에서 운영하는 Azure인지, 개인 Azure 플랫폼인지 확인합니다. **서비스 끝점** 대화 상자를 통해 사용하려는 서비스 끝점을 지정할 수 있습니다. **서비스 끝점** 대화 상자를 열려면 Eclipse 내에서 **창**, **기본 설정**을 차례로 클릭하고 **Azure**를 확장한 다음 **서비스 끝점**을 클릭합니다. **Active Set** (활성 설정) 필드를 설정하여 Azure 서비스 끝점을 현재 작업 영역에서 Azure 프로젝트에 사용할지 결정합니다.

다음은 **서비스 끝점** 대화 상자입니다.

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>서비스 끝점을 설정하려면
**서비스 끝점** 대화 상자에서 다음 작업 중 하나를 수행합니다.

* 글로벌 Azure 플랫폼을 사용하려는 경우 **활성 설정** 드롭다운 목록에서 **windowsazure.com**을 선택하고 **확인**을 클릭합니다.

* 중국의 21Vianet에서 운영하는 Azure를 사용하려는 경우 **Active Set**(활성 설정) 드롭다운 목록에서 **windowsazure.cn(중국)**을 선택하고 **확인**을 클릭합니다.

* 개인 Azure 플랫폼을 사용하려면:

  1. **편집**을 클릭합니다.

  2. 대화 상자가 열리면서 **서비스 끝점** 대화 상자가 닫히고 기본 설정 집합 파일이 열린다고 알려줍니다. **확인**을 클릭합니다.

  3. Preferencesets.xml 파일에 새 `preferenceset` 요소를 만듭니다. 이 새 요소에 대해 `name`, `blob`, `management`, `portalURL` 및 `publishsettings` 특성을 만들고 개인 Azure 플랫폼에 해당하는 특성 값을 추가합니다. 기존 `preferenceset` 요소에 대해 제공되는 값을 템플릿으로 사용할 수 있습니다. **참고**: `blob` 특성에 사용되는 값에는 URL에 "blob" 텍스트가 포함되어야 합니다.

  4. preferencesets.xml을 저장하고 닫습니다.

  5. **서비스 끝점** 대화 상자를 다시 엽니다.

  6. **활성 설정** 드롭다운 목록에서 만들어진 활성 설정을 선택하고 **확인**을 클릭합니다.

  7. 개인 Azure 플랫폼 `preferenceset` 요소를 만들면 **서비스 끝점** 대화 상자의 **편집** 단추를 클릭하여 요소에 할당된 값을 변경할 수 있습니다. 또한 원하는 경우 여러 개인 Azure 플랫폼 `preferenceset` 요소를 만들 수 있습니다.

## <a name="see-also"></a>참고 항목
[Eclipse용 Azure 도구 키트][Azure Toolkit for Eclipse]

[Eclipse용 Azure 도구 키트 설치][Installing the Azure Toolkit for Eclipse] 

[Eclipse에서 Azure용 Hello World 응용 프로그램 만들기][Creating a Hello World Application for Azure in Eclipse]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터][Azure Java Developer Center]를 참조하세요.

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

