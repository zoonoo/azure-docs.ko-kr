---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 590e5494a8c8f9d4e06b69af0708e83d53be72b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943759"
---
압축 된 오디오 처리는 [GStreamer를](https://gstreamer.freedesktop.org)사용하여 구현됩니다. 라이선스상의 이유로 GStreamer 바이너리는 컴파일되고 음성 SDK와 연결되지 않습니다. 개발자는 여러 종속성 및 플러그인을 설치해야 합니다.

# <a name="ubuntu-1604-1804-or-debian-9"></a>[우분투, 16.04, 18.04 또는 데비안 9](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rehl--centos"></a>[REHL / 센토스](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> RHEL / CentOS에서 [리눅스에 대한 OpenSSL을 구성하는 방법에 대한](../../../../how-to-configure-openssl-linux.md)지침을 따르십시오.

---