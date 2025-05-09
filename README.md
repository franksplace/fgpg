# fgpg

Bash script to help manage GnuGPG keys across systems

## Usage

```
Usage: fgpg [global option] command [command option]
Options:
  -d,--debug      : Enable debug logging
  -v,--verbose    : Enable verbose logging
  -s,--short      : Utilize short keyid format
  -l,--long       : Utilize long keyid format

Commands:
  list (host)                                   : List keys on host (if host not given localhost used)
  update-default-key <key>                      : Update default key in gpg.conf
  cleanup                                       : Cleanup gpg (remove expired keys)
  sync-backup <key file> <otrust file> <host>   : Utilizing backup files to import to remote key store"
  sync <key> <otrust> <host>                    : On the fly sync of key to host (*NOT DONE*)
  backup <key>                                  : Backup key and store info in <out>
  import <key file>                             : Import <key>
  import-otrust <otrust file>                   : Import ownership trust"
  scrub <key> (host)                            : Scrub <key> from local store or from (host)"
  remote-version <host> ..                      : Determine remote gpg version (ideally 2.4.x for Auth keys)"
```
