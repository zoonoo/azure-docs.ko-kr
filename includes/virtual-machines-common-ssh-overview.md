---
title: 파일 포함
description: 포함 파일
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513192"
---
## <a name="overview-of-ssh-and-keys"></a>SSH 및 키에 대한 개요

[SSH](https://www.ssh.com/ssh/) 는 보안 되지 않은 연결에 대 한 보안 로그인 기능을 제공 하는 암호화 된 연결 프로토콜입니다. SSH는 Azure에서 호스팅되는 Linux VM에 대한 기본 연결 프로토콜입니다. SSH는 암호화 된 연결을 제공 하지만, SSH 연결을 사용 하 여 암호를 사용 하면 VM이 무차별 암호 대입 공격에 취약 한 상태로 남아 있습니다. *Ssh 키*라고도 하는 공개-개인 키 쌍을 사용 하 여 ssh를 통해 VM에 연결 하는 것이 좋습니다. 

- *공개 키는* Linux VM에 배치 됩니다.

- *프라이빗 키*는 로컬 시스템에 남아 있습니다. 이 프라이빗 키는 보호해야 하는 한편, 공유하면 안됩니다.

SSH 클라이언트를 사용 하 여 Linux VM에 연결 하는 경우 (공개 키가 있음) 원격 VM은 클라이언트를 테스트 하 여 올바른 개인 키가 있는지 확인 합니다. 클라이언트에 프라이빗 키가 있으면 VM에 액세스할 수 있습니다. 

조직의 보안 정책에 따라 여러 Azure VM 및 서비스에 액세스하는 단일퍼블릭-프라이빗 키 쌍을 다시 사용할 수 있습니다. 각 VM 또는 서비스에 액세스하기 위해 별도의 키 쌍이 반드시 필요한 것은 아닙니다. 

공개 키는 누구와도 공유할 수 있지만 사용자 (또는 로컬 보안 인프라)만 개인 키에 액세스할 수 있어야 합니다.