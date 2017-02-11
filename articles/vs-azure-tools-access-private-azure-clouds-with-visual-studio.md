---
title: "Visual Studio로 사설 Azure 클라우드에 액세스 | Microsoft Docs"
description: "Visual Studio를 사용하여 사설 클라우드 리소스에 액세스하는 방법에 대해 알아봅니다."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 26fea09810e4a5ed1dc19123a5354905ec3e37ea


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Visual Studio로 사설 Azure 클라우드에 액세스
## <a name="overview"></a>개요
기본적으로 Visual Studio는 공용 Azure 클라우드 REST 끝점을 지원합니다. 하지만 사설 Azure 클라우드와 Visual Studio를 사용하는 경우 이 작업은 문제가 될 수 있습니다. 인증서를 사용하여 사용자 Azure 클라우드 REST 끝점에 액세스하기 위해 Visual Studio를 구성할 수 있습니다. Azure 게시 설정 파일을 통해 이러한 인증서를 얻을 수 있습니다.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Visual Studio의 사설 클라우드에 액세스 하려면
1. 사설 클라우드를 위한 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885) 에서 게시 설정 파일을 다운로드 하거나 게시 설정 파일에 대해 관리자에게 문의합니다. Azure의 공용 버전에서 이 설정의 다운로드에 대한 링크: [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (다운로드하는 파일의 확장명에는.publishsettings 있어야 합니다.)
2. **서버 탐색기**의 Visual Studio에서**Azure** 노드를 선택하고, 바로 가기 메뉴에서 **구독 관리** 명령을 선택합니다.
   
    ![구독 명령 관리](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)
3. **Microsoft Azure 구독 관리** 대화 상자에서 **인증서** 탭을 선택한 후 **가져오기** 단추를 선택합니다.
   
    ![Azure 인증서 가져오기](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)
4. **Microsoft Azure 구독 가져오기** 대화 상자에서 게시 설정 파일을 저장한 폴더를 찾고 파일을 선택한 다음 **가져오기** 단추를 선택합니다. 이렇게 하면 Visual Studio로 게시 설정 파일에서 인증서를 가져옵니다. 이제 사설 클라우드 리소스와 상호 작용할 수 있습니다.
   
    ![게시 설정 가져오기](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>다음 단계
[Visual Studio에서 Azure 클라우드 서비스에 게시](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[방법: 게시 설정 및 구독 정보를 다운로드 및 가져오기](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)




<!--HONumber=Nov16_HO3-->


