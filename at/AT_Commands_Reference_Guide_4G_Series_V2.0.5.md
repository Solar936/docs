# AT Commands

## Reference Guide

OneMO_12174

4G Series
Version: V2.0.5
Release Date: 2025/3/25

中移物联网有限公司

## Services & Supports

If you have comments, questions, or ideas regarding the products, or any that are not answered by querying the manual, please contact us in the following ways.

- Web： onemo10086.com
- Email： SmartModule@cmiot.chinamobile.com
- Hotline：400-110-0866

OneMO_12174

中移物联网有限公司

## Statement

### Notice

Some features and functions of the product and its accessories described in this manual depend on the designs and performances of your local network or the software you installed. Some features and functionality may not be available due to local network operators or ISPs, or due to local network settings, or if your installed software does not support it. Therefore, the descriptions in this manual may not correspond exactly to the products you purchased or their accessories.

### Limitation of Liability

The contents of this manual are provided "as is" and shall not provide any express or implied warranties, with respect to the contents of this manual, except as may be required by applicable law, including but not limited to the merchantability or fitness for a particular specific purpose of the guarantee.

To the extent permitted by applicable law, China Mobile IoT Co., Ltd. shall in no event be liable for any special, indirect, incidental or consequential damages arising from the use of the contents of this manual and the products described in this manual of any damages, nor will any profit, loss of data, goodwill or expected savings be compensated.

The maximum liability (this limitation shall not apply to liability for personal injury to the extent applicable law prohibits such a limitation) of China Mobile IoT arising from the use of the product described in this manual shall be limited to the amount paid by customers for the purchase of this product.

China Mobile IoT Co., Ltd. reserves the right to modify any information in this manual, without prior notice and does not assume any responsibility.

### Trademark

OneMO_12174 is the registered trademark of China Mobile.

Other trademarks, product names, service names, and company names that appear in this manual and in the products that are described in this manual are the property of their respective owners.

### Import and Export Regulations

To export, re-export or import the products (including but not limited to the software and technical data in the products) described in this manual, customers shall comply with applicable import and export control laws and regulations.

### Privacy Protection

To understand how we protect your personal information, please read our privacy policy.

中移物联网有限公司

### Operating System Upgrade

The operating system only supports the official upgrade. Users are responsible for the security risks and losses caused by installing unofficial system by themselves.

### Firmware Integrity Risk

The firmware only supports the official upgrade. Users are responsible for the security risks and losses caused by installing unofficial firmware by themselves.

Copyright © China Mobile IoT Co., Ltd. All Rights Reserved.

No part of this manual may be reproduced, copied, or transmitted in any form or by any means without prior written consent of China Mobile IoT Co., Ltd.

The products described in this manual may contain software copyrighted by China Mobile IoT Co., Ltd., and its potential licensors. No one may in any manner reproduce, distribute, modify, decompile, disassemble, decrypt, extract, reverse engineer, lease, assign, or sublicense the said software, unless such restrictions are prohibited by applicable laws or such actions are approved by respective copyright holders.

OneMO_12174

中移物联网有限公司

## About the Document

### Revision History

| Version | Summary of Change |
|---------|-------------------|
| V1.0.0 | Initial release |
| V1.1.0 | Added ML307A related information. |
| V1.2.0 | Added ML302A related information. |
| V1.3.0 | Added ML305U related information. |
| V1.4.0 | Added ML305A related information. |
| V1.4.1 | Added ML307A-DL related information. |
| V1.5.0 | Added ML305A-DL related information; Modified supported/unsupported commands. |
| V1.6.0 | Added ML305M related information. |
| V1.7.0 | Added ML307R related information; Added ML307M-DL/ML307M-DA/ML307R-BL/ML307R-MC/ML307R-ML related information. |
| V1.7.1 | Modified supported parameters of the defined value of `<chset>` in chapter 3.21. |
| V1.8.0 | Modified supported sub model information in chapter 5.4. Modified supported/unsupported commands. |
| V1.8.1 | Added ML307G related information. |
| V1.9.0 | Added ML307G-DC related information. |
| V2.0.0 | Added ML307M-DA related information. Added ML551Z related information. |
| V2.0.1 | Added ML307H-DU/ML307H-GU related information. |
| V2.0.2 | Added ML307C-DL-CN related information. |
| V2.0.3 | Added ML307N-DC/ML307N-DL related information. |
| V2.0.4 | Added ML307X-DB/ML307X-DC related information. |
| V2.0.5 | Added ML307M-DH related information. |

中移物联网有限公司

## Table of Contents

- Services & Supports
- Statement
- About the Document
- List of Tables
- 1. Introduction
  - 1.1. Applicable model
- 2. AT Command Overview
  - 2.1. AT Command Syntax
  - 2.2. AT Command Response
- 3. General Commands
  - 3.1. ATE Set command echo mode
  - 3.2. ATS3 Set command line termination character
  - 3.3. ATS4 Set response formatting character
  - 3.4. ATS5 Command line editing character
  - 3.5. +++ Escape from data mode
  - 3.6. AT&F Set all current parameters to manufacturer defaults
  - 3.7. ATV Set result code format mode
  - 3.8. ATQ Set result code presentation mode
  - 3.9. ATZ Set current parameters to user defined profile
  - 3.10. ATX Set connect result code format and call monitoring
  - 3.11. ATI Display product identification information
  - 3.12. AT+GMI Request manufacturer identification
  - 3.13. AT+CGMI Request manufacturer identification
  - 3.14. AT+GMM Request model identification
  - 3.15. AT+CGMM Request model identification
  - 3.16. AT+GMR Request revision identification
  - 3.17. AT+CGMR Request revision identification
  - 3.18. AT+GSN Request product serial number identification
  - 3.19. AT+CGSN Request product serial number identification
  - 3.20. AT+IPR Set fixed DTE rate
  - 3.21. AT+CSCS Set TE character set
- 4. Call Control Commands
  - 4.1. ATS0 Automatic answer
  - 4.2. ATA Answer incoming call
  - 4.3. ATD Mobile originated call to dial a number
  - 4.4. ATH Disconnect existing connection
  - 4.5. AT+CHUP Hang up call
  - 4.6. AT+CEER Extended error report
  - 4.7. AT+CRC Cellular result codes and ring
- 5. Network Service Commands
  - 5.1. AT+CREG Network registration
  - 5.2. AT+COPS Operator Selection
  - 5.3. AT+CLCK Facility lock
  - 5.4. AT+CHLD Call related supplementary services
  - 5.5. AT+CLCC List current calls of ME
  - 5.6. AT+CPOL Preferred operator list
  - 5.7. AT+CPLS Selection of preferred PLMN list
  - 5.8. AT+COPN Read operator names
- 6. ME Control and Status Commands
  - 6.1. AT+CPAS Mobile equipment activity status
  - 6.2. AT+CFUN Set phone functionality
  - 6.3. AT+CSQ Signal quality
  - 6.4. AT+CESQ Extended signal quality
  - 6.5. AT+CCLK Real time clock
  - 6.6. AT+CLAC List all available AT commands
  - 6.7. AT+CTZU Automatic time zone update
  - 6.8. AT+CTZR Time zone report
