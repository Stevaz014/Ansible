# Playbook Proxmox VE

## `update-nodes.yml`

Aggiorna i nodi Proxmox VE in modo sicuro, non interattivo e un nodo alla volta (`serial: 1`).

### Cosa fa

1. Verifica che l'host di destinazione sia effettivamente un nodo Proxmox VE (`pveversion`).
2. Esegue `apt update` + `apt dist-upgrade` (il metodo ufficiale richiesto da Proxmox VE, invece di `apt upgrade`) con `DEBIAN_FRONTEND=noninteractive` e mantenendo i file di configurazione esistenti (`force-confdef,force-confold`).
3. Esegue `autoremove` e `autoclean` (disattivabili via variabile).
4. Controlla se serve un riavvio verificando sia `/var/run/reboot-required` sia l'eventuale installazione di un kernel Proxmox più recente rispetto a quello in uso (`uname -r`).
5. Di default **non riavvia automaticamente** (`proxmox_auto_reboot: false`), ma segnala chiaramente quali nodi richiedono reboot. Se imposti `proxmox_auto_reboot: true`, riavvia un nodo alla volta e verifica che `pvedaemon` e `pveproxy` siano ripartiti prima di passare al nodo successivo.

### Variabili disponibili

| Variabile | Default | Descrizione |
| :--- | :--- | :--- |
| `proxmox_target_hosts` | `proxmox` | Gruppo/host dell'inventory su cui girare |
| `proxmox_serial` | `1` | Numero di nodi aggiornati in parallelo (`1` consigliato nei cluster) |
| `proxmox_auto_reboot` | `false` | Se `true`, riavvia automaticamente i nodi quando necessario |
| `proxmox_autoremove` | `true` | Rimuove i pacchetti non più necessari |
| `proxmox_autoclean` | `true` | Pulisce la cache APT dei pacchetti obsoleti |
| `proxmox_reboot_timeout` | `600` | Timeout in secondi in attesa del ritorno online dopo il reboot |

### Configurazione in Semaphore UI

1. **Key Store:** crea una chiave SSH (o login `root` / utente con `sudo`) per i tuoi nodi Proxmox.
2. **Inventory:** aggiungi i nodi nel gruppo `[proxmox]` (puoi prendere spunto da `inventory/hosts.example.yml`):
   ```yaml
   all:
     children:
       proxmox:
         vars:
           ansible_user: root
         hosts:
           pve-01:
             ansible_host: 192.168.x.x
   ```
3. **Environment (opzionale):** lascia `{}` oppure imposta Extra Variables JSON, ad esempio:
   ```json
   {
     "proxmox_auto_reboot": false
   }
   ```
4. **Task Template:**
   - **Name:** `Aggiorna nodi Proxmox`
   - **Playbook Filename:** `playbooks/proxmox/update-nodes.yml`
   - **Repository:** `Stevaz014/Ansible`
   - **Inventory:** il tuo inventory Proxmox
   - *(Consigliato)* Aggiungi una **Survey Variable** di nome `proxmox_auto_reboot` (tipo `Enum`: `false` / `true`) per decidere al volo se riavviare o meno quando lanci il task manualmente.
5. **Schedule (opzionale):** puoi pianificare il template (es. ogni domenica alle 04:00 con `0 4 * * 0`).

### Esecuzione manuale da terminale

```bash
# Aggiornamento senza riavvio automatico
ansible-playbook -i inventory/hosts.yml playbooks/proxmox/update-nodes.yml

# Aggiornamento con riavvio automatico se necessario
ansible-playbook -i inventory/hosts.yml playbooks/proxmox/update-nodes.yml -e proxmox_auto_reboot=true
```
