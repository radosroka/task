# logging-daemon
1. časť
- jednoduchý program ktorý vytvorí(bude vlastniť) a bude čítať /dev/log socket ako {read/write} pre všetkých
- parametrami bude zoznam súborov do ktorých bude zapisovať
- všetky logy ktore prečíta zo socketu vypíše taktiež na STDOUT
- bude bežať pod rootom

2. časť
- detekcia opakujúcich sa správ, stačí na úrovni toho že pri ukončení programu napr SIGINT
- to vypíše najviac frekventovanú správu a koľko krát sa objavila

Rozšírenia
- cmake project
- -f option pre fork

Dobré vedieť
- nazačiatok je dobré sa pozrieť ako funguje rsyslog
```
# dnf install rsyslog
# systemctl start rsyslog
```
- logovací daemon (rsyslog) beží (možné overiť systemctl status...)
- rsyslog číta z /dev/log a píše do /var/log/messages
- je to možné otestovať pomocou logger command
```
$ logger <message>
```
- a následne sa pozrieť do /var/log/messages
- https://github.com/rsyslog/rsyslog/blob/master/plugins/imuxsock/imuxsock.c
- createLogSocket <-- funkcia pre inšpiráciu

- pred vytváraním vlastného socketu vašim programom je potrebné
- existujúci /dev/log zmazať pomocou unlink
```
# unlink /dev/log
```

- možno bude treba vypnúť journald a rsyslogd
```
# systemctl stop rsyslog
# systemctl stop systemd-journald
```

* príklad
```
~/Work/tasks/c-task(master*) » sudo ./logging-daemon /tmp/output1.log /tmp/output2.log /tmp/output3.log
[sudo] password for rsroka:
<86>May 14 19:02:42 sudo: pam_unix(sudo-i:session): session closed for user root
<13>May 14 19:02:59 rsroka: some message
<13>May 14 19:03:07 rsroka: some message
<13>May 14 19:03:07 rsroka: some message
<13>May 14 19:03:08 rsroka: some message
<13>May 14 19:03:14 rsroka: some other message
<13>May 14 19:03:15 rsroka: some other message
4 --> rsroka: some message
The END!

```
- medzitým
```
~ » logger some message
~ » logger some message
~ » logger some message
~ » logger some message
~ » logger some other message
~ » logger some other message
```


# C++ buffer
- Vytvor program v C++
- bude čítať zo STDIN viacero typov reťazcov

exit

- po prečítaní program korektne zomrie

číslo:číslo

- napr 10:1
- prvá časť reťazca je hodnota a druhá je priorita, separátor je znak :
- ulož tento reťazec do buffera, tak že najvyššia priorita bude v bufferi ako prvá a najnižšia posledná
- ak sú rovnaké tak budú v takom poradí ako prišli

print

- vypíše a vymaže buffer

get

- vypíše prvý prvok buffera