- 7. Packet Domain Commands
  - 7.1. AT+CGDCONT Define PDP context
  - 7.2. AT+CGTFT Traffic flow template
  - 7.3. AT+CGATT Attachment or detachment of PS
  - 7.4. AT+CGACT Activate or deactivate PDP context
  - 7.5. AT+CGPADDR Show PDP address
  - 7.6. AT+CGCLASS GPRS mobile station class
  - 7.7. AT+CGEREP Packet domain event reporting
  - 7.8. AT+CGREG Network registration status
  - 7.9. AT+CEREG EPS network registration status
  - 7.10. AT+CGCONTRDP PDP context read dynamic
  - 7.11. AT+CGEQOS Defined EPS quality of service
  - 7.12. AT+CGEQOSRDP EPS quality of service read dynamic parameters
  - 7.13. AT+CEMODE UE modes of operation for EPS
  - 7.14. AT+CGDEL Delete non-active PDP contexts
  - 7.15. AT+CGAUTH Define PDP context authentication parameters
- 8. SIM Related Commands
  - 8.1. AT+CPIN PIN authentication
  - 8.2. AT+CPWD Change password
  - 8.3. AT+CSIM Generic SIM access
  - 8.4. AT+CRSM Restricted SIM access
  - 8.5. AT+CNUM Subscriber number
  - 8.6. AT+CIMI Request international mobile subscriber identity
  - 8.7. AT+CCHO Open UICC logical channel
  - 8.8. AT+CCHC Close UICC logical channel
  - 8.9. AT+CGLA Generic UICC logical channel access
- 9. SMS Related Commands
  - 9.1. AT+CSMS Select message service
  - 9.2. AT+CMGF Select SMS message format
  - 9.3. AT+CSMP Set SMS text mode parameters
  - 9.4. AT+CSCA Service center address
  - 9.5. AT+CSDH Show SMS text mode parameters
  - 9.6. AT+CNMI SMS event reporting configuration
  - 9.7. AT+CMGR Read message
  - 9.8. AT+CMGC Send command
  - 9.9. AT+CMGL List messages
  - 9.10. AT+CMGD Delete message
  - 9.11. AT+CMGW Write message to memory
  - 9.12. AT+CMGS Send message
  - 9.13. AT+CMSS Send message from storage
  - 9.14. +CMT/+CMTI Indication new short message (For SMS)
  - 9.15. +CDS/+CDSI Indicates SMS status report has been received
  - 9.16. AT+CPMS Preferred SMS message storage
  - 9.17. AT+CMMS Set SMS concat
- 10. ME Error Codes Related Commands
  - 10.1. AT+CMEE Error message format
  - 10.2. +CME ERROR ME Error code reporting
  - 10.3. +CMS ERROR ME Error code reporting

## List of Tables

- Table 1: Applicable modules
- Table 2: AT Command and Response Type
- Table 3: AT Command Response Type
- Table 4: The relationship table of combination of bit3 and bit4 of parameter `<fo>` and type of parameter `<vp>` in ML305M/ML307M/ML307N
- Table 5: General errors
- Table 6: Errors for CS, GPRS and UMTS
- Table 7: Errors for EPS
- Table 8: Errors for GPRS and UMITS
- Table 9: Errors for EPS
- Table 10: VBS, VGCS and eMLPP-related errors
- Table 11: ML305M/ML307M/ML307N extended errors

---

# 1. Introduction

## 1.1. Applicable model

**Table 1. Applicable modules**

| Module Series | Sub Model |
|---------------|-----------|
| ML302A | ML302A-DCLM/ML302A-DSLM/ML302A-GCLM/ML302A-GSLM |
| ML305A | ML305A-DC/ML305A-DS/ML305A-DL |
| ML307A | ML307A-DCLN/ML307A-DSLN/ML307A-GCLN/ML307A-GSLN/ML307A-DL |
| ML302S | ML302S-DNLM |
| ML307S | ML307S-DNLM |
| ML305U | ML305U-DBLN |
| ML305M | ML305M-DSLM |
| ML307R | ML307R-DL/ML307R-DC/ML307R-BL/ML307R-MC/ML307R-ML |
| ML551Z | ML551Z-SL |
| ML307G | ML307G-DL/ML307G-DC |
| ML307M | ML307M-DL/ML307M-DA/ML307M-DH |
| ML307H | ML307H-DU/ML307H-GU |
| ML307C | ML307C-DL-CN |
| ML307N | ML307N-DC/ML307N-DL |
| ML307X | ML307X-DB/ML307X-DC |

中移物联网有限公司

---

# 2. AT Command Overview

This chapter mainly introduces AT command definition and its syntax format.

AT command is a string that sent in a specific format from TE (Terminal Equipment) or DTE (Data Terminal Equipment) to TA (Terminal Adaptor) or DCE (Data Circuit Terminal Equipment). TE uses TA to send AT command to control the functions of MS (Mobile Station) and interact with network services. Through AT command, users can control phone calls, short messages, phonebooks, data services, supplementary services, and faxes etc.

## 2.1. AT Command Syntax

AT command must start with "AT" or "at", and end with a carriage return `<CR>`; the command is followed by a response with the structure "`<CR><LF>response<CR><LF>`". For readability, `<CR><LF>` are often omitted with only the response contents being displayed.

The AT command implemented by China Mobile IoT module includes 3GPP TS 27.005, 3GPP TS 27.007, ITU-TV.25ter standard command set and China Mobile IoT customized extended command set.

According to the syntax structure, AT command can be classified into three types: basic syntax, S parameter syntax and extended syntax.

### Basic Syntax

The command format of basic syntax is "AT`<x><n>`" or "AT&`<x><n>`", where "`<x>`" is the command, and "`<n>`" the command parameter.

For example, the command "ATE`<n>`". This command determines whether DCE needs to feed back the received characters to DTE according to the value of "`<n>`". "`<n>`" is optional, and the default value is used if the value is not included.

### S Parameter Syntax

The command format of S parameter syntax is "ATS`<n>=<m>`", where "`<n>`" is the index setting of S register, and "`<m>`" the setting value.

### Extended Syntax

This type of AT command has multiple operation modes.

**Table 2. AT Command and Response Type**

| Command | Response | Description | Type |
|---------|----------|-------------|------|
| AT+`<CMD>`=? | Return parameter list and parameter value range | | Test Command |
| AT+`<CMD>`? | Return the current value of the parameter | | Read Command |
| AT+`<CMD>`=`<p1>`[,`<p2>`[,`<p3>`[…]]] | Set the parameter value | | Set Command |
| AT+`<CMD>` | Perform specific operation | | Execute Command |

Please note:
- `<...>` It is the parameter that writes in the angle brackets, and the angle brackets are not included in the actual input;
- `[...]` It is the optional parameter that writes in the square brackets.

## 2.2. AT Command Response

**Table 3. AT Command Response Type**

| Response | Description |
|----------|-------------|
| ERROR | AT command format error or other errors |
| +CME ERROR: `<err>` or +CMS ERROR: `<err>` or +CIS ERROR: `<err>` | The extended error report (+CMEE) is enabled, where `<err>` represents the error code or detailed error information |
| OK | AT command executed successfully |

