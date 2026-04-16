# Beatrice.py

To bypass detection methods like YARA rules that look for certain bytes and memory scanners Beatrice.py patches machine code in binaries with alternative x64 assembly opcodes of the same size. This tool was also designed to modify machine code of executables or complex binaries that contain strings and other data, it will strictly match machine code to avoid breaking binaries.

![image](https://i.imgur.com/BTunLsS.gif)

# Usage

```
python3 beatrice.py           
                                                                                                  
@@@@@@@   @@@@@@@@   @@@@@@   @@@@@@@  @@@@@@@   @@@   @@@@@@@  @@@@@@@@       @@@@@@@   @@@ @@@  
@@@@@@@@  @@@@@@@@  @@@@@@@@  @@@@@@@  @@@@@@@@  @@@  @@@@@@@@  @@@@@@@@       @@@@@@@@  @@@ @@@  
@@!  @@@  @@!       @@!  @@@    @@!    @@!  @@@  @@!  !@@       @@!            @@!  @@@  @@! !@@  
!@   @!@  !@!       !@!  @!@    !@!    !@!  @!@  !@!  !@!       !@!            !@!  @!@  !@! @!!  
@!@!@!@   @!!!:!    @!@!@!@!    @!!    @!@!!@!   !!@  !@!       @!!!:!         @!@@!@!    !@!@!   
!!!@!!!!  !!!!!:    !!!@!!!!    !!!    !!@!@!    !!!  !!!       !!!!!:         !!@!!!      @!!!   
!!:  !!!  !!:       !!:  !!!    !!:    !!: :!!   !!:  :!!       !!:            !!:         !!:    
:!:  !:!  :!:       :!:  !:!    :!:    :!:  !:!  :!:  :!:       :!:       :!:  :!:         :!:    
 :: ::::   :: ::::  ::   :::     ::    ::   :::   ::   ::: :::   :: ::::  :::   ::          ::    
:: : ::   : :: ::    :   : :     :      :   : :  :     :: :: :  : :: ::   :::   :           :     
                                                                                                  
Usage: beatrice.py <binary>
-h for usage and flags
-v for Verbose mode
-s for Safer mode, normal mode already mostly safe but still may break some binaries
```

# What this tool does

### It will:

- Generate patterns of simple assembly x64 instructions and their alternative instructions, turn them into machine code and patch the machine code if it matches.
- Build different lists of assembly instructions that contain immediate values and other instructions that can’t be easily turned into patterns and apply appropriate changes to them.
- Apply alternative ways to encode instructions whenever possible.
- Create an identical binary functionality wise but with the above patches applied that will help evade YARA rules and some Antivirus solutions.

### It will NOT:

- Be a one size fits all solution.
- Modify strings, so detection is still possible based on strings.
- Modify imports or calls to Windows API functions that can be detected by some AVs and EDRs.
- Completely evade behavior based detection. While this modifies the machine code enough to sometimes trick behavior based detection it won’t change the core functionality leading to still possibilities for detection.

While this tool can make some binaries evade AVs on its own, it is best used combined with other evasion techniques (Examples: Modify shellcode to be used with a loader, help with custom or modified tooling)

# Tests against Windows Defender and Elastic YARA rules ( April 2026 )

### Executables (.exe)

**Mimikatz with obfuscated strings** → [Evades Defender, see my Medium article.](https://medium.com/@luisgerardomoret_69654/modifying-mimikatz-to-evade-defender-2026-dc701000289d)

**Metasploit** stageless reverse shell tcp → Inconsistent results against Defender sometimes it evades Defender sometimes it’s detected, evades Elastic YARA rules.

**Havoc** payload with default profile and no modification  → Evades Defender (shown on gif above), detected by Elastic YARA due to default hashing and default profile.

**Sliver** payload using its default obfuscation → Detected by Defender due to using Garble for obfuscation, evades Elastic YARA Rules.

**Sliver** with skip-symbols option → Detected by both Defender and Elastic YARA due to strings.

**AdaptixC2** payload with IAT Hiding → Already evasive against Defender but tool may help if Microsoft creates more signatures, evades Elastic YARA rules.

CobaltStrike stageless payload → Bypassed detection bytes but still detected by few strings.

### Raw binaries / Shellcode (.bin)

Using [DefenderYara](https://github.com/roadwy/DefenderYara/tree/main/Trojan/Win64), [defender2yara](https://github.com/t-tani/defender2yara/tree/yara-rules/Win64/Trojan) and [Elastic rules](https://github.com/elastic/protections-artifacts) to test.

**Metasploit** stageless reverse shell tcp → Evades YARA rules.

**Havoc** payload with custom profile and no modification  → Evades Defender YARA rules, detected by Elastic YARA due to default hashing and default profile.

**Sliver** payload using its default obfuscation → Evades YARA rules.

**Sliver** with skip-symbols option → Detected by both Defender and Elastic YARA due to strings.

**AdaptixC2** payload with IAT Hiding → Evades YARA rules.

CobaltStrike stageless payload → Bypassed detection bytes but still detected by few strings.

**Donut** shellcode  → Evades YARA rules.

Notes on Havoc: Ran on Docker to solve compiler compatibility issues so payloads compile as they originally do before using the tool.

Notes on CobaltStrike: I don’t own license, I have access to a course that provides labs and includes CobaltStrike.

# Known Issues

Golang compiled binaries that use Garble for obfuscation may break. 

Despite working most of the time some binaries may still break, that’s why safe mode was added an option to just use the most basic features.

# Support Me

https://buymeacoffee.com/lainkusanagi
