# Ansible — Homelab & Semaphore UI

Raccolta di playbook Ansible e script di automazione per il mio homelab, eseguiti tramite [Semaphore UI](https://semaphoreui.com/).

Il repository è pubblico per condividere gli script e raccogliere miglioramenti o correzioni: **issue e Pull Request sono sempre benvenute!**

## Struttura del repository

```text
Ansible/
├── ansible.cfg                 # Configurazione base di Ansible
├── collections/
│   └── requirements.yml        # Collection Ansible installate automaticamente da Semaphore
├── inventory/
│   └── hosts.example.yml       # Esempio di inventory (nessun dato reale/sensibile)
├── playbooks/
│   ├── proxmox/                # Manutenzione e aggiornamento nodi Proxmox VE
│   ├── system/                 # Manutenzione sistema, aggiornamenti, configurazioni base
│   ├── docker/                 # Gestione Docker e stack Compose
│   └── backup/                 # Automazioni di backup
├── roles/                      # Ruoli riutilizzabili tra più playbook
└── scripts/                    # Script Bash / Python standalone (eseguibili anche da Semaphore)
```

## Playbook disponibili

| Playbook | Descrizione | Documentazione |
| :--- | :--- | :--- |
| `playbooks/proxmox/update-nodes.yml` | Aggiornamento sicuro (`dist-upgrade`) dei nodi Proxmox VE con controllo kernel e riavvio opzionale | [Guida Proxmox](playbooks/proxmox/README.md) |

## Uso con Semaphore UI

1. In Semaphore aggiungi un **Repository** puntando a `https://github.com/Stevaz014/Ansible.git` (branch `main`).
2. Configura **Inventory** e **Key Store** (chiavi SSH, password `sudo`, eventuale password Ansible Vault) direttamente in Semaphore.
3. Crea un **Task Template** per ciascun playbook o script indicando il percorso relativo nel repository (es. `playbooks/system/<nome>.yml`).

## Sicurezza

Questo repository è pubblico: **non contiene password, token, chiavi SSH o indirizzi privati reali**. Tutti i segreti vanno gestiti nel Key Store di Semaphore oppure cifrati con `ansible-vault`.

## Contribuire

Hai notato un errore o un modo più pulito/idempotente di scrivere un playbook o uno script? Apri pure una Issue o una Pull Request!