Note:
- In the AT command response result, there is a space after the colon ":" to separate the response header and the parameter list.
- The error response in the manual description is represented by +CME ERROR: `<err>` or +CMS ERROR: `<err>` or +CIS ERROR: `<err>`. The actual returns refer to the AT+CMEE command.

---

# 3. General Commands

This chapter describes in detail the AT commands and command formats related to version information, DTE configuration, etc.

## 3.1. ATE Set command echo mode

This setting determines whether the TA echoes characters received from TE during command state.

### ATE

| Syntax | Possible Returns |
|--------|------------------|
| **Set Command** | If succeed |
| ATE[`<value>`] | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

The DCE may echo characters received from the DTE during command state and online command state back to the DTE, depending on the setting of the E command. If so enabled, characters received from the DTE are echoed at the same rate, parity, and format as received. Echoing characters not recognized as valid in the command line or of incomplete or improperly-formed command line prefixes is manufacturer-specific.

**Defined Values**

`<value>` Integer type, default is 0.
- 0: Echo mode off
- 1: Echo mode on

**Note:** For ML551Z, the AT port of UART does not support echo mode.

## 3.2. ATS3 Set command line termination character

This parameter setting determines the character recognized by the TA to terminate an incoming command line. The TA also returns this character in output.

### ATS3

| Syntax | Possible Returns |
|--------|------------------|
| **Read Command** | If succeed |
| ATS3? | `<n>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| ATS3=[`<n>`] | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This S-parameter represents the decimal IA5 value of the character recognized by the DCE from the DTE to terminate an incoming command line. It is also generated by the DCE as part of the header, trailer, and terminator for result codes and information text.

**Defined Values**

`<n>` Integer type, default is 13.
- 0-13-127: Command line termination character.

> Note: Default 13 = CR; parameter `<n>` only 13 supported in product ML302S/ML307S/ML302A/ML305A/ML307A/ML305M/ML307G/ML307H/ML307M/ML307N/ML551Z; ML305U support the range of `<n>` from 0 to 31.

**Note:** ML307A-DL/ML305A-DL/ML307R/ML307C/ML307X does not support this command.

## 3.3. ATS4 Set response formatting character

This parameter setting determines the character generated by the TA for result code and information text.

### ATS4

| Syntax | Possible Returns |
|--------|------------------|
| **Read Command** | If succeed |
| ATS4? | `<n>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| ATS4=[`<n>`] | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This S-parameter represents the decimal IA5 value of the character generated by the DCE as part of the header, trailer, and terminator for result codes and information text.

**Defined Values**

`<n>` Integer type, default is 10.
- 0-10-127: Response formatting character.

> Note: Default 10 = LF; parameter `<n>` only 10 supported in product ML302S/ML307S/ML302A/ML305A/ML307A/ML305M/ML307G/ML307H/ML307M/ML307N/ML551Z; ML305U support the range of `<n>` from 0 to 31.

**Note:** ML307A-DL/ML305A-DL/ML307R/ML307C/ML307X does not support this command.

## 3.4. ATS5 Command line editing character

This parameter setting determines the character recognized by TA as a request to delete from the command line the immediately preceding character.

### ATS5

| Syntax | Possible Returns |
|--------|------------------|
| **Read Command** | If succeed |
| ATS5? | `<n>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| ATS5=[`<n>`] | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This S-parameter represents the decimal IA5 value of the character recognized by the DCE as a request to delete from the command line the immediately preceding character.

**Defined Values**

`<n>` Integer type, default is 8.
- 0-8-127: Command line editing character.

> Note: Default 8 = Backspace; parameter `<n>` only 8 supported in product ML302S/ML307S/ML302A/ML305A/ML307A/ML305M/ML307G/ML307H/ML307M/ML307N/ML551Z; ML305U support the range of `<n>` from 0 to 31.

**Note:** ML307A-DL/ML305A-DL/ML307R/ML307C/ML307X does not support this command.

## 3.5. +++ Escape from data mode

This command is used to transfer from in-call data mode to in-call command mode without disconnecting from the remote modem.

### +++

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| +++ | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

The escape sequence is used to transfer from in-call data mode to in-call command mode without disconnecting from the remote modem. After a pause, responds with OK. Register S2 can be used to alter the escape character from '+', the default, to any decimal value in the range 0 to 255.

**Reference** V.250

This command is not preceded by AT and does not require a line terminator.

**Note:** ML307A-DL/ML307R/ML307C/ML305A-DL/ML305M/ML307G/ML307H/ML307M/ML307N/ML307X does not support this command.

## 3.6. AT&F Set all current parameters to manufacturer defaults

TA sets all current parameters to the manufacturer defined profile.

### AT&F

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| AT&F[`<value>`] | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This command instructs the DCE to set all parameters to default values specified by the manufacturer, which may take into consideration hardware configuration switches and other manufacturer-defined criteria.

**Defined Values**

`<value>` Integer type.
- 0: Set all TA parameters to manufacturer defaults.

**Scope**

Channel Specific and Generic: each parameter may be Channel Specific or Generic (see command for individual parameter).

**Note:** ML302A/ML305A/ML307A/ML307R/ML307C/ML307G/ML307H/ML307X does not support this command.

## 3.7. ATV Set result code format mode

This parameter setting determines the contents of the header and trailer transmitted with result codes and information responses.

### ATV

| Syntax | Possible Returns |
|--------|------------------|
| **Set Command** | If succeed |
| ATV[`<value>`] | When `<value>`=0: 0 |
| | When `<value>`=1: OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

The setting of this parameter determines the contents of the header and trailer transmitted with result codes and information responses. It also determines whether result codes are transmitted in a numeric form or an alphabetic (or "verbose") form. The text portion of information responses is not affected by this setting.

**Defined Values**

`<value>` Integer type, default is 1.
- 0: Information response: `<text><CR><LF>`, Short result code format: `<numeric code><CR>`
- 1: Long result code format: `<CR><LF><verbose code><CR><LF>`, Information response: `<CR><LF><text><CR><LF>`

> Note: In product ML302S/ML307S/ML302A/ML305A/ML307A/ML307R/ML307C, default is 0; ML305M/ML307G/ML307H/ML307M/ML307N only support 1.

**Note:** ML307X/ML551Z does not support this command.

## 3.8. ATQ Set result code presentation mode

This parameter setting determines whether the TA transmits any result code to the TE. Information text transmitted in response is not affected by this setting.

### ATQ

| Syntax | Possible Returns |
|--------|------------------|
| **Set Command** | If succeed |
| ATQ[n] | If `<n>`=0: OK |
| | If `<n>`=1: (none) |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

The setting of this parameter determines whether or not the DCE transmits result codes to the DTE. When result codes are being suppressed, no portion of any intermediate, final, or unsolicited result code-header, result text, line terminator, or trailer is transmitted. Information text transmitted in response to commands is not affected by the setting of this parameter.

**Defined Values**

`<n>` Integer type, default is 0.
- 0: TA transmits result code.
- 1: Result codes are suppressed and not transmitted.

> Note: ML307G/ML307H only support 0.

**Note:** ML305M/ML307M/ML307N/ML551Z/ML307X does not support this command.

## 3.9. ATZ Set current parameters to user defined profile

TA sets all current parameters to the user defined profile.

### ATZ

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| ATZ[`<value>`] | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This command instructs the DCE to set all parameters to their factory defaults as specified by the manufacturer. This may include taking into consideration the settings of hardware configuration switches or non-volatile parameter storage (if implemented). If the DCE is connected to the line, it is disconnected from the line, terminating any call in progress. All of the functions of the command shall be completed before the DCE issues the result code. The DTE should not include additional commands on the same command line after the Z command because such commands may be ignored.

**Defined Values**

`<value>` Integer type.

Implementation of this command is mandatory. Interpretation of `<value>` is optional and manufacturer-specific.

**Note:** ML302A/ML305A/ML307A/ML307R/ML307C/ML305M/ML307M/ML307N/ML307G/ML307H/ML551Z/ML307X does not support this command.

## 3.10. ATX Set connect result code format and call monitoring

This parameter setting determines whether or not the TA detected the presence of dial tone and busy signal and whether or not TA transmits particular result codes.

### ATX

| Syntax | Possible Returns |
|--------|------------------|
| **Set Command** | If succeed |
| ATX[`<value>`] | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

The setting of this parameter determines whether or not the DCE transmits particular result codes to the DTE. It also controls whether or not the DCE verifies the presence of a dial tone when it first goes off-hook to begin dialling, and whether or not engaged tone (busy signal) detection is enabled. However, this setting has no effect on the operation of the W dial modifier, which always checks for a dial tone regardless of this setting, nor on the busy signal detection capability of the W and @ dial modifiers.

**Defined Values**

`<value>` Integer type, default is 4.
- 0: CONNECT `<text>` result code only returned, dial tone and busy detection are both disabled.
- 1: CONNECT result code only returned, dial tone and busy detection are both disabled.
- 2: CONNECT `<text>` result code returned, dial tone and busy detection are both enabled.
- 3: CONNECT `<text>` result code returned, dial tone detection is disabled, busy detection is enabled.
- 4: CONNECT `<text>` result code returned, dial tone detection is enabled, busy detection is disabled.

> Note: ML307G/ML307H only support 3.

**Note:** ML305M/ML307M/ML307N/ML551Z/ML307X does not support this command.

## 3.11. ATI Display product identification information

This command is used to query manufacturer information, product model and version information.

### ATI

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| ATI | `<manufacturer>` |
| | `<model>` |
| | `<Revision>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This command causes the DCE to transmit one or more lines of information text, determined by the manufacturer, followed by a final result code. `<value>` may optionally be used to select from among multiple types of identifying information, specified by the manufacturer. NOTE: The responses to this command may not be reliably used to determine the DCE manufacturer, revision level, feature set, or other information, and should not be relied upon for software operation. In particular, expecting a specific numeric response to an I0 command to indicate which other features and commands are implemented in a DCE dooms software to certain failure, since there are widespread differences in manufacturer implementation among devices that may, coincidentally, respond with identical values to this command. Software implementors should use commands with extreme caution, since the amount of data returned by particular implementations may vary widely from a few bytes to several thousand bytes or more, and should be prepared to encounter ERROR responses if the value is not recognized.

