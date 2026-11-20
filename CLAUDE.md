@AGENTS.md

# Contesto di sessione (note per riprendere il lavoro)

Questo file vive solo sul branch di note del fork `ZioZoni95/AnyPS5`. Non va mai in una PR verso upstream: le regole del progetto vietano file di note nel repo e `tools/check_conventions.py` lo segnala.

## Obiettivo
Contribuire ad AnyPS5 (upstream: `boykopovar/AnyPS5`), che ricollega eseguibili PS5 in programmi nativi Linux o Windows con librerie di sistema riscritte. Non e' un emulatore. L'utente e' al primo contributo e ha firmato il CLA.

## Remote e branch
- `origin` = fork `ZioZoni95/AnyPS5`; `upstream` = `https://github.com/boykopovar/AnyPS5` (da aggiungere su un clone nuovo: `git remote add upstream https://github.com/boykopovar/AnyPS5`).
- `main` del fork e `ccr-99f1688e-sdsir6` sono stati allineati a `upstream/main` il 2026-11-19. Il fork non si aggiorna da solo: `git fetch upstream` e poi fast-forward o rebase prima di ogni lavoro.
- Questo branch e' il branch di note. Ogni argomento di codice va in un branch separato, nato da `upstream/main` aggiornato, con la sua PR. Il nome del branch compare nel fragment di debito `docs/dev/debt/changes/<branch>.md`.
- Un branch di argomento non contiene questo file. Su un clone locale copialo a mano e aggiungilo a `.git/info/exclude`.

## Regole che contano (dettaglio in AGENTS.md, CONTRIBUTING.md, docs/dev/CONVENTIONS.md)
- Nessun commento nel codice. Export non implementabile: `NotImplemented_nid_no_patch(__func__)`. Mai un successo inventato.
- Comportamento generale, mai casi speciali visti nella trace di un titolo.
- Lacune note solo tramite fragment in `docs/dev/debt/changes/`. Nessun altro file nuovo.
- Commit e titoli PR: Conventional Commits. Corpo PR: copia di `.github/pull_request_template.md`.
- Prima di iniziare `python3 tools/pr_overlap.py --names <funzione>`, prima del push `python3 tools/check_conventions.py --base upstream/main`, prima di aprire la PR `python3 tools/pr_overlap.py --branch <branch>`.
- Limite: 1 PR aperta finche' non ne hai una mergiata.
- Una PR si apre solo su richiesta esplicita dell'utente.

## Limiti dell'ambiente remoto (sessione cloud)
- Nessuna GPU, nessun gioco, nessuna console: si copre il relinker e cio' che `ctest` verifica.
- `pr_overlap.py` non funziona li': usa GraphQL e le API `pulls` del repo upstream, bloccate. Alternativa: `gh api -X GET search/issues -f q='repo:boykopovar/AnyPS5 is:pr is:open <nome> in:title,body'`, con poche ricerche al minuto. Sul PC va usato lo script vero.
- Il push va solo al fork.

## Stato rilevato il 2026-11-19 (da ricontrollare, cambia ogni ora)
- Upstream: circa 34.000 commit dal 2026-10-04, 700-1100 commit al giorno, 1.757 PR aperte, 292 issue aperte.
- Librerie: 16.834 funzioni su 39.508 (42,6%). Shader RDNA2: 1460/1460; gfx1013: 1484/1560.
- Tempi di merge (solo PR mergiate): mediana 6 ore; le PR `relinker` mediana circa 5 giorni.
- Issue #10219 (route ps5-payload-dev stub) scartata: la PR #10792 e' gia' aperta.

