# YOUSTEAM
Platforme de steam
# Prototype: Next.js (HLS player) + Express backend (auth + presigned S3 upload)

This single file contains a ready-to-copy prototype: frontend (Next.js single page with HLS player + mock catalogue) and backend (Express minimal API with auth and S3 presigned upload). Follow the README below to run locally.

---

## README

### Prérequis
- Node.js 18+
- npm ou yarn
- Un bucket S3 + credentials AWS (pour l'upload presigné)

### Structure proposée (fichiers à créer)

```
/proto-streaming/
  frontend/
    pages/
      index.js
    package.json
    next.config.js
  backend/
    index.js
    package.json
  README.md
```

---

## FRONTEND: pages/index.js (Next.js single-file prototype)

```jsx
import { useEffect, useRef, useState } from 'react'
import Hls from 'hls.js'

const mockVideos = [
  {
    id: 'vid-1',
    title: 'Sample VOD — Clip 1',
    poster: 'https://picsum.photos/640/360?random=1',
    // sample HLS public stream for testing; replace with your signed manifest
    hls: 'https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8',
  },
  {
    id: 'vid-2',
    title: 'Sample VOD — Clip 2',
    poster: 'https://picsum.photos/640/360?random=2',
    hls: 'https://test-streams.mux.dev/test_001/stream.m3u8',
  },
]

export default function Home() {
  const videoRef = useRef(null)
  const [selected, setSelected] = useState(mockVideos[0])

  useEffect(() => {
    const video = videoRef.current
    if (!video) return

    if (Hls.isSupported()) {
      const hls = new Hls()
      hls.loadSource(selected.hls)
      hls.attachMedia(video)
      hls.on(Hls.Events.MANIFEST_PARSED, () => {
        // autoplay can be blocked by browser; use play button if needed
        // video.play()
      })
      return () => {
        hls.destroy()
      }
    } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
      video
