# Sécurité de la bêta 0.4.50-beta.1

## Mise à jour automatique de l’application

À chaque démarrage, le launcher portable consulte les versions du seul dépôt officiel. Les brouillons et les versions plus anciennes sont ignorés. Les préversions sont acceptées pour les utilisateurs de la bêta ; une version stable ne bascule pas automatiquement vers une bêta. Le téléchargement exige une origine GitHub HTTPS autorisée, une taille attendue et une empreinte SHA-256 publiée par GitHub. Le fichier est également contrôlé comme EXE Windows.

Un helper compilé localement attend la fermeture du launcher et de son parent portable, vérifie les empreintes et remplace seulement l’EXE à son emplacement. Une copie .previous.exe est conservée. Le nouveau launcher confirme son démarrage ; en cas d’échec, le helper tente de restaurer et de relancer la version précédente. Un fichier verrouillé ou un dossier non modifiable ne déclenche pas d’élévation. Un échec déjà constaté n’est pas retenté automatiquement à chaque lancement. Les profils, jeux et sources ne sont pas remplacés.

Les versions jusqu’à 0.4.48 doivent être remplacées manuellement une dernière fois : leur transport Electron refuse les redirections du téléchargement GitHub. La 0.4.49 utilise net.request et valide chaque nouvelle origine avant de la contacter, avec une session en mémoire sans identifiants ni cookies.

## Correctif du tracker en 0.4.48

Les annonces HTTP(S) privées du moteur aria2 utilisent IPv4 et un délai de connexion borné, conformément au tracker actuel. Ce correctif reste inclus dans la 0.4.49. Une connexion au tracker réussie ne garantit pas que deux clients puissent établir une connexion directe : un NAT ou un VPN sans port entrant peut encore empêcher le transfert.

## Moteur de partage indépendant

Ozuro Partage est un processus distinct, avec une copie du runtime Electron contrôlée par empreintes et conservant la validation ASAR et les fuses. Il ne crée ni service Windows, ni tâche planifiée, ni démarrage automatique avec Windows. Une icône visible dans la zone de notification permet de l’arrêter. Il utilise le même chemin aria2 que le launcher, sans modifier les règles VPN, le pare-feu ou qBittorrent. La commande locale écoute uniquement 127.0.0.1 et exige un jeton aléatoire, des requêtes JSON et des caches torrent validés. L’interface se reconnecte au moteur existant. Les désactivations et limites existantes sont conservées.

Fermer le launcher conserve les partages déjà démarrés ; terminer le moteur ou l’ensemble de ses processus les arrête. La réception de jeux reste gérée par le launcher. La veille ou l’arrêt du PC interrompt les échanges.

## Torrent et conservation des fichiers

Le tracker est hébergé sur Cloudflare Workers en HTTPS. Il communique les adresses des participants ; les fichiers des jeux ne transitent pas par Workers. Les sources de téléchargement intégrées à l'EXE sont extractibles : une clé commune ne constitue pas une authentification individuelle.

Le partage après installation utilise le cache torrent original, avec vérification des morceaux, et uniquement les modes sélectionnés. Il n'ajoute pas les sauvegardes ni les fichiers personnels du dossier de jeu au torrent. Un cache incomplet ou modifié arrête le partage sans téléchargement automatique de réparation. Le moteur indépendant continue après fermeture du launcher. Le partage peut être désactivé dans Paramètres, arrêté par jeu ou terminé via l’icône du moteur près de l’horloge.

Les participants torrent voient l'adresse de sortie du client. Pour masquer l'IP domestique, le VPN doit couvrir qBittorrent et aria2. Déplacer le tracker n'efface pas une IP déjà connue ; une ancienne adresse DNS, une adresse d'administration distante ou un historique DNS peuvent toujours révéler l'IP de la box. Le launcher ne configure pas le VPN ni la Freebox.

## Discord et compteurs

Les effectifs du Discord sont les estimations publiques de l'invitation. Le total du launcher additionne les téléchargements des EXE Windows dans les versions du dépôt officiel ; il ne mesure pas les personnes uniques. Ces valeurs sont mises en cache et peuvent être légèrement décalées.

Les compteurs par jeu reçoivent uniquement l'identifiant du jeu et un identifiant aléatoire d'installation. Aucun nom de compte, chemin local, sauvegarde ou fichier personnel n'est envoyé. Les réparations et nouvelles tentatives de la même installation ne créent pas un second comptage. Les anciens téléchargements ne sont pas reconstitués. Comme tout service Internet, le serveur reçoit l'adresse de connexion ; pour limiter les abus, il conserve temporairement une empreinte salée quotidienne, sans stocker l'IP brute dans sa base. Les requêtes sortantes utilisent la connexion du launcher.

## Validation et limites

- 205 tests de la suite complète passent, dont les 4 tests du helper Windows et le test avec Electron réel des redirections, du flux et des annulations. Les 284 contrôles de l’interface compilée passent. Le remplacement et le redémarrage du vrai EXE portable sont vérifiés avec un profil isolé ; un téléchargement GitHub réel est vérifié par empreinte.
- Les 65 fichiers intégrés contrôlés correspondent aux sources ; l'intégrité ASAR et les fuses Electron sont vérifiés. Les fonctions d'injection Node restent désactivées.
- L'EXE est non signé Authenticode. Son empreinte SHA-256 vérifie les octets téléchargés ; elle ne remplace pas une signature Windows ni une source de confiance.
- Les quotas et la disponibilité des fournisseurs peuvent interrompre le tracker ou les compteurs. Le tracker gère IPv4.
- Ces contrôles concernent le launcher ; ils ne certifient pas chaque jeu, DLL, mod ou mise à jour tierce. Aucun vrai jeu n'a été exécuté pendant ces essais et aucun transfert entre deux réseaux distincts n'est certifié par cette publication.
