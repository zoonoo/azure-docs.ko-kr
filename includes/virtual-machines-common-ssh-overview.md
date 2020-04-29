---
title: 파일 포함
description: 포함 파일
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "71168617"
---
## <a name="overview-of-ssh-and-keys"></a>SSH 및 키에 대한 개요

[SSH](https://www.ssh.com/ssh/) 는 보안 되지 않은 연결에 대 한 보안 로그인을 허용 하는 암호화 된 연결 프로토콜입니다. SSH는 Azure에서 호스팅되는 Linux VM에 대한 기본 연결 프로토콜입니다. SSH 자체에서 암호화된 연결을 제공하지만 SSH 연결과 함께 암호를 하더라도 VM은 여전히 무차별 암호 대입 공격이나 암호 추측에 취약합니다. Ssh를 사용 하 여 VM에 연결 하는 보다 안전 하 고 선호 되는 방법은 *ssh 키*라고도 하는 공개-개인 키 쌍을 사용 하는 것입니다. 

* *공개 키*는 public-key 암호화를 사용하려는 Linux VM 또는 다른 서비스에 배치됩니다.

* *프라이빗 키*는 로컬 시스템에 남아 있습니다. 이 프라이빗 키는 보호해야 하는 한편, 공유하면 안됩니다.

SSH 클라이언트를 사용하여 공개 키가 있는 Linux VM에 연결하는 경우 원격 VM이 클라이언트를 테스트하여 프라이빗 키를 소유하고 있는지 확인합니다. 클라이언트에 프라이빗 키가 있으면 VM에 액세스할 수 있습니다. 

조직의 보안 정책에 따라 여러 Azure VM 및 서비스에 액세스하는 단일퍼블릭-프라이빗 키 쌍을 다시 사용할 수 있습니다. 각 VM 또는 서비스에 액세스하기 위해 별도의 키 쌍이 반드시 필요한 것은 아닙니다. 

다른 사람과 공개 키를 공유할 수 있지만 사용자(또는 로컬 보안 인프라)만이 프라이빗 키를 소유해야 합니다.