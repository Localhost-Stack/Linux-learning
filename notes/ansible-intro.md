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

