# Vodafone Power Station (SERCOMM VOX30) disable DHCP procedure

The scope of this procedure is to disable the internal DHCP server running on the Vodafone Power Station (VPS, SERCOMM VOX30) distributed in Italy by the provider.
On this router DHCP server cannot be disabled; the only trick available was to shrink the IP addresses range available to DHCP to a single address, but actually the server remains active and can produce jam if another DHCP server is present on the network (e.g. Pi-hole): in fact, most of the times, being the VPS the central point of the LAN, it is the fastest DHCP to reply to client requests with a negative response ("No address range available for DHCP request"). These generates DHCP requests loops by the client that cannot get the network interfaces configured.

The procedure is based on injection of parameters in the web interface of the VPS. Fortunatelly the VPS backend already implements the possibility to enable or disable the DHCP server, but actually the web interface does not allow this setting. Being most of the input validation on client side (JS running in the browser), it is possible to force writing the disable bits of the DHCP server (main LAN and Guest LAN).

The following procedure have been tested on VPS with firmware XS_3.9.00.05.

Procedure:

* As _expert_ user mode, go to the Settings / IPv4 page
* 




