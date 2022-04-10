# Vodafone Power Station (SERCOMM VOX30) disable DHCP procedure

The scope of this procedure is to disable the internal DHCP server running on the Vodafone Power Station (VPS, SERCOMM VOX30) distributed in Italy by the provider.
On this router DHCP server cannot be disabled; the only trick available was to shrink the IP addresses range available to DHCP to a single address, but actually the server remains active and can produce jam if another DHCP server is present on the network (e.g. Pi-hole): in fact, most of the times, being the VPS the central point of the LAN, it is the fastest DHCP to reply to client requests with a negative response ("No address range available for DHCP request"). These generates DHCP requests loops by the client that cannot get the network interfaces configured.

The procedure is based on injection of parameters in the web interface of the VPS. Fortunatelly the VPS backend already implements the possibility to enable or disable the DHCP server, but actually the web interface does not allow this setting. Being most of the input validation on client side (JS running in the browser), it is possible to force writing the disable bits of the DHCP server (main LAN and Guest LAN).

The following procedure have been tested on VPS with firmware XS_3.9.00.05.

Procedure. Be fast before the VPS kick you out the configuration pages after few minutes (session expires).

* As _expert_ user mode, go to the Settings / IPv4 page

  ![](VPS1.PNG)
 
* Start the 'Analyze' tool within your web browser (I personally use Firefox) and catch the Apply button inner hook, as in figure:

  ![](VPS2.PNG)
  
* The Analyze tool jumps to the HTML code associated to the Apply button. We are interested in the events associated to this button (click on 'event' label). Two events are available, one handles the animation of the button; the jquery one handles the interesting things (click on the _jump_ icon).

  ![](VPS3.PNG)
  
* The _JS_ applyButton on click function is reached, but the 'send to the server' code is about 400 hundreds lines later... after a lot of input validation code, that we have to cheat :)
  Scroll down until function _dataBatchSend_ is found:

  ![](VPS4.PNG)
  
* Set a breakpoint on this line (click on the line's number) and the click and the real button. If you correctly set the breakpoint you should reach this line very fast:

  ![](VPS5.PNG)
  
* If you notice some lines before the _dataBatchSend_ there is the population of _data_format_ structure, which is passed to _dataBatchSend_ function. This structure contains all the validated inputs present on this configuration web page, all validated. Actually you could notice some names of information available on that page, such as DHCP Start/End IP, Subnet mask... You can also notice _settingsLanDHCP_ and _settingsLanDHCP_guest_

  ![](VPS6.PNG)
  
* You can go to the console to inspect that structure. Well, actually it's an array of objects. For example you can inspect the content with the _alert_ function, equivalent to old glory _printf_ :)

  ![](VPS7.PNG)

* Now it's the turn to modify the DHCP enable parameters: _settingsLanDHCP_ and _settingsLanDHCP_guest_
  Write to "0" the third and fourth object's value in the array to disable both main and guest DHCP servers

  ![](VPS8.PNG)
  
* Now in the debugger controls button press _Continue execution_ icon

  ![](VPS9.PNG)
  
* The settings are confermed and your VPS DHCP server should be gone!

  ![](VPS10.PNG)

* Reload the IPv4 configuration page. Some times may be required by the router to apply changes, networks may be disconnected, so be prepared with a static IP address for your client or a brand new DHCP server :)