**Example**

```
ATI
CMCC
MN316
MN316-DBRS-MBRH0C00
OK
```

## 3.12. AT+GMI Request manufacturer identification

TA returns manufacturer identification text.

### AT+GMI

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| AT+GMI | `<manufacturer>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Execution command causes the TA to return one or more lines of information text `<manufacturer>`, determined by the MT manufacturer, which is intended to permit the user of the TA to identify the manufacturer of the MT to which it is connected to. Typically, the text will consist of a single line containing the name of the manufacturer, but manufacturers may choose to provide more information if desired.

**Defined Values**

`<manufacturer>` String type. Manufacturer identification

**Scope**

Channel specific (response output only on channel which entered the command).

## 3.13. AT+CGMI Request manufacturer identification

TA returns manufacturer identification text.

### AT+CGMI

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| AT+CGMI | `<manufacturer>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Execution command causes the TA to return one or more lines of information text `<manufacturer>`, determined by the MT manufacturer, which is intended to permit the user of the TA to identify the manufacturer of the MT to which it is connected to. Typically, the text will consist of a single line containing the name of the manufacturer, but manufacturers may choose to provide more information if desired.

**Defined Values**

`<manufacturer>` String type. Manufacturer identification

**Example**

```
AT+CGMI
CMCC
OK
```

## 3.14. AT+GMM Request model identification

TA returns product model identification text.

### AT+GMM

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| AT+GMM | `<model>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Execution command causes the TA to return one or more lines of information text `<model>`, determined by the MT manufacturer, which is intended to permit the user of the TA to identify the specific model of the MT to which it is connected to. Typically, the text will consist of a single line containing the name of the product, but manufacturers may choose to provide more information if desired.

**Defined Values**

`<model>` String type. Product model identification

**Scope**

Channel specific (response output only on channel which entered the command)

## 3.15. AT+CGMM Request model identification

TA returns product model identification text.

### AT+CGMM

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| AT+CGMM | `<model>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Execution command causes the TA to return one or more lines of information text `<model>`, determined by the MT manufacturer, which is intended to permit the user of the TA to identify the specific model of the MT to which it is connected to. Typically, the text will consist of a single line containing the name of the product, but manufacturers may choose to provide more information if desired.

**Defined Values**

`<model>` String type. Product model identification

## 3.16. AT+GMR Request revision identification

TA reports one or more lines of information text that permit the user to identify the version, revision level or data or other information of the device.

### AT+GMR

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed (Note: The keyword "Revision:" is not available to ML302A/ML305A/ML307R/ML307C/ML307A/ML307X) |
| AT+GMR | Revision:`<revision>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Execution command causes the TA to return one or more lines of information text `<revision>`, determined by the MT manufacturer, which is intended to permit the user of the TA to identify the version, revision level or date, or other pertinent information of the MT to which it is connected to. Typically, the text will consist of a single line containing the version of the product, but manufacturers may choose to provide more information if desired.

**Defined Values**

`<revision>` String type. Product software version identification.

**Scope**

Channel specific (response output only on channel which entered the command)

## 3.17. AT+CGMR Request revision identification

TA returns product software version identification text.

### AT+CGMR

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed (Note: The keyword "Revision:" is not available to ML302A/ML305A/ML307R/ML307C/ML307A/ML307X) |
| AT+CGMR | Revision:`<revision>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Execution command causes the TA to return one or more lines of information text `<revision>`, determined by the MT manufacturer, which is intended to permit the user of the TA to identify the version, revision level or date, or other pertinent information of the MT to which it is connected to. Typically, the text will consist of a single line containing the version of the product, but manufacturers may choose to provide more information if desired.

**Defined Values**

`<revision>` String type. Product software version identification

**Example**

```
AT+CGMR
MN316-DBRS-MBRH0C00
OK
```

## 3.18. AT+GSN Request product serial number identification

This command request TA serial number identification/IMEI number.

### AT+GSN

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | +GSN: (list of supported `<snt>`s) |
| AT+GSN=? | OK |
| **Execute Command** | If succeed |
| AT+GSN | `<sn>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| AT+GSN=`<snt>` | when `<snt>`=0 and command successful |
| | `<sn>` |
| | OK |
| | when `<snt>`=1 and command successful |
| | +GSN: `<imei>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Execution command causes the TA to return IMEI (International Mobile station Equipment Identity number) and related information to identify the MT that the TE is connected to. Test command returns values supported as a compound value. For a TA which does not support `<snt>`, only OK is returned.

