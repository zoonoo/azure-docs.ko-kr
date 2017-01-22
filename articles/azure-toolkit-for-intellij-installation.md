---
title: "IntelliJ용 Azure 도구 키트 설치 | Microsoft Docs"
description: "IntelliJ IDEA용 Azure 도구 키트를 설치하는 방법을 알아봅니다."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 349252e6629c0eda4d603399345206903d530578


---
# <a name="installing-the-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트 설치
IntelliJ용 Azure 도구 키트는 IntelliJ IDEA 개발 환경에서 Azure 응용 프로그램을 쉽게 작성, 개발, 테스트 및 배포할 수 있는 템플릿과 기능을 제공합니다. IntelliJ용 Azure 도구 키트는 다음 URL에 있는 GitHub의 프로젝트 사이트를 통해 MIT 라이선스에 따라 소스 코드 사용이 허가된 오픈 소스 프로젝트입니다.

<https://github.com/microsoft/azure-tools-for-java>

IntelliJ용 Azure 도구 키트는 설정 대화 상자와 시작 화면의 구성 메뉴에서 설치할 수 있습니다. 다음 단계에서는 두 설치 방법을 모두 설명합니다.

[!INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>설정 대화 상자에서 IntelliJ용 Azure 도구 키트를 설치하려면
1. IntelliJ IDEA를 시작합니다.
2. IntelliJ IDEA가 열리면 **File**을 클릭한 다음 **Settings**를 클릭합니다.
   
    ![IntelliJ IDEA Settings 대화 상자 열기][01a]
3. 설정 대화 상자에서 **Plugins**를 클릭하고 **Browse repositories**를 클릭합니다.
   
    ![IntelliJ IDEA Settings 대화 상자][02a]
4. **Browse repositories** 대화 상자에서 검색 상자에 "Azure"를 입력합니다. **IntelliJ용 Azure 도구 키트**를 강조 표시하고 **Install**을 클릭합니다.
   
    ![IntelliJ용 Azure 도구 키트 검색][03]
   
    IntelliJ IDEA 대화 상자에서 설치 진행 상태가 표시됩니다.
   
    ![설치 진행률][04]
5. 설치가 완료되면 **Restart IntelliJ IDEA**를 클릭합니다.
   
    ![Restart IntelliJ IDEA][05]
6. **OK** 를 클릭하여 Settings 대화 상자를 닫습니다.
   
    ![IntelliJ IDEA Settings 대화 상자 닫기][06]
7. IntelliJ IDEA를 다시 시작할지 또는 연기할지 묻는 메시지가 나타나면 **Restart**를 클릭합니다.
   
    ![Restart IntelliJ IDEA][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>시작 화면에서 IntelliJ용 Azure 도구 키트를 설치하려면
1. IntelliJ IDEA를 시작합니다.
2. IntelliJ IDEA 시작 화면이 나타나면 **Configure**를 클릭하고 **Plugins**를 클릭합니다.
   
    ![IntelliJ IDEA 플러그 인 설치][01b]
3. **Plugins** 대화 상자에서 **Browse repositories**를 클릭합니다.
   
    ![IntelliJ 아이디어 플러그 인 저장소 찾아보기][02b]
4. **Browse repositories** 대화 상자에서 검색 상자에 "Azure"를 입력합니다. **IntelliJ용 Azure 도구 키트**를 강조 표시하고 **Install**을 클릭합니다.
   
    ![IntelliJ용 Azure 도구 키트 검색][03]
   
    IntelliJ IDEA 대화 상자에서 설치 진행 상태가 표시됩니다.
   
    ![설치 진행률][04]
5. 설치가 완료되면 **Restart IntelliJ IDEA**를 클릭합니다.
   
    ![Restart IntelliJ IDEA][05]
6. IntelliJ IDEA를 다시 시작할지 또는 연기할지 묻는 메시지가 나타나면 **Restart**를 클릭합니다.
   
    ![Restart IntelliJ IDEA][07]

## <a name="see-also"></a>참고 항목
Java IDE용 Azure 도구 키트에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Eclipse용 Azure 도구 키트]
  * [Eclipse용 Azure 도구 키트 설치]
  * [Eclipse에서 Azure용 Hello World 웹앱 만들기]
  * [Eclipse용 Azure 도구 키트의 새로운 기능]
* [IntelliJ용 Azure 도구 키트]
  * *IntelliJ용 Azure 도구 키트 설치(이 문서)*
  * [IntelliJ에서 Azure용 Hello World 웹앱 만들기]
  * [IntelliJ용 Azure 도구 키트의 새로운 기능]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터]를 참조하세요.

<!-- URL List -->

[Eclipse용 Azure 도구 키트]: ./azure-toolkit-for-eclipse.md
[IntelliJ용 Azure 도구 키트]: ./azure-toolkit-for-intellij.md
[Eclipse에서 Azure용 Hello World 웹앱 만들기]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ에서 Azure용 Hello World 웹앱 만들기]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Eclipse용 Azure 도구 키트 설치]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Eclipse용 Azure 도구 키트의 새로운 기능]: ./azure-toolkit-for-eclipse-whats-new.md
[IntelliJ용 Azure 도구 키트의 새로운 기능]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png



<!--HONumber=Jan17_HO1-->


