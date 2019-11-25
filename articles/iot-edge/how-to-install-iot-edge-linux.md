---
title: Linux에 Azure IoT Edge 설치 | Microsoft Docs
description: Azure IoT Edge installation instructions on Linux devices running Ubuntu or Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.openlocfilehash: ec463efb1282c311757bb90fd614e1247459c80f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457332"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Install the Azure IoT Edge runtime on Debian-based Linux systems

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. To learn more, see [Understand the Azure IoT Edge runtime and its architecture](iot-edge-runtime.md).

This article lists the steps to install the Azure IoT Edge runtime on an X64, ARM32, or ARM64 Linux device. Installation packages are provided for Ubuntu Server 16.04, Ubuntu Server 18.04, and Raspbian Stretch. Refer to [Azure IoT Edge supported systems](support.md#operating-systems) for a list of supported Linux operating systems and architectures.

>[!NOTE]
>Support for ARM64 devices is in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Linux 소프트웨어 저장소의 패키지는 각 패키지에 있는 사용 조건에 따릅니다(/usr/share/doc/*package-name*). 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="install-the-latest-runtime-version"></a>Install the latest runtime version

Use the following sections to install the most recent version of the Azure IoT Edge runtime onto your device. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft 키 및 소프트웨어 리포지토리 피드 등록

Prepare your device for the IoT Edge runtime installation.

Install the repository configuration. Choose the **16.04** or **18.04** command that matches your device operating system:

* **Ubuntu Server 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:
   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copy the generated list.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Install Microsoft GPG public key

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>컨테이너 런타임 설치

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 런타임을 사용합니다. For production scenarios, we recommended that you use the [Moby-based](https://mobyproject.org/) engine provided below. Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

Perform apt update.

   ```bash
   sudo apt-get update
   ```

Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

Moby CLI(명령줄 인터페이스)를 설치합니다. CLI는 개발에 유용하지만 프로덕션 배포에서는 선택 사항입니다.

   ```bash
   sudo apt-get install moby-cli
   ```

If you encounter errors when installing the Moby container runtime, follow the steps to [Verify your Linux kernel for Moby compatibility](#verify-your-linux-kernel-for-moby-compatibility), provided later in this article. 

### <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 설치

The **IoT Edge security daemon** provides and maintains security standards on the IoT Edge device. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

The installation command also installs the standard version of the **libiothsm** if not already present.

Perform apt update.

   ```bash
   sudo apt-get update
   ```

보안 디먼을 설치합니다. 패키지가 `/etc/iotedge/`에 설치됩니다.

   ```bash
   sudo apt-get install iotedge
   ```

Once IoT Edge is successfully installed, the output will prompt you to update the configuration file. Follow the steps in the [Configure the Azure IoT Edge security daemon](#configure-the-security-daemon) section to finish provisioning your device. 

## <a name="install-a-specific-runtime-version"></a>Install a specific runtime version

If you want to install a specific version of the Azure IoT Edge runtime, you can target the component files directly from the IoT Edge GitHub repository. Use the following steps to get all of the IoT Edge components onto your device: the Moby engine and CLI, the libiothsm, and finally the IoT Edge security daemon.

1. Navigate to the [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases), and find the release version that you want to target. 

2. Expand the **Assets** section for that version.

3. There may or may not be updates to the Moby engine in any given release. If you see files that start with **moby-engine** and **moby-cli**, use the following commands to update those components. If you don't see any Moby files, go back through the older release assets until you find the most recent version. 

   1. Find the **moby-engine** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address.

   2. Use the copied link in the following command to install that version of the Moby engine: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Find the **moby-cli** file that matches your IoT Edge device's architecture. The Moby CLI is an optional component, but can be helpful during development. Right-click on the file link and copy the link address. 

   4. Use the copied link in the following command to install that version of the Moby CLI: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Every release should have new files for the IoT Edge security daemon and the hsmlib. Use the following commands to update those components. 

   1. Find the **libiothsm-std** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address. 

   2. Use the copied link in the following command to install that version of the hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Find the **iotedge** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address. 

   4. Use the copied link in the following command to install that version of the IoT Edge security daemon. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Once IoT Edge is successfully installed, the output will prompt you to update the configuration file. Follow the steps in the next section to finish provisioning your device. 

## <a name="configure-the-security-daemon"></a>Configure the security daemon

물리적 디바이스를 Azure IoT Hub에 있는 디바이스 ID와 연결하도록 IoT Edge 런타임을 구성합니다.

디먼은 `/etc/iotedge/config.yaml`에 있는 구성 파일을 사용하여 구성할 수 있습니다. 이 파일은 기본적으로 쓰기 금지되어 있습니다 편집하려면 관리자 권한이 필요합니다.

IoT Hub에서 제공하는 디바이스 연결 문자열을 사용하여 단일 IoT Edge 디바이스를 수동으로 프로비전할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 디바이스를 프로비전할 수 있습니다. 이는 많은 디바이스를 프로비전할 때 유용합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다.

### <a name="option-1-manual-provisioning"></a>옵션 1: 수동 프로비전

디바이스를 수동으로 프로비전하려면 IoT Hub에 새 디바이스를 등록하여 만들 수 있는 [디바이스 연결 문자열](how-to-register-device.md#register-in-the-azure-portal)을 디바이스에 프로비전해야 합니다.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

Find the provisioning configurations of the file and uncomment the **Manual provisioning configuration** section. **device_connection_string**의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다. Make sure any other provisioning sections are commented out.

   ```yaml
   # Manual provisioning configuration
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   attestation:
   #     method: "tpm"
   #     registration_id: "{registration_id}"
```
To paste clipboard contents into Nano `Shift+Right Click` or press `Shift+Insert`.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>옵션 2: 자동 프로비전

디바이스를 자동으로 프로비전하려면 [Device Provisioning Service를 설정하고 디바이스 등록 ID를 검색](how-to-auto-provision-simulated-device-linux.md)합니다. There are a number of attestation mechanisms supported by IoT Edge when using automatic provisioning but your hardware requirements also impact your choices. For example, Raspberry Pi devices do not come with a Trusted Platform Module (TPM) chip by default.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

Find the provisioning configurations of the file and uncomment the section appropriate for your attestation mechanism. When using TPM attestation, for example, update the values of **scope_id** and **registration_id** with the values from your IoT Hub Device Provisioning service and your IoT Edge device with TPM, respectively.

   ```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
   ```

To paste clipboard contents into Nano `Shift+Right Click` or press `Shift+Insert`.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

이전 섹션의 **수동 구성** 단계를 사용한 경우 IoT Edge 런타임을 디바이스에 성공적으로 프로비전하고 실행해야 합니다. **자동 구성** 단계를 사용한 경우 사용자를 대신하여 런타임에서 디바이스를 IoT 허브에 등록할 수 있도록 몇 가지 추가 단계를 수행해야 합니다. For next steps, see [Create and provision a simulated TPM IoT Edge device on a Linux virtual machine](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

You can check the status of the IoT Edge Daemon:

```bash
systemctl status iotedge
```

Examine daemon logs:

```bash
journalctl -u iotedge --no-pager --no-full
```

Run an automated check for the most common configuration and networking errors: 

```bash
sudo iotedge check
```

And, list running modules:

```bash
sudo iotedge list
```

After installing IoT Edge on your device, the only module you should see running is **edgeAgent**. Once you create your first deployment, the other system module **$edgeHub** will start on the device as well. For more information, see [deploy IoT Edge modules](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>팁 및 문제 해결

`iotedge` 명령을 실행하려면 상승된 권한이 필요합니다. 런타임을 설치한 후 머신에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다. 그 전까지는 `iotedge` 명령 앞에 **sudo**를 사용합니다.

리소스가 제한된 디바이스에서는 [문제 해결 가이드](troubleshoot.md)의 지침에 따라 *OptimizeForPerformance* 환경 변수를 *false*로 설정하는 것이 좋습니다.

프록시 서버가 있는 네트워크의 경우 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)의 단계를 수행합니다.

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verify your Linux kernel for Moby compatibility

Many embedded device manufacturers ship device images that contain custom Linux kernels without the features required for container runtime compatibility. If you encounter issues while installing the recommended Moby container runtime, you may be able to troubleshoot your Linux kernel configuration using the [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) script from the official [Moby GitHub repository](https://github.com/moby/moby). Run the following commands on the device to check your kernel configuration:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

This will provide a detailed output that contains the status of kernel features that are used by the Moby runtime. You will want to ensure that all items under `Generally Necessary` and  `Network Drivers` are enabled to ensure that your kernel is fully compatible with the Moby runtime.  If you have identified any missing features, enable them by rebuilding your kernel from source and selecting the associated modules for inclusion in the appropriate kernel .config.  Similarly, if you are using a kernel configuration generator like defconfig or menuconfig, find and enable the respective features and rebuild your kernel accordingly.  Once you have deployed your newly modified kernel, run the check-config script again to verify that all the required features were successfully enabled.


## <a name="uninstall-iot-edge"></a>IoT Edge 제거

Linux 디바이스에서 IoT Edge 설치를 제거하려면 명령줄에서 다음 명령을 사용합니다.

IoT Edge 런타임을 제거합니다.

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge 런타임을 제거하면 만든 컨테이너는 중지되지만 디바이스에는 계속 남아 있습니다. 모든 컨테이너를 보고 남아 있는 항목을 확인합니다.

```bash
sudo docker ps -a
```

두 개의 런타임 컨테이너를 포함하여 디바이스에서 컨테이너를 삭제합니다.

```bash
sudo docker rm -f <container name>
```

마지막으로 디바이스에서 컨테이너 런타임을 제거합니다.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>다음 단계

런타임을 설치하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

If you are having problems with the IoT Edge runtime installing properly, check out the [troubleshooting](troubleshoot.md) page.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
