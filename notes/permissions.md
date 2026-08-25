## Linux Permissions
Varje fil/mapp har tre behörighetsnivåer:

- **Owner (ägare)** - personen som skapade filen
- **Group (grupp)** - en grupp användare
- **Others (övriga)** - alla andra


Och tre rättigher per nivå:
- **r** (read) - läsa
- **w** (write) - skriva/läsa
- **x** (execute) - köra som program. eller gå in i en mapp


## Läsa rättigheter
'ls -l' visar t.ex 'rwxr-xr--'
- Position 1: filtyp ('-' = fil. 'd' = mapp)
- Position 2-4: owner (rwx)
- Position 5-7: group (r-x)
- Position 8-10: others (r--)

## Ändra rättigheter

- 'chmod' - ändra behörigheter, t.ex. 'chmod 755 fil.sh'
- 'chown' - byta ägare, t.ex. 'chown user:group fil.sh'

### Siffersystem (oktalt)
r=4, w=2, x=1 > summera per nivå
- 7 = rwx
- 5 = r-x
- 4 = r--

