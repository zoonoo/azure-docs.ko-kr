---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 04/14/2021
ms.openlocfilehash: a33a702128be0d8e90b3db635001dbfbdb279f2a
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575951"
---
|이미지 형식  |일반화  |특수화  |
|---------|---------|---------|
|대상     |모든 시스템에 배포됩니다.         | 특정 시스템을 대상으로 합니다.        |
|부팅 후 설정     | VM을 처음 부팅할 때 설정해야 합니다.          | 설정은 필요하지 않습니다. <br> 플랫폼이 VM을 설정합니다.        |
|구성     |호스트 이름, 관리자-사용자 및 기타 VM별 설정이 필요합니다.         |사전 구성.         |
|사용 시기     |동일한 이미지에서 새 VM을 여러 개 만듭니다.         |특정 머신을 마이그레이션하거나 이전 백업에서 VM 복원 중입니다.         |
