# SocialVideoDownload.py

Bot Telegram de téléchargement de vidéos et d'audio depuis les réseaux sociaux (YouTube, TikTok et les autres sites pris en charge par `yt-dlp`). On lui envoie un lien, il renvoie le fichier.

Fork de travail de [OverStyleFR/SocialVideoDownload.py](https://github.com/OverStyleFR/SocialVideoDownload.py), créé par Tom V. (OverStyleFR). Le crédit du projet lui revient.

## Ce que fait le bot

| Usage | Commande |
|---|---|
| Télécharger une vidéo | `/download <lien>` |
| Extraire l'audio en MP3 | `/music <lien>` |
| Télécharger sans commande | envoyer le lien en message |
| Aide et statistiques | `/help`, `/stats` |

- **Envoi intelligent** : sous 35 Mo le fichier part directement dans Telegram, au-delà il est déposé sur un hébergeur externe et le bot renvoie le lien.
- **Déduplication** : une URL déjà téléchargée n'est pas retéléchargée (empreinte SHA-256).
- **Nettoyage automatique** : un fil de fond libère le disque selon la taille et le type de fichier, avec un seuil d'urgence.
- **Images multi-architecture** : `linux/amd64` et `linux/arm64`, publiées sur GitHub Container Registry.

## Démarrage

Docker Compose (recommandé) :

```bash
cp .env.example .env      # renseigner BOT_TOKEN
docker compose up -d --build
docker compose logs -f
```

Sans Docker (Python 3.11) :

```bash
python3.11 -m venv venv
venv/bin/pip install -r requirements.txt
venv/bin/python main.py
```

Le jeton peut aussi être passé par l'environnement : `BOT_TOKEN=... docker compose up -d --build`.

## Configuration

Tout se règle dans `.env`, prioritaire sur les variables du système.

| Variable | Obligatoire | Rôle | Défaut |
|---|:-:|---|---|
| `BOT_TOKEN` | oui | Jeton du bot Telegram | |
| `MIN_FREE_SPACE_MB` | non | Espace libre minimal avant nettoyage d'urgence | `500` |
| `CLEANUP_INTERVAL_HOURS` | non | Intervalle du nettoyage périodique | `24` |
| `SMALL_FILE_SIZE_MB` | non | Seuil d'un « petit » fichier | `12` |
| `RETENTION_SMALL_HOURS` | non | Conservation des petits fichiers et des MP3 | `24` |
| `RETENTION_LARGE_HOURS` | non | Conservation des gros fichiers | `2` |
| `FFMPEG_PATH` | non | Chemin de FFmpeg (repli sur `ffmpeg`) | embarqué |
| `VERSION`, `DEVELOPED_BY` | non | Texte affiché dans `/help` et `/stats` | |

## Organisation du code

```
main.py            point d'entrée
config.py          lecture du .env
commands/          start, help, download, music, stats, auto_download
utils/             cache, disque, rétention, déduplication, upload, logs
```

Détails d'architecture, flux et dépannage : [`DOCS.md`](DOCS.md).

## Images Docker

| Branche | Tags |
|---|---|
| `main` | `latest`, `VERSION` |
| `develop` | `dev` |

Registre : `ghcr.io/overstylefr/socialvideodownload.py`
