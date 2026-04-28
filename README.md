# Rapport du LAB 10 : Guide d'installation de Frida

## 1. Introduction

Dans le cadre de ce TP, nous avons utilisé l’outil Frida, une plateforme d’instrumentation dynamique permettant d’analyser et de modifier le comportement d’une application en temps réel.

L’objectif principal est de :

installer et configurer Frida ;
connecter un environnement Android (émulateur) ;
injecter du code JavaScript dans une application ;
observer son comportement interne (réseau, mémoire, fichiers) ;
simuler et résoudre une panne.

## 2. Objectifs du lab
- Installer Frida (CLI + Python)
- Déployer frida-server sur Android
- Tester la connexion PC ↔ Android
- Injecter un script JavaScript
- Explorer la console interactive
- Observer réseau / fichiers / crypto
- Diagnostiquer et corriger une panne

## 3. Environnement utilisé
- OS : Windows
- Python : 3.14
- Frida : 17.9.1
- Android Emulator : Pixel 5 (x86_64)
- ADB : Android Platform Tools

## 4. Installation de Frida

Installation via pip : python -m pip install --upgrade frida frida-tools
<img width="1097" height="87" alt="1" src="https://github.com/user-attachments/assets/8068c96e-9c2e-4edc-ade9-7195eadac1f8" />
<img width="1097" height="453" alt="2" src="https://github.com/user-attachments/assets/e5ca096e-c29f-42cc-ab94-5a2dea14572b" />

- Vérification :
frida --version
frida-ps --help
<img width="1105" height="575" alt="3" src="https://github.com/user-attachments/assets/a58d7a14-bea6-4d7f-ab1b-acbfd91a8e08" />

python -c "import frida; print(frida.__version__)"
<img width="808" height="77" alt="4" src="https://github.com/user-attachments/assets/53e4a626-6247-4461-9f2b-a71df6208489" />

-->  Résultat : Frida correctement installé.

## 5. Déploiement sur Android

### 🔎 Architecture

L’émulateur utilise : x86_64
<img width="805" height="650" alt="5" src="https://github.com/user-attachments/assets/9f1aa7de-d3e2-4f4f-8896-fe3be774234e" />

### 📥 Téléchargement

Téléchargement depuis GitHub :frida-server-17.9.1-android-x86_64.xz
<img width="1915" height="770" alt="8" src="https://github.com/user-attachments/assets/5416f13f-2d7f-4506-9285-b21de23ae8a5" />

### 📦 Déploiement
adb push frida-server /data/local/tmp/
<img width="1105" height="83" alt="9" src="https://github.com/user-attachments/assets/e423c180-c973-4612-af70-ef58305c1f03" />

### 🔐 Permissions
adb shell chmod +x /data/local/tmp/frida-server
<img width="1110" height="107" alt="10" src="https://github.com/user-attachments/assets/363e3328-da10-43fc-a748-766b8deb676d" />

### ▶️ Lancement
adb shell
/data/local/tmp/frida-server-17.9.1-android-x86_64 &
<img width="1110" height="107" alt="10" src="https://github.com/user-attachments/assets/428d9e1f-d6df-442c-a437-2eec4162a496" />
👉 Le serveur est lancé en arrière-plan.

## 🔗 6. Test de connexion
frida-ps -U
<img width="1072" height="538" alt="11" src="https://github.com/user-attachments/assets/a875acb5-cfb4-4c12-a43f-08df14b8e725" />
frida-ps -Uai
<img width="1042" height="481" alt="12" src="https://github.com/user-attachments/assets/406e12da-96d2-425e-853d-280579c9be95" />

👉 Résultat :
- liste des processus Android
- liste des applications avec leurs packages

✔️ connexion réussie

## 🧪 7. Injection de script
📄 Script hello.js
Java.perform(function () {
  console.log("[+] Frida Java.perform OK");
});
<img width="1206" height="260" alt="13" src="https://github.com/user-attachments/assets/ab013b8a-ebac-4709-8739-13b942257432" />

 ▶️ Exécution
frida -U -f com.android.settings -l hello.js
<img width="1090" height="376" alt="14" src="https://github.com/user-attachments/assets/5eae2128-45a6-4987-8d37-0fc70219ea63" />

### Résultat 
[+] Frida Java.perform OK
👉 confirme que :
- le script est injecté
- Frida fonctionne correctement

## 8. Console interactive

Utilisation de commandes :

Process.arch
<img width="1092" height="453" alt="16" src="https://github.com/user-attachments/assets/c6dc5d35-35da-43a4-8446-bcddd61eba5b" />

Process.mainModule
<img width="806" height="273" alt="17" src="https://github.com/user-attachments/assets/09312eb8-08e9-4cd0-887b-edbbe607b786" />

Process.getModuleByName("libc.so")
<img width="888" height="228" alt="18" src="https://github.com/user-attachments/assets/a7c00571-0817-4ee9-b528-28a75b7c5134" />
<img width="1032" height="57" alt="19" src="https://github.com/user-attachments/assets/717be51a-cd01-4a92-a1e5-7614940b1e63" />

Process.enumerateModules()
<img width="1078" height="522" alt="20" src="https://github.com/user-attachments/assets/41345d62-bd77-4cca-aec9-d99f4f8be7bb" />

Java.available
<img width="776" height="138" alt="21" src="https://github.com/user-attachments/assets/1f20746f-a868-4801-92c5-6da460693393" />

👉 Permet d’analyser :
- architecture
- modules
- fonctions natives
- environnement Java


## 🛠️ 9. Dépannage
❌ Problème simulé

Arrêt du serveur : adb shell pkill -f frida
<img width="962" height="132" alt="22" src="https://github.com/user-attachments/assets/dec202a1-9a4a-4b6c-92ad-e3410d3756b9" />

❌ Symptôme
frida-ps -U
<img width="711" height="60" alt="23" src="https://github.com/user-attachments/assets/1d5db96b-c512-42b7-8008-981b2dc8c16d" />
👉 erreur : inaccessible or not found !

### 🔎 Diagnostic
adb shell ps | findstr frida
<img width="676" height="75" alt="24" src="https://github.com/user-attachments/assets/d1c97815-e98c-4602-83d9-7151a782be09" />
👉 aucun processus → serveur arrêté

🔧 Correction
adb shell
/data/local/tmp/frida-server-17.9.1-android-x86_64 &
<img width="845" height="44" alt="image" src="https://github.com/user-attachments/assets/df997c89-c80e-4168-9a4c-ac5fa54f623d" />

✅ Vérification
frida-ps -U
<img width="908" height="494" alt="image" src="https://github.com/user-attachments/assets/ce30f678-6f60-4640-834e-7809b035df5b" />

👉 fonctionnement restauré

## 10. Résultats
- Frida correctement installé 
- Connexion Android réussie 
- Injection de script validée 
- Analyse dynamique réalisée
- Hooks réseau et fichiers testés 
- Dépannage effectué 

## 11. Conclusion

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
