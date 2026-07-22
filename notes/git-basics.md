#Git Basics

##Dagens lärdomar
-mkdir -p skapar parent + child i ett svep
-git init skapar .git som spårar historik
- SSH-nyckel: privat stannar lokalt, publikt delas


## Branchingin
En branch är en pararell tidslinje i git historiken. Man kan expremintera fritt på en branch
utan att påverka master.

Skapa + växla till en ny branch i ett steg:
git checkout -b <branchnamn>

Se vilken branch man står på (stjärna = aktuell):
git branch

Växla mellan branches:
git checkout <branchnamn>

Merga en branch tillbaka till master:
1. git checkout master    (gå tillbaka till master först)
2. git merge <branchnamn> (för in ändringarna)

Om master inte fått nya commits sen branchen skapades blir det en FAST-FORWARD merge - git
flyttar bara master-pekaren framåt inget att "slå ihop" i konflikbemärkelse.

Ta bort en bran när den är klar/mergad (säkert, git varnar annars):
git branch -d <branchnamn>

## Varför använda branches?
Testa riskabla ändringar utan att förstöra en fungerande main/master.
om något går fel: kasta branchen, och master är orörd
Om det funkar: merga in det i master.


## Praktiskt exempel jag körde
git checkout -b add-postgres
[gjorde en ändring, commitade]
git checkout master
git merge add-postgres ->FAST-FORWARD, ingen konflikt
git branch -d add-postgres
git push
