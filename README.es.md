# /watch — dale ojos a Claude para ver videos (guía en español, con foco en Windows)

**Claude no puede ver videos por sí solo. Esta skill le fabrica ojos:** le pasas una URL (YouTube, TikTok, Instagram, X, Vimeo y ~1800 sitios más) o un archivo local, y la skill descarga el video, extrae fotogramas como imágenes, consigue la transcripción con marcas de tiempo, y Claude responde tu pregunta habiendo **visto** y **escuchado** el video.

```
/watch https://youtu.be/XXXX ¿qué pasa en el segundo 30?
/watch C:\Videos\clip.mp4 resume este video
```

> Este es un fork de [mathiaschu/watch](https://github.com/mathiaschu/watch) (a su vez fork de [bradautomates/claude-video](https://github.com/bradautomates/claude-video), MIT). La transcripción corre **100% en tu máquina** — sin API key, sin cuenta, el audio nunca sale de tu PC.

## Qué cambia en este fork

1. **Arreglado el backend `openai-whisper` en Windows/Linux/Intel Mac** — en el original, un choque de nombres de módulo (`scripts/whisper.py` vs el paquete `whisper`) rompía la transcripción local en toda máquina sin Apple Silicon. Propuesto también al original en [PR #5](https://github.com/mathiaschu/watch/pull/5).
2. **Subtítulos en español por defecto** — el original solo pedía subtítulos en inglés; aquí se piden `es, es-419, es-ES` primero (y hay flag `--sub-langs` para cualquier idioma — propuesto al original en [PR #6](https://github.com/mathiaschu/watch/pull/6)).
3. **Esta guía en español**, con la instalación de Windows paso a paso.

## Instalación en Windows

**1. Clona la skill donde Claude Code busca skills:**

```powershell
git clone https://github.com/KevoMandujano/watch.git "$env:USERPROFILE\.claude\skills\watch"
```

**2. Instala las tres dependencias** (si no las tienes):

```powershell
winget install --id Gyan.FFmpeg -e        # extrae los fotogramas y el audio
winget install --id yt-dlp.yt-dlp -e      # descarga el video de ~1800 sitios
py -m pip install openai-whisper          # transcribe el audio EN TU PC (baja ~300 MB)
```

**3. Ojo con el comando de Python.** En muchas PC con Windows, `python` es un acceso directo falso de Microsoft Store que no hace nada. Usa **`py`** en su lugar. Verifica con:

```powershell
py --version
```

Si `py` no existe, instala Python desde [python.org](https://www.python.org/downloads/) (no desde la Microsoft Store).

**4. Verifica que todo está en su sitio:**

```powershell
py "$env:USERPROFILE\.claude\skills\watch\scripts\setup.py" --json
```

Debe responder `"status": "ready"`.

## Uso

En Claude Code, escribe:

```
/watch <url-o-ruta> <tu pregunta>
```

Notas de uso que ahorran tiempo y tokens:

- **Videos públicos de YouTube, TikTok y Vimeo** funcionan directo.
- **Instagram Reels y X (Twitter)** exigen estar logueado: si falla la descarga, Claude te pedirá de qué navegador tomar prestadas tus cookies (`--cookies-from-browser chrome`). Se leen de tu propia máquina y no se copian ni se envían a ningún lado.
- **Telegram no está soportado por yt-dlp**: guarda el video a disco desde Telegram Desktop (clic derecho → guardar) y pásale la ruta del archivo — funciona igual.
- **El costo en tokens vive en los fotogramas**: ~50-80k tokens por un video con 80 fotogramas. Para videos largos (>10 min), pide un tramo concreto ("analiza del minuto 2 al 3") en vez de un barrido completo.
- La primera transcripción baja el modelo de voz (~140 MB) una sola vez y queda en caché.

## Créditos y licencia

- Skill original: [bradautomates/claude-video](https://github.com/bradautomates/claude-video) (MIT).
- Fork con Whisper local y soporte de cookies: [mathiaschu/watch](https://github.com/mathiaschu/watch) de Mathias Schusterman (MIT).
- Este fork: arreglo de Windows + español por defecto + esta guía. Licencia [MIT](LICENSE), igual que los anteriores.
