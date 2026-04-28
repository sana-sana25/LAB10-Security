##Rapport de TP — Instrumentation dynamique avec Frida

📌 1. Introduction

Dans le cadre de ce TP, nous avons utilisé l’outil Frida, une plateforme d’instrumentation dynamique permettant d’analyser et de modifier le comportement d’une application en temps réel.

L’objectif principal est de :

installer et configurer Frida ;
connecter un environnement Android (émulateur) ;
injecter du code JavaScript dans une application ;
observer son comportement interne (réseau, mémoire, fichiers) ;
simuler et résoudre une panne.
🎯 2. Objectifs du lab
Installer Frida (CLI + Python)
Déployer frida-server sur Android
Tester la connexion PC ↔ Android
Injecter un script JavaScript
Explorer la console interactive
Observer réseau / fichiers / crypto
Diagnostiquer et corriger une panne
⚙️ 3. Environnement utilisé
OS : Windows
Python : 3.14
Frida : 17.9.1
Android Emulator : Pixel 5 (x86_64)
ADB : Android Platform Tools
🧩 4. Installation de Frida

Installation via pip :

python -m pip install --upgrade frida frida-tools

Vérification :

frida --version
frida-ps --help
python -c "import frida; print(frida.__version__)"

👉 Résultat : Frida correctement installé.

📱 5. Déploiement sur Android
🔎 Architecture

L’émulateur utilise :

x86_64
📥 Téléchargement

Téléchargement depuis GitHub :

frida-server-17.9.1-android-x86_64.xz
📦 Déploiement
adb push frida-server /data/local/tmp/
🔐 Permissions
adb shell chmod +x /data/local/tmp/frida-server
▶️ Lancement
adb shell
/data/local/tmp/frida-server &

👉 Le serveur est lancé en arrière-plan.

🔗 6. Test de connexion
frida-ps -U
frida-ps -Uai

👉 Résultat :

liste des processus Android
liste des applications avec leurs packages

✔️ connexion réussie

🧪 7. Injection de script
📄 Script hello.js
Java.perform(function () {
  console.log("[+] Frida Java.perform OK");
});
▶️ Exécution
frida -U -f com.android.settings -l hello.js

Puis :

%resume
✅ Résultat
[+] Frida Java.perform OK

👉 confirme que :

le script est injecté
Frida fonctionne correctement
🧠 8. Console interactive

Utilisation de commandes :

Process.arch
Process.mainModule
Process.getModuleByName("libc.so")
Java.available

👉 Permet d’analyser :

architecture
modules
fonctions natives
environnement Java
🔍 9. Analyse dynamique
🔐 Bibliothèques crypto
Process.enumerateModules().filter(m =>
  m.name.indexOf("ssl") !== -1 ||
  m.name.indexOf("crypto") !== -1
)

👉 Détection de bibliothèques de chiffrement.

🌐 Réseau

Hook des fonctions :

connect
send
recv

Observation :

connect appelée
send appelée
recv appelée
💾 Fichiers

Hook de :

open
read

👉 permet d’observer les accès au stockage local

🛠️ 10. Dépannage
❌ Problème simulé

Arrêt du serveur :

adb shell pkill -f frida
❌ Symptôme
frida-ps -U

👉 erreur :

unable to connect
🔎 Diagnostic
adb shell ps | findstr frida

👉 aucun processus → serveur arrêté

🔧 Correction
adb shell
/data/local/tmp/frida-server &
✅ Vérification
frida-ps -U

👉 fonctionnement restauré

📊 11. Résultats
Frida correctement installé ✔️
Connexion Android réussie ✔️
Injection de script validée ✔️
Analyse dynamique réalisée ✔️
Hooks réseau et fichiers testés ✔️
Dépannage effectué ✔️
🧠 12. Conclusion

Ce TP a permis de comprendre le fonctionnement de l’instrumentation dynamique avec Frida.

Nous avons appris à :

interagir avec une application en temps réel ;
observer son comportement interne ;
intercepter des appels système ;
analyser des composants critiques (réseau, mémoire, fichiers).

Frida constitue un outil puissant en cybersécurité mobile, notamment pour :

le reverse engineering ;
l’analyse de malware ;
le pentest d’applications Android.
