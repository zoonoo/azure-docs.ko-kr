---
title: "IntelliJ용 Azure 탐색기를 사용하여 저장소 계정 관리 | Microsoft 문서"
description: "IntelliJ용 Azure 탐색기를 사용하여 Azure Storage 계정을 관리하는 방법을 알아봅니다."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 22930bf1d37d7b6039bd02792cdd70ec321d6fa7
ms.lasthandoff: 04/22/2017


---

# <a name="managing-storage-accounts-using-the-azure-explorer-for-intellij"></a>IntelliJ용 Azure 탐색기를 사용하여 저장소 계정 관리

IntelliJ용 Azure 도구 키트의 일부인 Azure 탐색기는 IntelliJ IDE 내에서 Azure 계정의 저장소 계정을 관리하기 위한 사용하기 쉬운 솔루션을 Java 개발자에게 제공합니다.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="creating-a-storage-account-in-intellij"></a>IntelliJ에서 저장소 계정 만들기

다음 단계에서는 Azure 탐색기를 사용하여 저장소 계정을 만드는 단계를 안내합니다.

1. [Eclipse용 Azure 도구 키트에 대한 로그인 지침] 문서의 단계를 사용하여 Azure 계정에 로그인합니다.

1. **Azure 탐색기** 도구 창에서 **Azure** 노드를 확장하고 **저장소 계정**을 마우스 오른쪽 단추로 클릭한 후 **저장소 계정 만들기**를 클릭합니다.
   ![저장소 계정 만들기 메뉴][CS01]

1. **저장소 계정 만들기** 대화 상자가 나타나면 다음 옵션을 지정합니다. ![새 저장소 계정 만들기 대화 상자][CS02]

   a. **이름**: 새 저장소 계정의 이름을 지정합니다.

   b. **계정 종류**: 만들려는 저장소 계정의 유형(예: "Blob Storage")을 지정합니다. 자세한 내용은 [Azure Storage 계정 정보]를 참조하세요.

   c. **성능**: 선택한 게시자에서 사용할 제품을 제공하는 저장소 계정(예: "Premium")을 지정합니다. 자세한 내용은 [Azure Storage 확장성 및 성능 목표]를 참조하세요.

   ㄹ. **복제**: 저장소 계정에 대한 복제(예: "영역 중복")를 지정합니다. 자세한 내용은 [Azure Storage 복제]를 참조하세요.

   e. **구독**: 새 저장소 계정에 사용할 Azure 구독을 지정합니다.

   f. **위치**: 저장소 계정을 만들 위치(예: "미국 서부")를 지정합니다.

   g. **리소스 그룹**: 가상 컴퓨터에 대한 리소스 그룹을 지정합니다. 다음 옵션 중 하나를 선택해야 합니다.
      * **새로 만들기**: 새 리소스 그룹을 만들도록 지정합니다.
      * **기존 그룹 사용**: Azure 계정에 연결된 목록 리소스 그룹에서 선택하도록 지정합니다.

1. 위의 옵션을 모두 지정한 경우 **확인**을 클릭합니다.

## <a name="creating-a-storage-container-in-intellij"></a>IntelliJ에서 저장소 컨테이너 만들기

다음 단계에서는 Azure 탐색기를 사용하여 저장소 컨테이너를 만드는 단계를 안내합니다.

1. Azure 탐색기에서 컨테이너를 만들 저장소 계정을 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기**를 클릭합니다.
   ![저장소 컨테이너 만들기 메뉴][CC01]

1. **Blob 컨테이너 만들기** 대화 상자가 나타나면 컨테이너의 이름을 지정하고 **확인**을 클릭합니다. 저장소 컨테이너 명명에 대한 자세한 내용은 [컨테이너, Blob 및 메타데이터 명명 및 참조]를 참조하세요. ![저장소 컨테이너 만들기 대화 상자][CC02]

## <a name="deleting-a-storage-container-in-intellij"></a>IntelliJ에서 저장소 컨테이너 삭제

Azure 탐색기를 사용하여 저장소 컨테이너를 삭제하려면 다음 단계를 사용합니다.

