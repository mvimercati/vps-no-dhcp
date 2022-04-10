# Vodafone Power Station (SERCOMM VOX30) disable DHCP procedure

The scope of this procedure is to disable the internal DHCP server running on the Vodafone Power Station (VPS, SERCOMM VOX30) distributed in Italy by the provider.
On this router DHCP servers cannot be disabled; the only trick available wto limit the DHCP was to shrink the IP addresses range available to the DHCP to a single address, but actually the server is still running and can produce jam if another DHCP server is present on the network (e.g. Pi-hole).

