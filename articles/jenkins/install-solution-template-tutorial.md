---
title: 자습서 - Azure에서 Jenkins 서버 만들기
description: 이 자습서에서는 Jenkins 솔루션 템플릿에서 Azure Linux 가상 머신에 Jenkins를 설치하고 샘플 Java 애플리케이션을 빌드합니다.
keywords: Jenkins, Azure, DevOps, 포털, 가상 머신, 솔루션 템플릿
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274532"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>자습서: Azure Linux VM에 Jenkins 서버 만들기 

이 자습서는 Azure에서 사용 가능하도록 구성된 도구와 플러그 인을 사용하여 Ubuntu Linux VM에 [Jenkins](https://jenkins.io)를 설치하는 방법을 보여 줍니다. 작업을 완료하면 [GitHub](https://github.com)에서 샘플 Java 앱을 빌드하는 Jenkins 서버가 Azure에서 실행됩니다.

> [!div class="checklist"]
> * Azure에서 Jenkins 서버 설치 및 구성
> * SSH 터널을 사용하여 Jenkins 콘솔에 액세스
> * 프리스타일 프로젝트 만들기
> * 코드 컴파일 및 샘플 앱 패키징

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]