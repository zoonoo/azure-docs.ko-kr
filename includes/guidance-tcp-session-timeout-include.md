## Azure VM에 대한 TCP 설정
Azure VM은 [NAT][nat](Network Address Translation)를 사용하여 공용 인터넷과 통신합니다. NAT 장치는 Azure VM에 공용 IP 주소 및 포트를 할당하여 해당 VM이 다른 장치와의 통신을 위한 소켓을 설정하도록 허용합니다. 패킷이 특정 시간 이후에 해당 소켓을 통해 흐르는 것을 중지하는 경우 NAT 장치가 매핑을 종료하고 소켓은 다른 VM에서 사용할 수도 있습니다.

이는 일반적인 NAT 동작이며, 소켓을 시간 제한 기간을 초과하여 유지 관리하는 TCP 기반 응용 프로그램에서 통신 문제를 발생시킬 수 있습니다. *설정된 연결* 상태의 세션에 대해 두 가지 유휴 시간 제한 설정을 고려해야 합니다.

* [Azure 부하 분산 장치][azure-lb-timeout]를 통한 **인바운드**. 이 시간 제한은 4분을 기본값으로 하며 30분까지 조정할 수 있습니다.
* [SNAT][snat](원본 NAT)를 사용하는 **아웃바운드**. 이 시간 제한은 4분으로 설정되고 조정할 수 없습니다.

연결이 시간 제한을 초과하여 손실되지 않도록 하려면 응용 프로그램의 세션을 활성 상태로 유지하거나 그렇게 하도록 기본 운영 체제를 구성할 수 있습니다. 아래와 같이 Linux 및 Windows 시스템의 경우 사용하는 설정이 서로 다릅니다.

[Linux][linux]의 경우 아래 커널 변수를 변경해야 합니다. net.ipv4.tcp\_keepalive\_time = 120 net.ipv4.tcp\_keepalive\_intvl = 30 net.ipv4.tcp\_keepalive\_probes = 8

[Windows][windows]의 경우 아래의 레지스트리 값을 변경해야 합니다. KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8

위의 설정으로 연결 유지 패킷이 2분(120초)의 유휴 시간 후 전송된 다음 30초마다 전송됩니다. 또한 해당 패킷이 8번 실패하면 세션이 삭제됩니다.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md