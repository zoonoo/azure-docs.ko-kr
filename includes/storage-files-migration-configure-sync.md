---
title: Azure 파일 동기화 구성
description: Azure 파일 동기화를 구성합니다. 마이그레이션 문서 간에 공유되는 공통 텍스트 블록입니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209585"
---
이 단계는 이전 단계에서 Windows Server에서 설정한 모든 리소스와 폴더를 연결합니다.

* [Azure 포털에](https://portal.azure.com)로그인합니다.
* 저장소 동기화 서비스 리소스를 찾습니다.
* 각 Azure 파일 공유에 대한 저장소 동기화 서비스 리소스 내에 새 *동기화 그룹을* 만듭니다. Azure File Sync 용어에서 Azure 파일 공유는 동기화 그룹을 만들 때 설명하는 동기화 토폴로지의 *클라우드 끝점이* 됩니다. 동기화 그룹을 만들 때 여기에 동기화되는 파일 집합을 인식할 수 있도록 친숙한 이름을 지정합니다. 일치하는 이름으로 Azure 파일 공유를 참조해야 합니다.
* 동기화 그룹이 만들어지면 동기화 그룹 목록에 표시되는 행이 표시됩니다. 동기화 그룹의 내용을 표시하려면 이름(링크)을 클릭합니다. "클라우드 끝점"에서 Azure 파일 공유가 표시됩니다.
* 명령 단추를 + *서버 끝점 추가합니다.* 프로비저닝한 로컬 서버의 폴더가 이 서버 *끝점의*경로가 됩니다.