1. Azure 탐색기에서 저장소 컨테이너를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.
   ![저장소 컨테이너 삭제 메뉴][DC01]

1. 저장소 컨테이너를 삭제할지 묻는 메시지가 표시되면 **예**를 클릭합니다.
   ![저장소 컨테이너 삭제 대화 상자][DC02]

## <a name="deleting-a-storage-account-in-intellij"></a>IntelliJ에서 저장소 계정 삭제

Azure 탐색기를 사용하여 저장소 계정을 삭제하려면 다음 단계를 사용합니다.

1. **Azure 탐색기** 도구 창에서 저장소 계정을 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.
   ![저장소 계정 삭제 메뉴][DS01]

1. 저장소 계정을 삭제할지 묻는 메시지가 표시되면 **예**를 클릭합니다.
   ![저장소 계정 삭제 대화 상자][DS02]

## <a name="see-also"></a>참고 항목
Azure Storage 계정, 크기 및 가격 책정에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Microsoft Azure 저장소 소개]
* [Azure Storage 계정 정보]
* Azure Storage 계정 이름
   * [Azure의 Windows 저장소 계정 크기]
   * [Azure의 Linux 저장소 계정 크기]
* Azure Storage 계정 가격 책정
   * [Windows 저장소 계정 가격 책정]
   * [Linux 저장소 계정 가격 책정]

Java IDE용 Azure 도구 키트에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Eclipse용 Azure 도구 키트]
  * [Eclipse용 Azure 도구 키트의 새로운 기능]
  * [Eclipse용 Azure 도구 키트 설치]
  * [Eclipse용 Azure 도구 키트에 대한 로그인 지침]
  * [Eclipse에서 Azure용 Hello World 웹앱 만들기]
* [IntelliJ용 Azure 도구 키트]
  * [IntelliJ용 Azure 도구 키트의 새로운 기능]
  * [IntelliJ용 Azure 도구 키트 설치]
  * [IntelliJ용 Azure 도구 키트에 대한 로그인 지침]
  * [IntelliJ에서 Azure용 Hello World 웹앱 만들기]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터] 및 [Visual Studio Team Services용 Java 도구]를 참조하세요.

<!-- URL List -->

[Eclipse용 Azure 도구 키트]: ./azure-toolkit-for-eclipse.md
[IntelliJ용 Azure 도구 키트]: ./azure-toolkit-for-intellij.md
[Eclipse에서 Azure용 Hello World 웹앱 만들기]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ에서 Azure용 Hello World 웹앱 만들기]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Eclipse용 Azure 도구 키트 설치]: ./azure-toolkit-for-eclipse-installation.md
[IntelliJ용 Azure 도구 키트 설치]: ./azure-toolkit-for-intellij-installation.md
[Eclipse용 Azure 도구 키트에 대한 로그인 지침]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[IntelliJ용 Azure 도구 키트에 대한 로그인 지침]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Eclipse용 Azure 도구 키트의 새로운 기능]: ./azure-toolkit-for-eclipse-whats-new.md
[IntelliJ용 Azure 도구 키트의 새로운 기능]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/
[Visual Studio Team Services용 Java 도구]: https://java.visualstudio.com/

[Microsoft Azure 저장소 소개]: /azure/storage/storage-introduction
[Azure Storage 계정 정보]: /azure/storage/storage-create-storage-account
[Azure Storage 복제]: /azure/storage/storage-redundancy
[Azure Storage 확장성 및 성능 목표]: /azure/storage/storage-scalability-targets
[컨테이너, Blob 및 메타데이터 명명 및 참조]: http://go.microsoft.com/fwlink/?LinkId=255555

[Azure의 Windows 저장소 계정 크기]: /azure/virtual-machines/virtual-machines-windows-sizes
[Azure의 Linux 저장소 계정 크기]: /azure/virtual-machines/virtual-machines-linux-sizes
[Windows 저장소 계정 가격 책정]: /pricing/details/virtual-machines/windows/
[Linux 저장소 계정 가격 책정]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC02.png

