                          ###### CONFIGURER NGINX EN MULTI-DOMAINES ######



Comment configurer un serveur Nginx sur Debian 10 pour qu'il serve deux sites hébergés sur la même machine hôte.

    prérequis:
        - un Nginx fonctionnel configurer par défaut

processus:

1) Créer les fichier de configuration pour chaque site avec chaqun un "block server" approprié:
        
      site 1:
        
            $ sudo nano /etc/nginx/sites-available/www.site1.com.conf

                server {
                    listen 80;
                    listen [::]:80;
                    # ici, on définit le nom de domain qui doit également figurer dans le fichier /etc/hosts
                    server_name site1.com www.site1.com;

                    # ici, on définit le répertoire racine où se trouve le site
                    root /var/www/html/site1;

                        # ici, on indique quel type de fichiers il doit chercher
                        location / {
                            index          index.html index.htm index.php ;
                        }
                }   

      site 2:
        
            $ sudo nano /etc/nginx/sites-available/www.site2.com.conf
                    
                server {
                    listen 80;
                    listen [::]:80;
                    # ici, on définit le nom de domain qui doit également figurer dans le fichier /etc/hosts
                    server_name site2.com www.site2.com;

                    # ici, on définit le répertoire racine où se trouve le site
                    root /var/www/html/site2;

                        # ici, on indique quel type de fichiers il doit chercher
                        location / {
                            index          index.html index.htm index.php ;
                        }
                }

2) Créer les liens symboliques pour que Nginx prenne en compte les configurations nouvellement crées:

        $ sudo ln -s /etc/nginx/sites-available/www.site1.com.conf /etc/nginx/sites-enabled/www.site1.com.conf

        $ sudo ln -s /etc/nginx/sites-available/www.site2.com.conf /etc/nginx/sites-enabled/www.site2.com.conf

3) Ajouter les noms de domaine aux variables d'environnement dans le fichier /etc/hosts

        $ sudo nano /etc/hosts

    Vous devriez pouvoir ajouter les noms de domaines juste à côté de l'adresse locale de la machine (127.0.0.1) comme ceci:

        127.0.0.1 localhost www.autre_nom_de_domaine www.site1.com www.site2.com

4) Créer les dossiers de nos sites (site1 et site2) dans le dossier par defaut de Nginx (/var/www/html):

        $ sudo mkdir /var/www/html/site1 /var/www/html/site2

5) Créer les fichier index.html dans les répertoires racines de chaque site et y copier respectivement les contenus HTML suivant:

        $ sudo nano /var/www/html/site1/index.html

            <html>
                <title>site1</title>
                    <p style="text-align:center; font-size: 40px">Hello site 1</p>
            </html>

        $ sudo nano /var/www/html/site2/index.html

            <html>
                <title>site2</title>
                    <p style="text-align:center; font-size: 40px">Hello site 2</p>
            </html>

6) Donnez les droits d'accès au dossier /var/www contenant les sites:

        $ sudo chown -R votre_nom_d'utilsateur /var/www
    
7) Donner les accès suffisant au fichiers pour que Nginx y accède;

        $ sudo chmod -R 755 /var/www

8) Redémarrer le service Nginx et réseau pour ... les nouvelles configurations:

        $ sudo systemctl restart nginx

        $ sudo systemctl restart networking

9) Accédez par votre navigateur aux site 1 et 2 en entrant les noms de domaine correspondant dans la barre d'adresse de votre navigateur
