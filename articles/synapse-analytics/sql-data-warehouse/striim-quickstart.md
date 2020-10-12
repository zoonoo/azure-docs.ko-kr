---
title: Striim 빠른 시작
description: Striim 및 Azure Synapse Analytics를 빠르게 시작 하세요.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d6b5fa1a42201479c5d426a5bbb9188d0cb8e03d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89485977"
---
# <a name="striim-azure-synapse-analytics-marketplace-offering-install-guide"></a>Striim Azure Synapse Analytics Marketplace 제품 설치 가이드

이 빠른 시작에서는 Azure Synapse Analytics의 기존 인스턴스가 이미 있다고 가정 합니다.

Azure Marketplace에서 Striim을 검색 하 고 Azure Synapse Analytics (준비 됨)에 대 한 데이터 통합 Striim를 선택 합니다. 

![Striim 설치][install]

지정된 속성으로 Striim VM을 구성하고 Striim 클러스터 이름, 암호 및 관리자 암호를 기록해 둡니다.

![Striim 구성][configure]

배포되면 Azure Portal에서 \<VM Name>-masternode를 클릭하고 연결을 클릭한 다음, VM 로컬 계정을 사용하여 로그인을 복사합니다. 

![Azure Synapse Analytics에 Striim 연결][connect]

<https://www.microsoft.com/en-us/download/details.aspx?id=54671>의 sqljdbc42.jar을 로컬 머신에 다운로드합니다. 

명령줄 창을 열고 JDBC jar를 다운로드한 위치로 디렉터리를 변경합니다. SCP를 통해 jar 파일을 Striim VM으로 전송하고 Azure Portal에서 주소 및 암호 가져오기

![VM에 jar 파일 복사][copy-jar]

다른 명령줄 창을 열거나 ssh 유틸리티를 사용하여 Striim 클러스터에 대해 ssh를 수행합니다.

![클러스터에 대해 SSH 수행][ssh]

다음 명령을 실행하여 JDBC jar 파일을 Striim의 lib 디렉터리로 이동하고 서버를 시작했다가 중지합니다.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Striim 클러스터 시작][start-striim]

이제 즐겨 찾는 브라우저를 열고 \<DNS Name>:9080으로 이동합니다.

![로그인 화면으로 이동][navigate]

Azure Portal에서 설정한 사용자 이름과 암호로 로그인하고 원하는 마법사를 선택하여 시작하거나 앱 페이지로 끌어서 놓기 UI를 사용합니다.

![서버 자격 증명으로 로그인][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
