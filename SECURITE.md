# Sécurité de la bêta 0.4.46-beta.1

## Torrent et conservation des fichiers

Le tracker est hébergé sur Cloudflare Workers en HTTPS. Il communique les adresses des participants ; les fichiers des jeux ne transitent pas par Workers. Les sources de téléchargement intégrées à l'EXE sont extractibles : une clé commune ne constitue pas une authentification individuelle.

Le partage après installation utilise le cache torrent original, avec vérification des morceaux, et uniquement les modes sélectionnés. Il n'ajoute pas les sauvegardes ni les fichiers personnels du dossier de jeu au torrent. Un cache incomplet ou modifié arrête le partage sans téléchargement automatique de réparation. Le partage s'arrête à la fermeture du launcher et peut être désactivé dans Paramètres ou arrêté par jeu.

Les participants torrent voient l'adresse de sortie du client. Pour masquer l'IP domestique, le VPN doit couvrir qBittorrent et aria2. Déplacer le tracker n'efface pas une IP déjà connue ; une ancienne adresse DNS, une adresse d'administration distante ou un historique DNS peuvent toujours révéler l'IP de la box. Le launcher ne configure pas le VPN ni la Freebox.

## Discord et compteurs

Les effectifs du Discord sont les estimations publiques de l'invitation. Le total du launcher additionne les téléchargements des EXE Windows dans les versions du dépôt officiel ; il ne mesure pas les personnes uniques. Ces valeurs sont mises en cache et peuvent être légèrement décalées.

Les compteurs par jeu reçoivent uniquement l'identifiant du jeu et un identifiant aléatoire d'installation. Aucun nom de compte, chemin local, sauvegarde ou fichier personnel n'est envoyé. Les réparations et nouvelles tentatives de la même installation ne créent pas un second comptage. Les anciens téléchargements ne sont pas reconstitués. Comme tout service Internet, le serveur reçoit l'adresse de connexion ; pour limiter les abus, il conserve temporairement une empreinte salée quotidienne, sans stocker l'IP brute dans sa base. Les requêtes sortantes utilisent la connexion du launcher.

## Validation et limites

- 186 tests du launcher et 8 tests serveur ont réussi pour la 0.4.45. Les changements de texte de cette version ont passé 4 tests ciblés ; ses 277 contrôles d'interface compilée passent également.
- Les 59 fichiers intégrés contrôlés correspondent aux sources ; l'intégrité ASAR et les fuses Electron sont vérifiés. Les fonctions d'injection Node restent désactivées.
- L'EXE est non signé Authenticode. Son empreinte SHA-256 vérifie les octets téléchargés ; elle ne remplace pas une signature Windows ni une source de confiance.
- Les quotas et la disponibilité des fournisseurs peuvent interrompre le tracker ou les compteurs. Le tracker gère IPv4.
- Ces contrôles concernent le launcher ; ils ne certifient pas chaque jeu, DLL, mod ou mise à jour tierce. Aucun vrai jeu n'a été exécuté pendant ces essais et aucun transfert entre deux réseaux distincts n'est certifié par cette publication.
