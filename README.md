# Vacation Sieve
## Document
- [rfc5230-vacation](https://github.com/dovecot/pigeonhole/blob/87ea4d74a051399222b6e0ca0e6f59700c89f923/doc/rfc/vacation.rfc5230.txt)
- [rfc6131-vacation-second](https://github.com/dovecot/pigeonhole/blob/87ea4d74a051399222b6e0ca0e6f59700c89f923/doc/rfc/vacation-seconds.rfc6131.txt)
- [Dovecot Vacation Sieve](https://doc.dovecot.org/configuration_manual/sieve/extensions/vacation/)
## 90-SIEVE.conf


`sieve_extensions = +vacation-seconds `
#### Enable extension
`sieve_vacation_min_period = 0 `
##### Minium period send vacation response per sender address `0` is no limit 

`sieve_vacation_default_period = 1d`
##### Default period send vacation response per sender address 

`sieve_vacation_max_period = 2d`
##### Max period send vacation response per sender address 

## Script Sieve
```
nano /sieve/vacationtest.sieve
```

```
require ["vacation-seconds"];
    vacation :seconds 2
			 :subject "Auto reply"
			#:addresses [""]
             " I'am on vacation ";

```
- `second 2` period send vacation
- `subject` subject of vacation mail
- `address` email list applies vacation extension but it doesn't work. When I used it it applied for all mail
- `"I'am on vacation"` Message for sender

```
sievec /sieve/vacationtest.sieve
```
```
nano /etc/dovecot/sieve/before.sieve
```
```
require ["enotify", "fileinto", "variables", "mailbox", "envelope", "copy", "body", "regex", "imap4flags","duplicate","include"];
include :global "vacationtest";
```
```
sievec /etc/dovecot/sieve/before.sieve
```
## Restart dovecot 
```
systemctl restart dovecot
```
## Test 
- Send mail from outsite to ah@dinhha.online 
- Check log
```
tail -f /var/log/mail.log | grep vacation
```
- Result OK
```

root@mail:~# tail -f /var/log/mail.log | grep vacation
May 23 02:35:22 mail dovecot: lmtp(ah@dinhha.online)<1170861><Kye7IfrDa2St3REA8GMZgg>: sieve: msgid=<3c7c369855757d5801ef7781db49480e@anthanh264.site>: sent vacation response to <test2@anthanh264.site>
May 23 02:35:45 mail dovecot: lmtp(ah@dinhha.online)<1170861><EA22ExHEa2St3REA8GMZgg>: sieve: msgid=<9f76806ec3618b31f4d1aab71bc28cd6@anthanh264.site>: sent vacation response to <test2@anthanh264.site>


```