**Defined Values**

`<snt>` Integer type, indicating serial number type that has been requested.
- 0: Returns `<sn>`
- 1: Returns `<imei>`

`<sn>` String type. The total number of characters, including line terminators, in the information text shall not exceed 2048 characters.

`<imei>` String type. International mobile equipment identity.

**Reference** V.250

**Note:** ML305M/ML307M/ML307N does not support this command.

## 3.19. AT+CGSN Request product serial number identification

This command request TA serial number identification | IMEI number.

### AT+CGSN

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | +CGSN: (list of supported `<snt>`s) |
| AT+CGSN=? | OK |
| **Execute Command** | If succeed |
| AT+CGSN | `<sn>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| AT+CGSN=`<snt>` | when `<snt>`=0 and command successful: `<sn>` OK |
| | when `<snt>`=1 and command successful: +CGSN: `<imei>` OK |
| | when `<snt>`=2 and command successful: +CGSN: `<imeisv>` OK |
| | when `<snt>`=3 and command successful: +CGSN: `<svn>` OK |
| | If fail: +CME ERROR: `<err>` |

**Description**

Execution command causes the TA to return IMEI (International Mobile station Equipment Identity number) and related information to identify the MT that the TE is connected to. Test command returns values supported as a compound value. For a TA which does not support `<snt>`, only OK is returned.

**Defined Values**

`<snt>` Integer type, indicating serial number type that has been requested.
- 0: Returns `<sn>`
- 1: Returns `<imei>`
- 2: Returns `<imeisv>`
- 3: Returns `<svn>`

> Note: ML302S/ML307S/ML302A/ML305A/ML307A/ML307R/ML307C/ML305U/ML551Z does not support parameters 2, 3.

`<sn>` String type, the total number of characters, including line terminators, in the information text shall not exceed 2048 characters.

`<imei>` String type, international mobile equipment identity.

`<imeisv>` String type, in decimal format indicating the IMEISV.

`<svn>` String type, in decimal format indicating the current SVN which is a part of IMEISV.

**Example**

```
AT+CGSN=1
+CGSN: 869975033574370
OK
```

## 3.20. AT+IPR Set fixed DTE rate

The set command parameter setting determines the data rate of the TA on the serial interface.

### AT+IPR

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | +IPR: (list of supported auto detectable `<rate>`s),(list of supported fixed only `<rate>`s) |
| AT+IPR=? | OK |
| **Read Command** | If succeed |
| AT+IPR? | +IPR: `<rate>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| AT+IPR=`<rate>` | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This numeric extended-format parameter specifies the data rate at which the DCE will accept commands, in addition to 1200 bit/s or 9600 bit/s. It may be used to select operation at rates at which the DCE is not capable of automatically detecting the data rate being used by the DTE. Specifying a value of 0 disables the function and allows operation only at rates automatically detectable by the DCE. The specified rate takes effect following the issuance of any result code(s) associated with the current command line. The `<rate>` specified does not apply in online data state if Direct mode of operation is selected.

(Please use the test command to query the support range of `<rate>`.)

**Defined Values**

`<rate>` Integer type, baud-rate per second.
- 0 (auto baud rate), 110, 300, 1200, 2400, 4800, 9600, 19200, 38400, 57600, 115200, 230400, 460800, 921600, etc.

> Note:
> - ML302S/ML307S/ML307G/ML307H: Default is 115200 and does not support auto baud rate.
> - ML305U/ML307X: Default is 115200.
> - ML551Z: Default is 921600.
> - ML305M/ML307M/ML307N: Default is 9600.
> - ML302A/ML305A/ML307A/ML307R/ML307C: Default is 0, and 115200 is recommended.
> - Please use the test command to query the support range of `<rate>`.

**Example**

```
AT+IPR=115200
OK
```

## 3.21. AT+CSCS Set TE character set

This command is used to set TE character set.

### AT+CSCS

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | +CSCS: (list of supported `<chset>`s) |
| AT+CSCS=? | OK |
| **Read Command** | If succeed |
| AT+CSCS? | +CSCS: `<chset>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| AT+CSCS=`<chset>` | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Write command informs DCE which character set `<chset>` is used by the TE. DCE is then able to convert character strings correctly between TE and ME character sets.

**Defined Values**

`<chset>` String type.
- GSM: GSM default alphabet
- HEX: Hexadecimal numbers in character strings
- IRA: International reference alphabet (ITU-T T.50)
- PCCP: PC character set Code Page
- PCDN: PC Danish/Norwegian character set
- UCS2: UCS2 alphabet
- 8859-1: ISO 8859 Latin (1) character set

> Note:
> - ML302S/ML307S only supports parameters GSM and IRA.
> - ML302A/ML305A/ML307A/ML307R/ML307C only supports parameter IRA.
> - ML305M/ML307M/ML307N/ML307X/ML551Z only supports parameters GSM, IRA, UCS2 and HEX, and default is GSM.
> - ML307G/ML307H only supports parameters GSM, IRA, UCS2 and HEX, and default is IRA.
> - ML305U only supports parameters GSM, IRA, UCS2, PCCP and HEX.

**Example**

```
AT+CSCS?
+CSCS: "IRA"
OK
```

**Note:**
- In ML302S/ML307S/ML302A/ML305A/ML307R/ML307C/ML307A, when parameter `<dcs>` of command AT+CSMP is set 4 or 8, Ignore the value of AT+CSCS, input or output a hex string similar to PDU mode. So only support characters '0'-'9' and 'A'-'F';
- In ML302S/ML307S/ML302A/ML305A/ML307R/ML307C/ML307A, when received short message is UCS2 code, Ignore the value of AT+CSCS, input or output a hex string similar to PDU mode. So only support characters '0'-'9' and 'A'-'F'.

---

# 4. Call Control Commands

This chapter describes in detail the AT commands and command formats related to Call control, etc.

**Note:** ML307R/ML307C does not support any call control commands.

## 4.1. ATS0 Automatic answer

This command is used to set automatic answer.

### ATS0

| Syntax | Possible Returns |
|--------|------------------|
| **Read Command** | If succeed |
| ATS0? | `<n>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| ATS0=`<n>` | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This S-parameter controls the automatic answering feature of the DCE. If set to 0, automatic answering is disabled. If set to a non-zero value, the DCE shall cause the DCE to answer when the incoming call ringing has occurred the number of times indicated by the value.

**Defined Values**

`<n>` Integer type, the auto answering times, range from 0~255.

**Remark**

If set to 0, auto answering is disabled. This command is specially used on data service in GPRS mode.

In ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML307H, If `<n>` is set too high, the calling party may hang up before the call is answered automatically; For VoLTE call, only support `<n>`=0; Test command not supported currently.

**Note:** ML302A-DCLM/ML302A-GCLM/ML305A-DC/ML305A-DL/ML307A-DCLN/ML307A-GCLN/ML305U/ML305M/ML307M/ML307N/ML307H/ML307G-DL/ML551Z/ML307X does not support the command.

The unit of `<n>` is second in ML302A/ML305A/ML307A/ML307G/ML307H.

