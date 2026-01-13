# Script d'Activation ZRAM pour Ubuntu

## 📋 Description

Ce script permet d'installer, configurer et gérer facilement ZRAM sur Ubuntu 24.04. ZRAM est un module du noyau Linux qui crée un périphérique de swap compressé en RAM, améliorant ainsi les performances du système en réduisant l'utilisation du swap sur disque.

Le script utilise le paquet **zram-tools** et configure le fichier `/etc/default/zramswap`.

## ✨ Fonctionnalités

- ✅ Installation automatique du paquet `zram-tools`
- ✅ Configuration personnalisable (algorithme, taille, priorité)
- ✅ Sauvegarde automatique des configurations existantes
- ✅ Vérification complète du statut ZRAM
- ✅ Tests de performance intégrés
- ✅ Désinstallation propre avec option de purge
- ✅ Logging détaillé de toutes les opérations
- ✅ Mode verbeux pour le débogage
- ✅ Messages colorés et informatifs

## 🔧 Prérequis

- **Système d'exploitation** : Ubuntu 24.04 (ou versions antérieures compatibles)
- **Privilèges** : Accès root (sudo)
- **Kernel** : Version 3.15 ou supérieure (généralement inclus dans Ubuntu moderne)
- **RAM** : Minimum 2GB recommandé (le script fonctionne avec moins mais les bénéfices sont limités)

## 📦 Installation

1. **Téléchargez le script** :
   ```bash
   wget https://github.com/votre-repo/activation_zram_ubuntu.sh
   # ou clonez le dépôt
   ```

2. **Rendez-le exécutable** :
   ```bash
   chmod +x activation_zram_ubuntu.sh
   ```

3. **Exécutez-le avec les privilèges root** :
   ```bash
   sudo ./activation_zram_ubuntu.sh
   ```

## 🚀 Utilisation

### Installation par défaut

L'installation avec les paramètres par défaut (50% de la RAM, algorithme zstd, priorité 100) :

```bash
sudo ./activation_zram_ubuntu.sh install
```

ou simplement :

```bash
sudo ./activation_zram_ubuntu.sh
```

### Installation avec options personnalisées

```bash
# Installation avec 8GB de ZRAM et algorithme lz4
sudo ./activation_zram_ubuntu.sh install --size "8G" --algorithm lz4

# Installation avec 25% de la RAM et tests de performance
sudo ./activation_zram_ubuntu.sh install --size "ram / 4" --test

# Installation complète avec toutes les options
sudo ./activation_zram_ubuntu.sh install --size "ram / 2" --algorithm zstd --priority 100 --test --verbose
```

### Vérification du statut

```bash
sudo ./activation_zram_ubuntu.sh verify
```

### Tests de performance

```bash
sudo ./activation_zram_ubuntu.sh test
```

### Désinstallation

```bash
# Désactivation simple (conserve le paquet)
sudo ./activation_zram_ubuntu.sh uninstall

# Désinstallation complète avec suppression du paquet
sudo ./activation_zram_ubuntu.sh uninstall --purge
```

### Rollback

Annuler l'installation et restaurer l'état précédent :

```bash
sudo ./activation_zram_ubuntu.sh rollback
```

## ⚙️ Options de Configuration

### Options en ligne de commande

| Option | Description | Exemple |
|--------|-------------|---------|
| `--size SIZE` | Taille du ZRAM | `--size "4G"` ou `--size "ram / 2"` |
| `--algorithm ALGO` | Algorithme de compression | `--algorithm zstd` |
| `--priority PRIO` | Priorité du swap (0-32767) | `--priority 100` |
| `--test` | Effectuer des tests après installation | `--test` |
| `--verbose` ou `-v` | Mode verbeux (débogage) | `--verbose` |
| `--help` ou `-h` | Afficher l'aide | `--help` |

### Formats de taille acceptés

- **Pourcentage de RAM** : `ram / 2` (50%), `ram / 4` (25%)
- **Taille fixe** : `4G`, `8192M`, `2G`, etc.

### Algorithmes de compression disponibles

| Algorithme | Description | Performance | Compression |
|------------|-------------|-------------|-------------|
| `zstd` | **Recommandé** - Excellent équilibre | Rapide | Très bonne |
| `lz4` | Très rapide | Très rapide | Bonne |
| `lzo-rle` | Optimisé pour certaines architectures | Rapide | Moyenne |
| `lzo` | Standard, compatible | Rapide | Moyenne |

### Priorité du swap

La priorité détermine l'ordre d'utilisation des périphériques de swap. Plus la valeur est élevée, plus ZRAM sera utilisé en premier. La valeur par défaut de **100** est recommandée pour que ZRAM soit prioritaire par rapport au swap sur disque.

## 📊 Configuration par défaut

Le script utilise les paramètres suivants par défaut (modifiables dans le script) :

```bash
ZRAM_COMP_ALGO="zstd"      # Algorithme de compression
ZRAM_SIZE="ram / 2"        # 50% de la RAM totale
ZRAM_PRIORITY=100          # Priorité élevée
```

