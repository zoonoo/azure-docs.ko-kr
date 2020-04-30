---
title: Azure File Sync 구성
description: Azure File Sync를 구성 합니다. 마이그레이션 문서 전체에서 공유 되는 일반 텍스트 블록
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143601"
---
이 단계에서는 이전 단계에서 Windows Server 인스턴스에 설정한 모든 리소스와 폴더를 연결 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 저장소 동기화 서비스 리소스를 찾습니다.
1. 각 Azure 파일 공유에 대해 저장소 동기화 서비스 리소스 내에 새 *동기화 그룹* 을 만듭니다. Azure File Sync 용어로, Azure 파일 공유는 동기화 그룹 만들기로 설명 하는 동기화 토폴로지의 *클라우드 끝점이* 됩니다. 동기화 그룹을 만들 때 여기에 동기화 되는 파일 집합을 인식할 수 있도록 친숙 한 이름을 지정 합니다. 이름이 일치 하는 Azure 파일 공유를 참조 하는지 확인 합니다.
1. 동기화 그룹을 만든 후에는 해당 그룹에 대 한 행이 동기화 그룹 목록에 표시 됩니다. 이름 (링크)을 선택 하 여 동기화 그룹의 내용을 표시 합니다. **클라우드 끝점**에서 Azure 파일 공유를 볼 수 있습니다.
1. **+ 서버 끝점 추가** 단추를 찾습니다. 프로 비전 한 로컬 서버의 폴더가이 *서버 끝점*의 경로가 됩니다.
