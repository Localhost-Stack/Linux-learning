#Ansible - Introduktion


##Kärnkoncept
-Control node = maskinen som kör ansible-kommandon FRÅN (Min Arch-maskin)
-Managed node = maskinen som blir styrd (min Fedora-laptop)
-Agentless: ingen agent/mjukvara behövs på managed nodes, bara SSH-åtkomst + python på target maskinen


##Felsökning: SSH-nyckel med passphrase kan inte Ansible logga in,
eftersom den inte kan visa en interaktiv prompt (fel:ssh_askpass).

Lösning: ladda nyckeln i ssh-agenten en gång per terminal-session:
eval "$("ssh-agent -s")
ssh-add ~/.ssh/id_ed25519

Efter detta hålls nyckeln upplåst i minnet per session och både manuell SSH
och Ansible kan använda den utan att fråga om passphrase igen.


##Resultat
ansible fedora_laptop -i inventory.ini -m ping
-> Success, "ping": "pong"

Detta bekräftar hela kjedan fungerar:
controle node (ARCH) -> SSH-nyckel -> managed node (Fedora) -> Python


##Playbooks
En playbook beskriver ÖNSKAT TILLSTÅND i YAML, inte steg för steg kommandon.
Ansible räknar ut vad som krävs för att uppnå det tillståndet (idempotens)

Exempel: install-podman.yml
---
- name: Install Podman on Fedora Laptop
  hosts: fedora_laptop
  become: true
  tasks:
    - name: Install podman package
      dnf:
        name: podman
        state: present



---
Nyckelord:
- hosts: vilken grupp från inventory.ini som ska köras mot
- become: true = kör med sudo-rättigheter
- dnf-modulen = pakethanterar för Fedora/RHEL (apt på Debian/Ubuntu)
- state: present = "detta ska finnas intesallerat" (absent = ska inte finnas, latest)


## Köra en playbook
ansible-playbok -i inventory.ini install-podman.yml --ask-become-pass
--syntax-check = validera YAML-syntax INNAN man kör påriktigt


## Resultat
changed=1, failed=0 -> Podman installerades korrekt på Fedora-laptopen
Kör man samma playbook igen: changed=0, eftersom paketet redan finns (idempotens)



##Felsökning: Container + UFW + portmappning (djupyk)
Symptom: curl fastnat på "Trying...." även efter att en UFW INPUT-regel
för 8080/tcp lades till. Container kör, lyssnar korrekt (ss -tlnp visar 0.0.0.0:8080)

Root cause: Podmans portmappning (8080:80) gör DNAT - traffik omdirigeras från värdens 8080
till containers interna IP (tex 10.88.0.2:80).
Det räknas som Forwards traffik, inte direkt input till värden.

UFW:s "ufw_allow 8080/tcp" lägger bara till en regel i INPUT kjedan,
Forwards-kedjans policy är "drop" som standard - och Podmans virituella interface (podman0)
hade ingen "tillåtande" regel där.

Fix (Vi behövde BÅDA riktningarna):
sudo ufw route allow in on podman 0
sudo ufw route allow out on podman0

## Lärdom
Container-Nätverk med brandvägg har två seperata lager att tänka på - INPUT (traffik till värden) och Forward
Traffik som vidarebefodras vidare, tex via DNAT till en container). Att bara öppna porten i input
räcker inte om trafiken faktiskt vidarebefodras.


## Ansible Vault
Krypterar känsliga värden (lösenord, API-nycklar) så de kan sparas säkert i git, istället för klartext eller manuell inmatning varje gång.

##Skapa en krypterade fil:
ansible-vault create secrets.yml
-> ber om Vault-lösenfras (nyckeln som låser upp filen inte samma som lösenordet man lagarar inuti)

Innehåll (VIKTIGT: melanslag efter kolon, annars ogiltigt YAML):
ansible_become_pass: (lösenord)

Visa innehåll (dekrypterar tillfälligt i terminalen):
ansible-vault view secret.yml

Redigera:
ansible-vault edit secrets.yml


##Köra playbook med vailt-fil:
ansible-playbook -i invento.ini install-podman.-yml --extras-vars "@secrets.yml" --ask-vault-pass

secrets.yml är Säker att committa till git eftersom att den är krypterad på disk det är hela poängen med Vault
(till skillnad från tex .gitignore som bara döljer filen lokalt)

## Felsökning
"Could not be made into a dictionary" = ogi YAML-syntax i vault-filen, oftast sakande kolon+mellanslag mellan nyckel och värde