## 📁 Fichiers et répertoires

- **Configuration** : `/etc/default/zramswap`
- **Sauvegardes** : `/etc/default/backups/`
- **Logs** : `/var/log/zram-install.log` (ou `/tmp/zram-install.log` si `/var/log` n'est pas accessible)
- **Service systemd** : `zramswap.service`

## 🔍 Vérification manuelle

Après l'installation, vous pouvez vérifier le statut de ZRAM manuellement :

```bash
# Vérifier le service
sudo systemctl status zramswap.service

# Voir les périphériques ZRAM
zramctl

# Voir le swap actif
swapon --show

# Voir les statistiques détaillées
cat /sys/block/zram0/mm_stat
```

## 🐛 Dépannage

### Le service ne démarre pas

1. Vérifiez les logs :
   ```bash
   sudo journalctl -u zramswap.service -n 50
   ```

2. Vérifiez la configuration :
   ```bash
   cat /etc/default/zramswap
   ```

3. Vérifiez que le paquet est installé :
   ```bash
   dpkg -l | grep zram-tools
   ```

### ZRAM n'est pas utilisé

1. Vérifiez que le swap ZRAM est actif :
   ```bash
   swapon --show
   ```

2. Vérifiez la priorité (doit être supérieure au swap disque) :
   ```bash
   swapon --show | grep priority
   ```

3. Vérifiez l'utilisation actuelle :
   ```bash
   free -h
   ```

### Erreur de permissions

Assurez-vous d'exécuter le script avec `sudo` :
```bash
sudo ./activation_zram_ubuntu.sh
```

### Le script échoue lors de l'installation du paquet

1. Mettez à jour la base de données des paquets :
   ```bash
   sudo apt update
   ```

2. Vérifiez que le dépôt universe est activé :
   ```bash
   sudo apt install software-properties-common
   sudo add-apt-repository universe
   ```

## 📝 Logs

Toutes les opérations sont enregistrées dans le fichier de log. Consultez-le pour plus de détails :

```bash
# Voir les dernières entrées
tail -f /var/log/zram-install.log

# Voir tout le log
cat /var/log/zram-install.log
```

## 🔄 Commandes disponibles

| Commande | Description |
|----------|-------------|
| `install` | Installe et configure ZRAM (par défaut) |
| `uninstall` | Désactive ZRAM et supprime la configuration |
| `uninstall --purge` | Désactive ZRAM et supprime le paquet |
| `verify` | Vérifie le statut actuel de ZRAM |
| `test` | Teste les performances de ZRAM |
| `rollback` | Annule l'installation et restaure l'état précédent |

## 💡 Recommandations

### Taille de ZRAM

- **2-4GB RAM** : Utilisez `ram / 4` (25%)
- **4-8GB RAM** : Utilisez `ram / 2` (50%) - **Recommandé**
- **8GB+ RAM** : Utilisez `ram / 2` ou une taille fixe comme `4G`

### Algorithme

- **zstd** est recommandé pour la plupart des cas (meilleur équilibre)
- **lz4** si vous privilégiez la vitesse pure
- **lzo** ou **lzo-rle** pour la compatibilité maximale

### Quand utiliser ZRAM

✅ **Recommandé pour** :
- Systèmes avec RAM limitée (< 8GB)
- Machines virtuelles
- Systèmes de développement
- Serveurs avec charge variable

❌ **Moins utile pour** :
- Systèmes avec beaucoup de RAM (> 16GB) et peu de charge
- Systèmes avec SSD très rapide et beaucoup de RAM

## 🔒 Sécurité

- Le script nécessite les privilèges root pour fonctionner
- Les sauvegardes sont créées automatiquement avant toute modification
- Le script valide toutes les entrées avant de les appliquer
- Aucune donnée n'est envoyée à l'extérieur

## 📄 Licence

Ce script est fourni tel quel, sans garantie. Utilisez-le à vos propres risques.

## 🤝 Contribution

Les contributions sont les bienvenues ! N'hésitez pas à :
- Signaler des bugs
- Proposer des améliorations
- Soumettre des pull requests

## 📚 Ressources

- [Documentation ZRAM du kernel Linux](https://www.kernel.org/doc/Documentation/blockdev/zram.txt)
- [Paquet zram-tools sur Ubuntu](https://packages.ubuntu.com/zram-tools)
- [Documentation systemd](https://www.freedesktop.org/software/systemd/man/)

## 🆘 Support

En cas de problème :
1. Consultez la section Dépannage ci-dessus
2. Vérifiez les logs dans `/var/log/zram-install.log`
3. Utilisez le mode `--verbose` pour plus d'informations
4. Ouvrez une issue sur GitHub avec les détails du problème

---

**Version** : 2.0  
**Dernière mise à jour** : 2024  
**Auteur** : Script d'activation ZRAM pour Ubuntu
