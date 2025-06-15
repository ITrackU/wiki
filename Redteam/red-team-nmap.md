# Nmap Wiki - Guide Complet

## Table des matières
- [Introduction à Nmap](#introduction-à-nmap)
- [Commandes basiques](#commandes-basiques)
  - [Configuration des ports](#configuration-des-ports)
  - [Options de détection](#options-de-détection)
  - [Paramètres divers](#paramètres-divers)
- [Techniques de balayage furtif](#techniques-de-balayage-furtif)
  - [Options de balayage](#options-de-balayage)
  - [Contrôle de vitesse](#contrôle-de-vitesse)
  - [Conseils pour éviter la détection](#conseils-pour-éviter-la-détection)
- [Nmap Scripting Engine (NSE)](#nmap-scripting-engine-nse)
  - [Introduction au NSE](#introduction-au-nse)
  - [Structure des scripts NSE](#structure-des-scripts-nse)
  - [Exécution de scripts](#exécution-de-scripts)
  - [Création de scripts NSE simples](#création-de-scripts-nse-simples)
  - [Collecte de données avec NSE](#collecte-de-données-avec-nse)
  - [Scripting avancé](#scripting-avancé)
  - [Bonnes pratiques](#bonnes-pratiques)
- [Exemples pratiques](#exemples-pratiques)
  - [Scan de base](#scan-de-base)
  - [Détection de services](#détection-de-services)
  - [Audit de sécurité](#audit-de-sécurité)
  - [Projet : Script de scan personnalisé](#projet--script-de-scan-personnalisé)
- [Ressources supplémentaires](#ressources-supplémentaires)

## Introduction à Nmap

Nmap (Network Mapper) est un outil de découverte et d'audit de réseau open source développé par Gordon Lyon (alias Fyodor). Il permet de scanner des réseaux informatiques, découvrir des dispositifs, des services et des vulnérabilités potentielles.

**Principales fonctionnalités :**
- Balayage de ports
- Détection de dispositifs
- Identification de services et versions
- Détection de vulnérabilités
- Exécution de scripts personnalisés via NSE

## Commandes basiques

### Configuration des ports

Pour spécifier les ports à scanner :

| Option | Description |
|--------|-------------|
| `-p <ports>` | Scan des ports spécifiques (ex: `-p 80,443,8080`) |
| `-p- <plage de ports>` | Scan d'une plage de ports (ex: `-p 1-1000`) |
| `-top-ports <nombre>` | Scan des N ports les plus courants (ex: `-top-ports 100`) |

### Options de détection

| Option | Description |
|--------|-------------|
| `-A` | Active la détection d'OS et des versions de services |
| `-O` | Active uniquement la détection d'OS |
| `-sV` | Active la détection des versions de services |

### Paramètres divers

| Option | Description |
|--------|-------------|
| `-v` / `-vv` | Active le mode verbeux (affiche des informations détaillées) |
| `-oN <fichier>` | Enregistre les résultats dans un fichier spécifié |
| `-iL <fichier>` | Lit une liste d'adresses IP ou d'hôtes à partir d'un fichier |
| `--script <scripts>` | Spécifie un ou plusieurs scripts NSE à exécuter |
| `-sP` | Effectue un balayage de ping sans balayage de ports |

## Techniques de balayage furtif

### Options de balayage

| Option | Description | Caractéristiques |
|--------|-------------|------------------|
| `-sS` | Balayage SYN (Half-open) | Envoie des paquets SYN sans établir de connexion complète, moins détectable |
| `-sT` | Balayage TCP connect | Effectue une connexion TCP complète, laisse plus de traces dans les journaux |
| `-sI` | Balayage Idle (ZOMBIE) | Utilise un hôte tiers (zombie) avec IPID incrémentiel comme intermédiaire |

### Contrôle de vitesse

| Option | Description |
|--------|-------------|
| `-T0` | Mode furtif (très lent) |
| `-T1` | Mode lent |
| `-T2` | Mode par défaut |
| `-T3` | Mode agressif |
| `-T4` | Mode insolent |
| `-T5` | Mode insane (très rapide) |
| `--scan-delay <temps>` | Spécifie un délai entre les paquets envoyés |

### Conseils pour éviter la détection

- Utiliser Chameleon ou Ncat pour encapsuler les paquets Nmap dans du DNS ou HTTP
- Utiliser un VPN ou un proxy pour masquer l'adresse IP source
- Éviter les heures de pointe sur le réseau cible
- Modifier les règles de pare-feu pour rendre Nmap moins détectable
- Privilégier les options de balayage SYN ou IDLE

## Nmap Scripting Engine (NSE)

### Introduction au NSE

Le Nmap Scripting Engine (NSE) est un composant puissant de Nmap qui permet d'étendre ses fonctionnalités via des scripts écrits en Lua. NSE permet d'automatiser des tâches comme la découverte de services, la détection de vulnérabilités, et la collecte d'informations système.

Pour exécuter un script NSE :
```bash
nmap -p 22 --script ssh-brute nom_de_l_hote
```

### Structure des scripts NSE

Un script NSE est un fichier Lua qui suit une structure spécifique :

```lua
-- Description du script
description = "Mon script NSE"

-- Catégories du script
categories = {"default", "discovery"}

-- Règle d'exécution pour un hôte spécifique
hostrule = function(host)
    -- Conditions pour exécuter le script sur cet hôte
    return true  -- ou false selon les conditions
end

-- Règle d'exécution pour un port spécifique
portrule = function(host, port)
    -- Conditions pour exécuter le script sur ce port
    return port.number == 80 and port.state == "open"
end

-- Fonction principale du script
function action(host, port)
    -- Code du script ici
    return "Résultat du script"
end
```

### Exécution de scripts

Pour exécuter des scripts NSE avec Nmap :

```bash
# Exécuter un script spécifique
nmap --script nom_du_script.lua nom_de_l_hote

# Exécuter tous les scripts d'une catégorie
nmap --script discovery nom_de_l_hote

# Exécuter plusieurs scripts
nmap --script "http-*,ssl-*" nom_de_l_hote
```

Les scripts NSE sont stockés dans le répertoire `nmap/scripts` de l'installation Nmap.

### Création de scripts NSE simples

Exemple d'un script NSE simple :

```lua
description = "Script NSE simple"
categories = {"default"}

function action()
    -- Affiche un message dans la console
    print("Mon premier script NSE")
    return "Script exécuté avec succès"
end
```

### Collecte de données avec NSE

Les scripts NSE peuvent collecter des données réseau en utilisant les fonctions `hostrule()` et `portrule()` :

```lua
description = "Collecte d'informations sur les hôtes avec le port 80 ouvert"
categories = {"discovery"}

-- Exécution du script uniquement si le port 80 est ouvert
portrule = function(host, port)
    return port.number == 80 and port.state == "open"
end

function action(host, port)
    -- Code pour collecter des informations sur l'hôte
    local result = "Informations sur l'hôte " .. host.ip
    return result
end
```

### Scripting avancé

Exemple de script NSE avancé pour la collecte d'informations :

```lua
description = "Script NSE avancé pour collecter des informations"
categories = {"discovery"}

function action(host, port)
    local result = {}
    
    -- Ajoute l'adresse IP de l'hôte au résultat
    table.insert(result, "Adresse IP : " .. host.ip)
    
    -- Vérifie si un serveur web est détecté
    if port.service == "http" then
        table.insert(result, "Serveur web détecté sur le port " .. port.number)
        
        -- Tente d'obtenir le titre de la page d'accueil
        local response = http.get(host, port, "/")
        if response and response.body then
            local title = response.body:match("<title>(.-)</title>")
            if title then
                table.insert(result, "Titre de la page : " .. title)
            end
        end
    end
    
    return table.concat(result, "\n")
end
```

### Bonnes pratiques

Pour développer des scripts NSE efficaces :

1. **Documentation complète**
   - Fournir une description claire du script
   - Ajouter des commentaires explicatifs
   - Inclure des exemples d'utilisation

2. **Structure du code**
   - Choisir un nom de script significatif
   - Sélectionner les catégories appropriées
   - Organiser le code de manière logique

3. **Tests et débogage**
   - Tester sur des cibles contrôlées
   - Utiliser des messages de débogage
   - Gérer correctement les erreurs
   - Utiliser des outils de débogage interactifs

## Exemples pratiques

### Scan de base

Scan des 1000 premiers ports d'un hôte :
```bash
nmap -p 1-1000 exemple.com
```

### Détection de services

Détection des services et des versions :
```bash
nmap -sV -p 1-1000 exemple.com
```

### Audit de sécurité

Scan complet avec détection d'OS et scripts de vulnérabilité :
```bash
nmap -A --script vuln exemple.com
```

### Projet : Script de scan personnalisé

Voici un exemple de script NSE pour scanner des réseaux IP avec des ports spécifiques :

```lua
description = "Script NSE de scan de réseaux IP avec ports spécifiques"
categories = {"discovery"}

-- Arguments acceptés par le script
args = {
    {name = "ip", desc = "Plages d'adresses IP à scanner"},
    {name = "ports", desc = "Ports à scanner"}
}

function action()
    -- Lire les arguments pour les plages d'adresses IP et les ports
    local target_ips = stdnse.get_script_args('ip')
    local target_ports = stdnse.get_script_args('ports')
    
    -- Vérifier que des plages d'adresses IP ont été spécifiées
    if not target_ips then
        return "Erreur: Veuillez spécifier au moins une adresse IP avec --script-args='ip=192.168.1.0/24'"
    end
    
    -- Vérifier que des ports ont été spécifiés
    if not target_ports then
        return "Erreur: Veuillez spécifier au moins un port avec --script-args='ports=80,443'"
    end
    
    -- Préparer la commande Nmap
    local command = string.format("nmap -p %s %s", target_ports, target_ips)
    
    -- Exécuter la commande
    local handle = io.popen(command)
    local result = handle:read("*a")
    handle:close()
    
    return result
end
```

Pour utiliser ce script :
```bash
nmap --script scan_custom.lua --script-args='ip=192.168.1.0/24,ports=22,80,443'
```
