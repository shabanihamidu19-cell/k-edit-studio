# K-EDIT STUDIO – Full Phase Roadmap

## Phase 1 – Architecture & Interface ✅ DELIVERED
- Capacitor project skeleton
- Professional dark CapCut-style workspace
- Multi-track timeline UI (Video / Overlay / Text / Audio)
- Preview player + transport
- Tool panel system
- Project store with undo/redo
- Export job preparation UI
- AI tools panel (contract only)

## Phase 2 – Media Import & Preview
**Goal**: Real files from device appear on timeline and play.

Tasks:
1. Capacitor File Picker / Photo Library plugin
2. Copy selected media into app sandbox (Filesystem)
3. Generate low-res proxy (Media3 or MediaMetadataRetriever) for timeline
4. Resolve blob/file paths correctly for <video>
5. Basic scrubbing works with real media

## Phase 3 – Timeline & Video Editing Engine
**Goal**: Trim, split, move, speed, reverse (preview) are accurate.

Tasks:
1. Correct sourceStart / duration math
2. Live preview of speed changes
3. Split at playhead
4. Drag-and-drop reorder + snap
5. Delete / duplicate
6. Basic transitions (cross-fade preview)

## Phase 4 – Audio Studio
1. Multiple audio tracks
2. Volume + mute per clip
3. Fade in / fade out
4. Extract audio from video (Media3)
5. Voice-over via MediaRecorder → file → timeline

## Phase 5 – Photo Editing Mode
1. Dedicated image mode or image-as-clip
2. Crop / rotate / flip (Canvas + native)
3. Filters & color (already partially in Phase 1)
4. Transparent PNG export
5. Background tools (link to AI)

## Phase 6 – AI-Powered Tools
1. Configurable provider registry (Remove.bg, Replicate, custom)
2. Real upload of current frame / selected image
3. Strict success/failure handling – never apply fake result
4. Background removal → new transparent clip
5. Auto captions (Whisper API or on-device if feasible)
6. Fallback chain when primary API is down

## Phase 7 – Export Engine (Critical)
1. Custom Capacitor plugin wrapping Media3 Transformer
2. Build Composition from project tracks
3. Apply effects, speed, reverse, overlays, audio mixing
4. Progress listener + cancel
5. Save to MediaStore / Downloads
6. Support 720p / 1080p / 4K + FPS options

## Phase 8 – Performance & Hardening
1. Proxy strategy enforced
2. Memory pressure handling
3. Background processing where possible
4. Crash recovery (save project draft to IndexedDB / Filesystem)
5. Low-end device test matrix (2–3 GB RAM)
6. Battery & thermal awareness

---

**Native Media3 Plugin Contract (Phase 7)**

```typescript
interface ExportJob {
  projectId: string;
  name: string;
  width: number;
  height: number;
  fps: number;
  quality: 'high' | 'medium' | 'low';
  tracks: {
    video: Clip[];
    overlay: Clip[];
    text: Clip[];
    audio: Clip[];
  };
}

interface Clip {
  id: string;
  type: string;
  src: string;          // file:// or content:// path
  start: number;        // ms on timeline
  duration: number;
  sourceStart: number;
  sourceDuration: number;
  speed: number;
  reverse: boolean;
  volume: number;
  filters: Record<string, number>;
  transform: object;
  text?: string;
  style?: object;
  fadeIn?: number;
  fadeOut?: number;
}

// Plugin methods
export(job: ExportJob): Promise<{ path: string; size: number }>;
generateProxy(path: string, maxHeight: number): Promise<{ path: string }>;
extractAudio(path: string): Promise<{ path: string }>;
```

Use `androidx.media3:media3-transformer` + `media3-effect`.
