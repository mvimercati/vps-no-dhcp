# Vodafone Power Station (SERCOMM VOX30) disable DHCP procedure

The scope of this procedure is to disable the internal DHCP server running on the Vodafone Power Station (VPS, SERCOMM VOX30) distributed in Italy by the provider.
On this router DHCP server cannot be disabled; the only trick available was to shrink the IP addresses range available to DHCP to a single address, but actually the server remains active and can produce jam if another DHCP server is present on the network (e.g. Pi-hole): in fact, most of the times, being the VPS the central point of the LAN, it is the fastest DHCP to reply to client requests with a negative response ("No address range available for DHCP request"). These generates DHCP requests loops by the client that cannot get the network interfaces configured.

The procedure is based on injection of parameters in the web interface of the VPS. Fortunatelly the VPS backend already implements the possibility to enable or disable the DHCP server, but actually the web interface does not allow this setting. Being most of the input validation on client side (JS running in the browser), it is possible to force writing the disable bits of the DHCP server (main LAN and Guest LAN).

The following procedure have been tested on VPS with firmware XS_3.9.00.05.

The http session expires in fe minutes, be quick!

Procedure:

1. As _expert_ user mode, go to the Settings / IPv4 page

  ![](VPS1.PNG)
 
2. Start the 'Analyze' tool within your web browser (I personally use Firefox) and catch the Apply button inner hook, as in figure:

  ![](VPS2.PNG)
  
3. The Analyze tool jumps to the HTML code associated to the Apply button. We are interested in the events associated to this button (click on 'event' label). Two events are available, one handles the animation of the button (Bubbling); the other one (JQuery) handles the interesting things (click on the _jump_ icon).

  ![](VPS3.PNG)
  
4. The JS _applyButton on click_ function is reached, but the 'send to the server' code is about 400 hundreds lines later... after a lot of input validation code, that we have to cheat :)
  Scroll down until function _dataBatchSend_ is found:

  ![](VPS4.PNG)
  
5. Set a breakpoint on this line (click on the line's number) and then click on the Apply button to trigger the event. If you correctly set the breakpoint you should reach this line very fast:

  ![](VPS5.PNG)
  
6. If you notice some lines before there is a piece of code that fill the _data_format_ structure, which is passed to _dataBatchSend_ function. This structure contains all the validated inputs present on the web form, all validated by the above code, such as DHCP Start/End IP, Subnet mask... You can also notice _settingsLanDHCP_ and _settingsLanDHCP_guest_

   You can go to the JS console to inspect that structure. Well, actually it's an array of objects. For example you can inspect the content of some elements using the _alert_ function, equivalent to old glory _printf_ :)

  ![](VPS7.PNG)

7. Now it's the turn to modify the DHCP enable parameters: _settingsLanDHCP_ and _settingsLanDHCP_guest_: write "0" to the third and fourth object's value in the array to disable both main and guest DHCP servers

  ![](VPS8.PNG)
  
8. Now click the _Continue execution_ icon in the debugger controls panel

  ![](VPS9.PNG)
  
9. The settings are confermed and your VPS DHCP server should be gone!

  ![](VPS10.PNG)

10. Reload the IPv4 configuration page. Some times may be required by the router to apply changes, networks may be disconnected, so be prepared with a static IP address for your client or a brand new DHCP server :)