## Candidati: stub senza PR nel titolo (verificati su main il 2026-11-19, vanno riverificati)
Librerie con 1-2 stub ancora `NotImplemented_nid_no_patch` e nessuna PR aperta che le nomini nel titolo:
libSceAc3Enc, libSceAgcVsh, libSceBackupRestoreUtil, libSceDtsEnc, libSceEsvmPlayReady, libSceFontGs.native, libSceGameUpdate, libSceImeDialogVoiceDictation, libSceLoginMgrServer, libSceNpFriendListDialog, libSceNpSpeechToText, libSceNpTextToSpeech, libScePfs (scePfsPreadDecompressBlock), libSceProprietaryVoiceChatHelperPrx, libSceRazorCpu, libSceSisrSys, libSceSulphaDrv, libSceSystemLogger2Game, libSceTtsEngine, libSceVideoArbitration, libSceVideoOutSecondary, ulobjmgr.
Gia' implementate nel frattempo: sceCustomMusicAudioOut*, sceLoginServiceRequestDevices, scePfsPread, sceVideodecMapMemory, libSceVcodecUnknown_Ac6aYjr1pY8.
Con PR aperte (da evitare): libSceFios2, libSceFontGs, libSceHidControl, libSceImeDialog, libSceNpEulaDialog, libSceNpPartnerSubscription, libSceOpusCeltDec, libScePlayGo, libScePosixForWebKit, libSceRudp.
Attenzione: la ricerca copre solo i titoli delle PR; una PR puo' nominare la funzione solo nel corpo. Molte stub sono `Initialize` o `Terminate` con firma e comportamento non noti: il comportamento va ricavato da `docs/dev/REVERSE_ENGINEERING.md` (fonti in ordine di affidabilita') e, se non e' noto, la funzione lancia e si registra il debito.

## Obiettivi del progetto (dedotti dai commit delle ultime 48 ore, non da una roadmap scritta)
Copertura ampia e verificata delle librerie di sistema (libc, libkernel, libSceNKWebKit, LibreSsl3, Icu, MsgDialog, Jsc), uso di titoli reali (title_run) per scoprire cosa manca, test per ogni modifica (circa il 29% dei commit), grafica e shader a ritmo piu' basso, prestazioni ancora aperte (molti titoli a 1-4 FPS).

## Metodo comune dei contributori
1. Un export, spesso una stub, per un titolo che serve.
2. Fonte del comportamento, in ordine: misura su console, codice del modulo del firmware, altre implementazioni, chiamate dei titoli.
3. Firma vera e controllo degli argomenti con i codici d'errore del firmware; il resto lancia con `NotImplemented_nid_no_patch`.
4. Un test che fissa i valori letti dal firmware.
5. Un fragment di debito in `docs/dev/debt/changes/` (prima la lacuna, poi il comportamento).
6. PR piccola, un argomento, template compilato. Esempio da imitare: commit `dfd3fc4914` (libSceVideoRecording.native, 5 file, circa 140 righe).

## Decisione dell'utente (2026-11-20)
Si fermano qui le sessioni remote: per leggere il firmware e usare i dump servono il PC, quindi il lavoro di codice riprende li'. Nessuna PR aperta, nessun branch di argomento creato. Su `upstream/main` ci sono centinaia di commit al giorno: sul PC fare prima `git fetch upstream` e ricontrollare lo stato delle librerie candidate.

## Cosa serve sul PC
- Dump del firmware e dei titoli ottenuti dalla propria console. Non vengono distribuiti dal progetto e non vanno messi in repo ne' in una PR.
- `docs/dev/GAME_TESTING.md`: `tools/unwrap_self.py`, relinker con `--registry`, `tools/audit_dump.py`, `tools/import_audit.py`.
- `docs/dev/REVERSE_ENGINEERING.md`: come leggere un modulo e come citare la fonte.
- Build: `docs/dev/BUILD.md`; per il relinker basta `-DANYPS5_RELINKER_ONLY=ON`.

## Prossimi passi
1. Scegliere una funzione dall'elenco, ricontrollare overlap e stato su `upstream/main`.
2. Leggere la libreria piccola e il suo file in `docs/dev/debt/` per il formato.
3. Creare il branch di argomento da `upstream/main`, implementare, aggiungere il fragment di debito.
4. Build relinker-only o completa e `ctest`, poi `check_conventions.py`.
5. Su richiesta dell'utente: push al fork e PR con il template.

## Riprendere la sessione sul PC
- Clone: `git clone https://github.com/ZioZoni95/AnyPS5 && cd AnyPS5 && git remote add upstream https://github.com/boykopovar/AnyPS5 && git checkout ccr-99f1688e-sdsir6`.
- Avviare `claude` nella cartella: carica questo file e AGENTS.md.
- La sessione web originale: https://claude.ai/code/session_013aUgBR1K4CggiyESP2GokF
