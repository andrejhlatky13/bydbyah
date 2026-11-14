# BYD by AH

Trip logbook, charging, battery and tyre dashboard for **BYD DiLink 5.0** head units (tested on BYD Sealion 7, Android 11).
Kniha jázd, nabíjanie, batéria a pneumatiky priamo na displeji auta BYD (DiLink 5.0).

**[⬇ Stiahnuť / Download v0.9.26](https://github.com/andrejhlatky13/bydbyah/releases/latest/download/byd-by-ah-public.apk)** · [all releases](https://github.com/andrejhlatky13/bydbyah/releases)

SHA-256 (v0.9.26): `f2c9afa68097c4103a5f0bfa71cc0d39bb813a6a5ab3e162db295fbdc2cf3dd9`

---

## 🇸🇰 Slovensky

- **Slovensko: 30 dní zadarmo**, ostatné krajiny **14 dní zadarmo** po zadaní e-mailu. Potom sa aplikácia uzamkne.
- **10 € a aplikácia je odomknutá navždy:** podpor vývoj cez [Buy Me a Coffee](https://www.buymeacoffee.com/andrejhlatt) a zadaj tam **ten istý e-mail** ako v aplikácii. Odomknutie pridáme zvyčajne do 24 hodín. V aplikácii potom stlač *Skontrolovať odomknutie*.
- E-mail sa nikam neposiela. Aplikácia porovnáva iba jeho odtlačok (solený SHA-256) so zoznamom [`licenses.json`](licenses.json).
- Aktualizácie: aplikácia sama zistí novú verziu a ponúkne tlačidlo **Aktualizovať**.

| Funkcia | Slovensko | Iná krajina |
|---|:-:|:-:|
| Skúšobné obdobie | 30 dní | 14 dní |
| Jazdy, Trip A/B, mapa | ✅ | ✅ |
| Nabíjanie, batéria, teploty článkov | ✅ | ✅ |
| Tlak a teplota pneumatík | ✅ | ✅ |
| Štatistiky, webový prehľad, zálohy | ✅ | ✅ |
| **Kniha jázd pre DPH (§ 85n), účely jázd, balík pre účtovníka** | ✅ | – |
| Jazyky: SK, EN, CS, HU, PL, DE, 中文 | ✅ | ✅ |

### Inštalácia (Termux)
1. V aute zapni ladenie cez sieť (ADB, port 5555) a zisti IP adresu auta. Telefón musí byť v rovnakej sieti (napr. hotspot).
2. V Termuxe na telefóne:
   ```sh
   pkg install -y android-tools curl
   CAR=192.0.2.10   # IP adresa tvojho auta
   curl -fLO https://github.com/andrejhlatky13/bydbyah/releases/latest/download/byd-by-ah-public.apk && adb connect $CAR:5555 && adb -s $CAR:5555 install -r byd-by-ah-public.apk
   ```
3. Nastavenie oprávnení a automatického štartu (jeden príkaz, rovnaké okno Termuxu):
   ```sh
   S=$CAR:5555; P=sk.hlatky.bydbyah; adb connect $S; A="adb -s $S shell"; for p in $($A dumpsys package $P | grep -o 'android\.permission\.[A-Z0-9_]*' | sort -u); do $A pm grant $P $p 2>/dev/null; done; APPUID=$($A dumpsys package $P | grep -m1 -o 'userId=[0-9]*' | cut -d= -f2); $A dumpsys deviceidle whitelist +$P; $A cmd netpolicy add restrict-background-whitelist $APPUID; $A settings put global hidden_api_policy 1; $A settings put global hidden_api_blacklist_exemptions 'Lcom/ts/,Ldalvik/system/'; $A cmd appops set $P RUN_ANY_IN_BACKGROUND allow; $A cmd appops set $P RUN_IN_BACKGROUND allow; $A appops set $P SYSTEM_ALERT_WINDOW allow; X=$P/sk.hlatky.evlog.service.AutoStartA11y; E=$($A settings get secure enabled_accessibility_services | tr -d '\r'); echo "prístupnosť pred: $E"; case ":$E:" in *":$X:"*|*":$P/sk.hlatky.evlog.service.AutoStartA11y:"*) ;; "::"|":null:") $A settings put secure enabled_accessibility_services $X;; *) $A settings put secure enabled_accessibility_services "'$E:$X'";; esac; $A settings put secure accessibility_enabled 1; echo "prístupnosť teraz: $($A settings get secure enabled_accessibility_services)"; $A cmd notification allow_listener $P/sk.hlatky.evlog.service.AutoStartNls; $A am force-stop $P; $A am start -n $P/sk.hlatky.evlog.ui.MainActivity; sleep 3; K=$($A pm path $P | head -1 | cut -d: -f2 | tr -d '\r'); $A "CLASSPATH=$K app_process /system/bin sk.hlatky.evlog.sup.Supervisor --daemon"; sleep 3; $A "pidof bydbyah_sup; tail -n 3 /data/local/tmp/bydbyah-sup.log"
   ```
4. Otvor aplikáciu, vyber krajinu a jazyk, zadaj e-mail.

## 🌍 English

- **30-day free trial in Slovakia**, **14 days** in other countries, after entering your e-mail; then the app locks.
- **€10 unlocks it forever:** support via [Buy Me a Coffee](https://www.buymeacoffee.com/andrejhlatt) using **the same e-mail** as in the app. Unlocks are usually added within 24 h; then tap *Check unlock*.
- Your e-mail never leaves the car – the app only compares a salted SHA-256 fingerprint with [`licenses.json`](licenses.json).
- The VAT logbook (Slovak § 85n) is only shown when you choose *Slovensko*. Language is independent of country.
- Updates: the app checks GitHub Releases and offers an **Update** button.

Install: same steps as above (Termux → `adb install` → setup one-liner with `CAR=<car IP>`).

---
Unofficial app, not affiliated with BYD. Use at your own risk; don't operate the screen while driving.

## Maintainer notes (update notices)

From v0.9.26 the in-app updater can limit an update dialog to selected countries. This is optional and backward compatible.

- In the GitHub release body, add a line such as `regions: SK` (comma-separated ISO codes, e.g. `regions: SK, CZ`).
- Or attach an `update.json` asset: `{"version":"0.9.27","regions":["SK"]}`. If both are present, `update.json` wins.

If `regions` is present and the user's selected country is not listed, the app stays silent (no update dialog). A later release without `regions`, or one that includes their country, is shown as usual. No `regions` = show to everyone, including older 0.9.25 clients, which ignore this field.

Do not set `regions` on a release that 0.9.25 users should also receive.