## 4.2. ATA Answer incoming call

This command is used to answer an incoming call.

### ATA

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| ATA | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| | NO CARRIER |

**Description**

This command instructs the DCE to immediately connect to the line and start the answer sequence as specified for the underlying DCE. Any additional commands that appear after A on the same command line are ignored. NOTE: The behaviour of the A command may be modified if DTE control of V.8 or V.8 bis is enabled; refer to Annex A in this case.

**Remark**

This command should be used only when there is one call. When there are several calls, please use the AT+CHLD to answer a new call.

**Note:** ML302A-DCLM/ML302A-GCLM/ML305A-DC/ML305A-DL/ML307A-DCLN/ML307A-GCLN/ML305U/ML305M/ML307M/ML307N/ML307H/ML307G-DL/ML307X does not support the command.

## 4.3. ATD Mobile originated call to dial a number

This command is used to make an outgoing call.

### ATD

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| ATD`<number>` | When the call is in progress: OK |
| | and |
| | NO DAILTONE or BUSY |
| | Connection be released: NO ANSWER or NO CARRIER |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This command instructs the DCE to originate a call. This may include several steps, depending upon the DCE type, such as: connecting to the line (going off-hook), waiting for the network to indicate readiness to receive call addressing information (wait for dial tone), signalling call addressing information to the network (dialling the number), monitoring the line for call progress signals (e.g., busy), and instructing the underlying DCE to start the call origination procedure (modulation handshaking). All characters appearing on the same command line after the "D" are considered part of the call addressing information to be signalled to the network, or modifiers used to control the signalling process (collectively known as a "dial string"), up to a semicolon character (IA5 3/11) or the end of the command line. If the dial string is terminated by a semicolon, the DCE does not start the call origination procedure as defined for the underlying DCE, but instead returns to command state after completion of the signalling of call addressing information to the network. Any characters appearing in the dial string that the DCE does not recognize as a valid part of the call addressing information or as a valid modifier shall be ignored. This permits characters such as parentheses and hyphens to be included that are typically used in formatting of telephone numbers. NOTE 1: The behaviour of the D command may be modified if DTE control of V.8 or V.8 bis is enabled; refer to Annex A in this case.

**Defined Values**

`<Number>` Dialing digits, include 1,2,3,4,5,6,7,8,9,0,*,„,+,A,B,C,.

**Note:**
- ML302A-DCLM/ML302A-GCLM/ML305A-DC/ML305A-DL/ML307A-DCLN/ML307A-GCLN/ML305U/ML305M/ML307M/ML307N/ML307H/ML307G-DL/ML307X does not support the command；
- ML302A/ML305A/ML307A: Add a semicolon at the end if you are using a call instead of PPP. For example, "ATD10086;".

## 4.4. ATH Disconnect existing connection

Hang up all existing connected calls, including active, waiting and hold calls.

### ATH

| Syntax | Possible Returns |
|--------|------------------|
| **Execute Command** | If succeed |
| ATH | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This command instructs the DCE to disconnect from the line, terminating any call in progress. All of the functions of the command shall be completed before the DCE issues any result code. NOTE: When used with modem-on-hold procedures per V.92, the call may be terminated without disconnecting from the line. Other V.250 commands such as AT+PMHF may then be used to cause the PSTN to switch to another line for placing another outgoing call or accepting another incoming call.

**Unsolicited Result Codes**

When the link is established or ringing, the command will get OK. But for the establishing, the command will get error.

URC1:
```
CIEV: SOUNDER 0
CIEV: CALL 0
```

**Remark**

**Note:** ML302A-DCLM/ML302A-GCLM/ML305A-DC/ML305A-DL/ML307A-DCLN/ML307A-GCLN/ML305U/ML305M/ML307M/ML307N/ML307H/ML307G-DL/ML307X does not support the command.

## 4.5. AT+CHUP Hang up call

Hang up all existing connected calls.

### AT+CHUP

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | OK |
| AT+CHUP=? | |
| **Execute Command** | If succeed |
| AT+CHUP | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Hang up all existing connected calls, including active, waiting and hold calls.

**Remark**

This command implements the same behavior as ATH.

**Note:** ML302A-DCLM/ML302A-GCLM/ML305A-DC/ML305A-DL/ML307A-DCLN/ML307A-GCLN/ML307A-DL/ML305U/ML305M/ML307M/ML307N/ML307H/ML307G-DL/ML307X does not support the command.

## 4.6. AT+CEER Extended error report

This command is used to report extended error.

