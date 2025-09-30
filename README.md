# Pixel’Art

## Ateliers de jeux vidéo rétro et culture geek.

Evaluation pour le module Intégration de la formation Beweb Développeur web et web mobile.

### Installation du serveur Apache sous WSL

J'ai le code de mon site dans un dépôt Git qui se trouve dans mon répertoire utilisateur sous Windows, VS Code ouvre donc ce dossier :

```
C:\Users\fgs\code\bwb\bwb-asso\
```

Je fais donc dans WSL un lien symbolique vers le serveur Apache de WSL pour qu'il accède de façon conventionnelle aux fichiers (pas besoin d'utiliser chown dans ce cas précis) :

```
sudo ln -s /mnt/c/Users/fgs/code/bwb/bwb-asso /var/www/asso.bwb.local
```

J'ai donc les fichiers suivant dans mon dossier Windows :

```
$ ls -l /mnt/c/Users/fgs/code/bwb/bwb-asso/
total 4
-rwxrwxrwx 1 garcia garcia 2278 Sep 30 10:55 README.md
drwxrwxrwx 1 garcia garcia 4096 Sep 30 09:12 public_html

$ ls -l /mnt/c/Users/fgs/code/bwb/bwb-asso/public_html/
total 240
drwxrwxrwx 1 garcia garcia   4096 Sep 29 11:03 activities
drwxrwxrwx 1 garcia garcia   4096 Sep 29 11:03 actu
drwxrwxrwx 1 garcia garcia   4096 Sep 29 11:03 contact
-rwxrwxrwx 1 garcia garcia 227102 Sep 29 14:50 favicon.ico
drwxrwxrwx 1 garcia garcia   4096 Sep 29 11:16 images
-rwxrwxrwx 1 garcia garcia   7771 Sep 30 10:09 index.html
-rwxrwxrwx 1 garcia garcia   6226 Sep 29 16:47 style.css
```

Pour la création du VirtualHost, j'utilise le template suivant :

```
<VirtualHost *:80>
    ServerName DOMAIN
    ServerAlias www.DOMAIN
    DocumentRoot /var/www/DOMAIN/public_html

    <Directory /var/www/DOMAIN/public_html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/DOMAIN_error.log
    CustomLog ${APACHE_LOG_DIR}/DOMAIN_access.log combined
</VirtualHost>
```

Je copie donc le fichier de configuration en y mettant le nom de domaine du site :

```
sudo sed "s|DOMAIN|asso.bwb.local|g" /mnt/c/Users/fgs/code/templates/apache2.local.conf \
    | sudo tee /etc/apache2/sites-available/asso.bwb.local.conf > /dev/null
```

Je modifie le fichier hosts avec PowerShell en tant qu'administrateur :

```
notepad C:\Windows\System32\drivers\etc\hosts
```

Pour y ajouter les lignes suivantes :

```
127.0.0.1   asso.bwb.local
::1 asso.bwb.local
```

Une fois que c'est fait, je peux à présent activer le site et recharger Apache :

```
sudo a2ensite asso.bwb.local.conf
sudo service apache2 reload
```

Je peux constater que le site est opérationnel avec la commande suivante :

```
$ apache2ctl -t -D DUMP_VHOSTS
VirtualHost configuration:
*:80                   is a NameVirtualHost
         default server FGS-PC. (/etc/apache2/sites-enabled/000-default.conf:1)
         port 80 namevhost FGS-PC. (/etc/apache2/sites-enabled/000-default.conf:1)
         port 80 namevhost asso.bwb.local (/etc/apache2/sites-enabled/asso.bwb.local.conf:1)
                 alias www.asso.bwb.local
```

It works!

L'accés au site via `http://localhost/association` peut se faire en mettant le lien symbolique suivant :

```
sudo ln -s /mnt/c/Users/fgs/code/bwb/bwb-asso/public_html /var/www/html/association
```