### AT+CEER

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | OK |
| AT+CEER=? | |
| **Execute Command** | If succeed |
| AT+CEER | +CEER: `<report>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This command causes the TA to return one or more lines of information text `<report>`, determined by the MT manufacturer, which should offer the user of the TA an extended report of the reason for:
- the failure in the last unsuccessful call setup (originating or answering) or in call modification;
- the last call release;
- the last unsuccessful GPRS attach or unsuccessful PDP context activation;
- the last GPRS detach or PDP context deactivation.

Typically, the text will consist of a single line containing the cause information given by GSM/UMTS network in textual format.

**Defined Values**

`<report>` Integer type, the total number of characters, including line terminators, in the information text shall not exceed 2041 characters. Text shall not contain the sequence 0`<CR>` or OK`<CR>`.

**Note:** ML302A-DCLM/ML302A-GCLM/ML305A-DC/ML305A-DL/ML307A-DCLN/ML307A-GCLN/ML307A-DL/ML305U/ML305M/ML307M/ML307N does not support this command.

## 4.7. AT+CRC Cellular result codes and ring

This command is to control whether the extended format of incoming call indication or GPRS network request for PDP context activation or notification for VBS/VGCS calls is used.

### AT+CRC

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | +CRC: (list of supported `<mode>`s) |
| AT+CRC=? | OK |
| **Read Command** | If succeed |
| AT+CRC? | +CRC: `<mode>` |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| AT+CRC=`<mode>` | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

This command is to control whether the extended format of incoming call indication or GPRS network request for PDP context activation or notification for VBS/VGCS calls is used. When enabled, an incoming call is indicated to the TE with unsolicited result code +CRING: `<type>` instead of the normal RING.

**Defined Values**

`<mode>` Integer type.
- 0: Disables extended format (default)
- 1: Enables extended format

**Note:** ML302A-DCLM/ML302A-GCLM/ML305A-DC/ML305A-DL/ML307A-DCLN/ML307A-GCLN/ML307A-DL/ML305U/ML305M/ML307M/ML307N/ML307H/ML307G/ML551Z/ML307X does not support the command.

---

# 5. Network Service Commands

This chapter describes in detail the AT commands and command formats related to Network service and configuration, etc.

## 5.1. AT+CREG Network registration

This command be used to query the register status.

### AT+CREG

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | +CREG: (list of supported `<n>`s) |
| AT+CREG=? | OK |
| **Read Command** | If succeed |
| AT+CREG? | +CREG: `<n>`,`<stat>`[,`<lac>`,`<ci>`,`<act>`] |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| AT+CREG=`<n>` | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Set command controls the presentation of an unsolicited result code +CREG: `<stat>` when `<n>`=1 and there is a change in the MT's circuit mode network registration status in GERAN/UTRAN/E-UTRAN, or unsolicited result code +CREG: `<stat>`[,[`<lac>`],[`<ci>`],[`<AcT>`]] when `<n>`=2 and there is a change of the network cell in GERAN/UTRAN/E-UTRAN. The parameters `<AcT>`, `<lac>` and `<ci>` are sent only if available. The value `<n>`=3 further extends the unsolicited result code with [,`<cause_type>`,`<reject_cause>`], when available, when the value of `<stat>` changes. NOTE 1: If the MT also supports one or more of the GPRS services, EPS services or 5G services, the +CGREG command and +CGREG: result codes, the +CEREG command and +CEREG: result codes and the +C5GREG command and +C5GREG: result codes apply to the registration status and location information for those services. Read command returns the status of result code presentation and an integer `<stat>` which shows whether the network has currently indicated the registration of the MT. Location information elements `<lac>`, `<ci>` and `<AcT>`, if available, are returned only when `<n>`=2 and MT is registered in the network. The parameters [,`<cause_type>`,`<reject_cause>`], if available, are returned when `<n>`=3. Refer subclause 9.2 for possible `<err>` values. Test command returns values supported as a compound value.

**Defined Values**

`<n>` Integer type.
- 0: Disable network registration unsolicited result code
- 1: Enable network registration unsolicited result code +CREG: `<stat>`
- 2: Enable network registration and location information unsolicited result code +CREG:`<stat>`[,`<lac>`,`<ci>`,`<act>`]
- 3: Enable network registration, location information and cause value information unsolicited result code +CREG: `<stat>`[,[`<lac>`],[`<ci>`],[`<AcT>`][,`<cause_type>`,`<reject_cause>`]]

> Note: ML551Z default `<n>`=1 and does not support parameter 3.

`<stat>` Integer type.
- 0: Not registered, MT is not currently searching a new operator to register to
- 1: Registered, home network
- 2: Not registered, but MT is currently searching a new operator to register to
- 3: Registration denied
- 4: Unknown
- 5: Registered, roaming
- 6: registered for "SMS only", home network (applicable only when `<AcT>` indicates E-UTRAN)
- 7: registered for "SMS only", roaming (applicable only when `<AcT>` indicates E-UTRAN)
- 8: attached for emergency bearer services only (see NOTE 2) (not applicable)
- 9: registered for "CSFB not preferred", home network (applicable only when `<AcT>` indicates E-UTRAN)

> NOTE 2: 3GPP TS 24.008 [8] and 3GPP TS 24.301 [83] specify the condition when the MT is considered as attached for emergency bearer services.
>
> Note: ML307X supports parameters 10, registered for "CSFB not preferred", roaming (applicable only when `<AcT>` indicates E-UTRAN).

`<lac>` String type, two-byte location area code (when `<AcT>` indicates value 0 to 6), or tracking area code (when `<AcT>` indicates value 7). In hexadecimal format (e.g. "00C3" equals 195 in decimal).

`<ci>` String type, two-byte cell ID in hexadecimal format.

`<act>` Integer type, access technology of serving cell.
- 0: GSM
- 1: GSM Compact
- 2: UTRAN
- 3: GSM w/GPRS
- 4: UTRAN w/HSDPA
- 5: UTRAN w/HSUPA
- 6: UTRAN w/HSDPA and HSUPA
- 7: E-UTRAN
- 8: EC-GSM-IoT (A/Gb mode)

> Note: ML302S/ML307S/ML302A/ML305A/ML307A/ML305U does not support parameters 1, 8. ML307G/ML307H does not support parameters 3, 4, 5, 6, 8. ML307X only supports parameters 7.

**Note:** ML302A-DCLM/ML302A-GCLM/ML305A-DC/ML305A-DL/ML307A-DCLN/ML307A-GCLN/ML307R/ML307C/ML305U/ML305M/ML307M/ML307N does not support this command.

## 5.2. AT+COPS Operator Selection

This command be used to select the operator.

### AT+COPS

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | +COPS: [list of supported (`<stat>`,long alphanumeric `<oper>`,short alphanumeric `<oper>`,numeric `<oper>`[,`<AcT>`[,`<SubAct>`]])s][„(list of supported `<mode>`s),(list of supported `<format>`s)] |
| AT+COPS=? | OK |
| **Read Command** | If succeed |
| AT+COPS? | +COPS: `<mode>`[,`<format>`,`<oper>`[,`<AcT>`[,`<SubAct>`]]] |
| | OK |
| | If fail |
| | +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| AT+COPS=`<mode>`[,`<format>`[,`<oper>`[,`<AcT>`[,`<SubAct>`]]]] | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Set command forces an attempt to select and register to the GSM/UMTS/EPS/5GS network operator using the SIM/USIM card installed in the currently selected card slot. `<mode>` is used to select whether the selection is done automatically by the MT or is forced by this command to operator `<oper>` (it shall be given in format `<format>`) to a certain access technology, indicated in `<AcT>`. If the selected operator is not available, no other operator shall be selected (except `<mode>`=4). If the selected access technology is not available, then the same operator shall be selected in other access technology. The selected operator name format shall apply to further read commands (+COPS?) also. `<mode>`=2 forces an attempt to deregister from the network. The selected mode affects to all further network registration (e.g. after `<mode>`=2, MT shall be unregistered until `<mode>`=0 or 1 is selected). This command should be abortable when registration/deregistration attempt is made. Read command returns the current mode, the currently selected operator and the current Access Technology. If no operator is selected, `<format>`, `<oper>` and `<AcT>` are omitted. Test command returns a set of five parameters, each representing an operator present in the network. A set consists of an integer indicating the availability of the operator `<stat>`, long and short alphanumeric format of the name of the operator, numeric format representation of the operator and access technology. Any of the formats may be unavailable and should then be an empty field. The list of operators shall be in order: home network, networks referenced in SIM or active application in the UICC (GSM or USIM) in the following order: HPLMN selector, User controlled PLMN selector, Operator controlled PLMN selector and PLMN selector (in the SIM or GSM application), and other networks.

*(Note: Full content of AT+COPS and subsequent sections continues in the original document)*

---

# 10. ME Error Codes Related Commands

This chapter describes in detail the error codes used in all commands in this manual.

## 10.1. AT+CMEE Error message format

This command is used to disable or enable the use of result code +CME ERROR: `<err>` or +CMS ERROR: `<err>` or +CIS ERROR: `<err>` as an indication of an error relating to the functionality of the ME.

### AT+CMEE

| Syntax | Possible Returns |
|--------|------------------|
| **Test Command** | +CMEE: (list of supported `<n>`s) |
| AT+CMEE=? | OK |
| **Read Command** | If succeed |
| AT+CMEE? | +CMEE: `<n>` |
| | OK |
| | If fail |
| | +CMS ERROR: `<err>` or +CME ERROR: `<err>` |
| **Set Command** | If succeed |
| AT+CMEE=`<n>` | OK |
| | If fail |
| | +CME ERROR: `<err>` |

**Description**

Set command disables or enables the use of final result code +CME ERROR: `<err>` or +CMS ERROR: `<err>` or +CIS ERROR: `<err>` as an indication of an error relating to the functionality of the MT. When enabled, MT related errors cause +CME ERROR: `<err>` or +CMS ERROR: `<err>` or +CIS ERROR: `<err>` final result code instead of the regular ERROR final result code. ERROR is returned normally when error is related to syntax, invalid parameters, or TA functionality. Read command returns the current setting of `<n>`. Test command returns values supported as a compound value.

**Defined Values**

`<n>` Integer type. The default value is 1.
- 0: Disable result code
- 1: Enable result code and use numeric values
- 2: Enable result code and use verbose values

> Note: ML302S/ML307S only support parameter 1; ML307G/ML307H/ML307X does not support parameter 2.

## 10.2. +CME ERROR ME Error code reporting

Code of CME ERROR Meaning.

### General errors

**Table 5. General errors**

| Code | Description |
|------|-------------|
| 0 | phone failure |
| 1 | no connection to phone |
| 2 | phone-adaptor link reserved |
| 3 | operation not allowed |
| 4 | operation not supported |
| 5 | PH-SIM PIN required |
| 6 | PH-FSIM PIN required |
| 7 | PH-FSIM PUK required |
| 10 | SIM not inserted (See NOTE 1) |
| 11 | SIM PIN required |
| 12 | SIM PUK required |
| 13 | SIM failure (See NOTE 1) |
| 14 | SIM busy (See NOTE 1) |
| 15 | SIM wrong (See NOTE 1) |
| 16 | incorrect password |
| 17 | SIM PIN2 required |
| 18 | SIM PUK2 required |
| 20 | memory full |
| 21 | invalid index |
| 22 | not found |
| 23 | memory failure |
| 24 | text string too long |
| 25 | invalid characters in text string |
| 26 | dial string too long |
| 27 | invalid characters in dial string |
| 30 | network not allowed - emergency calls only |
| 31 | no network service |
| 32 | network timeout |
| 40 | network personalization PIN required |
| 41 | network personalization PUK required |
| 42 | network subset personalization PIN required |
| 43 | network subset personalization PUK required |
| 44 | service provider personalization PIN required |
| 45 | service provider personalization PUK required |
| 46 | corporate personalization PIN required |
| 47 | corporate personalization PUK required |
| 48 | hidden key required (See NOTE 2) |
| 49 | EAP method not supported |
| 50 | Incorrect parameters |
| 51 | command implemented but currently disabled |
| 52 | command aborted by user |
| 53 | not attached to network due to MT functionality restrictions |
| 54 | modem not allowed - MT restricted to emergency calls only |
| 55 | fixed dial number only allowed - called number is not a fixed dial number (refer 3GPP TS 22.101 [147]) |
| 56 | temporarily out of service due to other MT usage |
| 57 | language/alphabet not supported |
| 58 | unexpected data value |
| 59 | system failure |
| 60 | data missing |
| 61 | call barred |
| 62 | message waiting indication subscription failure |
| 63 | operation not allowed because of MT functionality restrictions |
| 100 | unknown |

NOTE 1: This error code is also applicable to UICC.

NOTE 2: This key is required when accessing hidden phonebook entries.

### Errors related to a failure to perform an attach

**Table 6. Errors for CS, GPRS and UMTS**

| Code | Description |
|------|-------------|
| 102 | IMSI unknown in HLR (See NOTE 2) |
| 103 | Illegal MS |
| 104 | IMSI unknown in VLR (See NOTE 2) |
| 105 | IMEI not accepted (See NOTE 2) |
| 106 | Illegal ME |
| 107 | GPRS services not allowed |
| 108 | GPRS services and non-GPRS services not allowed |
| 109 | MS identity cannot be derived by the network (See NOTE 2) |
| 110 | Implicitly detached (See NOTE 2) |
| 111 | PLMN not allowed |
| 112 | Location area not allowed |
| 113 | Roaming not allowed in this tracking area |
| 114 | GPRS services not allowed in this PLMN |
| 115 | No suitable cells in tracking area |
| 116 | MSC temporarily not reachable (See NOTE 2) |
| 117 | Network failure (See NOTE 2) |
| 122 | Congestion |
| 125 | Not authorized for this CSG |
| 132 | Service option not supported (See NOTE 2) |
| 133 | Requested service option not subscribed (See NOTE 2) |
| 134 | Service option temporarily out of order (See NOTE 2) |
| 138 | Call cannot be identified (See NOTE 2) |
| 148 | Unspecified GPRS error (See NOTE 2) |
| 150 | Invalid mobile class |
| 172 | Semantically incorrect message |
| 173 | Invalid mandatory information |
| 174 | Message type non-existent or not implemented |
| 175 | Conditional IE error |
| 176 | Protocol error, unspecified |
| 183 | SMS provided via GPRS in this routing area (See NOTE 2) |
| 185 | No PDP context activated (See NOTE 2) |
| 186 | Recovery on timer expiry (See NOTE 2) |
| 187 | Message type not compatible with protocol state (See NOTE 2) |
| 208 | Message not compatible with protocol state |
| 209 | Information element non-existent or not implemented (See NOTE 2) |

NOTE 1: Values in parentheses are 3GPP TS 24.008 [8] cause codes.

NOTE 2: This error code was given a numeric value in 3GPP Rel-15, but was introduced in an earlier release.

## 10.3. +CMS ERROR ME Error code reporting

Code of CMS ERROR Meaning.

### General errors

| Code | Description |
|------|-------------|
| 1 | unassigned (unallocated) number |
| 8 | operator determined barring |
| 21 | Short message transfer rejected |
| 27 | Destination out of service |
| 28 | Unidentified subscriber |
| 29 | Facility rejected |
| 30 | Unknown subscriber |
| 38 | Network out of order |
| 41 | Temporary failure |
| 42 | Congestion |
| 47 | Resources unavailable, unspecified |
| 50 | Requested facility not subscribed |
| 69 | Requested facility not implemented |
| 81 | Invalid short message transfer reference value |
| 95 | Invalid message, unspecified |
| 96 | Invalid mandatory information |
| 97 | Message type non-existent or not implemented |
| 98 | Message not compatible with short message protocol state |
| 99 | Information element non-existent or not implemented |
| 111 | Protocol error, unspecified |
| 127 | Interworking, unspecified |
| 300 | ME failure |
| 301 | SMS ME reserved |
| 302 | operation not allowed |
| 303 | operation not supported |
| 304 | invalid PDU mode parameter |
| 305 | invalid text mode parameter |
| 310 | SIM not inserted |
| 311 | SIM pin necessary |
| 312 | PH SIM pin necessary |
| 313 | SIM failure |
| 314 | SIM busy |
| 315 | SIM wrong |
| 316 | SIM PUK required |
| 317 | SIM PIN2 required |
| 318 | SIM PUK2 required |
| 320 | memory failure |
| 321 | invalid memory index |
| 322 | memory full |
| 330 | SMSC address unknown |
| 331 | no network |
| 332 | network timeout |
| 340 | no +CNMA acknowledgment expected |
| 500 | Unknown |
| 512 | SIM not ready |
| 513 | unread records on SIM |
| 515 | PS busy |
| 516 | Couldn't read SMS parameters from SIM |
| 517 | SM BL not ready |
| 518 | invalid parameter |
| 519 | ME temporary not available |
| 528 | Invalid (non-hex) chars in PDU |
| 529 | Incorrect PDU length |
| 530 | Invalid MTI |
