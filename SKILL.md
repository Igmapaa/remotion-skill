---
name: remotion
description: Build, edit, and render programmatic videos with Remotion and React. Use this skill whenever the user wants to create a video with code, animate React components, generate motion graphics, render MP4/GIF/WebM files, work with Remotion compositions, sequences, interpolation, spring animations, audio, or anything related to Remotion projects. Trigger for phrases like "make a video", "animate this", "render a clip", "create motion graphics", "Remotion component", "video with React", even if the user doesn't say "Remotion" explicitly.
---

Remotion lets you create videos programmatically using React. Every frame is a React component. You control everything — timing, animation, layout — with code.

## Core Concepts

### useCurrentFrame()
Returns the current frame number (0-indexed):
```tsx
const frame = useCurrentFrame(); // 0, 1, 2, 3...
```

### interpolate()
Maps a frame range to a value range:
```tsx
import { interpolate } from 'remotion';
const opacity = interpolate(frame, [0, 30], [0, 1], { extrapolateRight: 'clamp' });
const scale = interpolate(frame, [0, 20], [0.8, 1], { extrapolateRight: 'clamp' });
```

### spring()
Physics-based animation:
```tsx
import { spring, useVideoConfig } from 'remotion';
const { fps } = useVideoConfig();
const scale = spring({ frame, fps, config: { damping: 12 } });
```

### Sequence
Offsets children in time:
```tsx
<Sequence from={30} durationInFrames={60}>
  <Subtitle />
</Sequence>
```

### AbsoluteFill
```tsx
<AbsoluteFill style={{ backgroundColor: '#0f0f0f' }}>
  <MyContent />
</AbsoluteFill>
```

### Audio, Video & Images
```tsx
import { Audio, Video, Img, staticFile } from 'remotion';
<Audio src={staticFile('music.mp3')} volume={0.8} />
<Video src={staticFile('clip.mp4')} />
<Img src={staticFile('logo.png')} />
```

## Project Structure

```
my-video/
├── public/
├── src/
│   ├── Root.tsx
│   └── MyVideo.tsx
├── package.json
└── remotion.config.ts
```

## Commands

```bash
npm run dev
npx remotion render src/index.ts MyVideo out/video.mp4
```

## Common Patterns

### Fade in
```tsx
const opacity = interpolate(frame, [0, 20], [0, 1], { extrapolateRight: 'clamp' });
```

### Fade out before end
```tsx
const { durationInFrames } = useVideoConfig();
const opacity = interpolate(frame, [durationInFrames - 20, durationInFrames], [1, 0], { extrapolateLeft: 'clamp' });
```

### Slide in from bottom
```tsx
const y = interpolate(frame, [0, 20], [60, 0], { extrapolateRight: 'clamp' });
// style={{ transform: `translateY(${y}px)` }}
```

### Spring pop
```tsx
const scale = spring({ frame, fps, config: { damping: 10, stiffness: 120 } });
// style={{ transform: `scale(${scale})` }}
```

### Staggered list
```tsx
{items.map((item, i) => (
  <Sequence key={i} from={i * 8}>
    <ListItem>{item}</ListItem>
  </Sequence>
))}
```

### Loop animation
```tsx
const loopedFrame = frame % 60;
```

## Component Template

```tsx
import { AbsoluteFill, interpolate, spring, useCurrentFrame, useVideoConfig } from 'remotion';

export const MyVideo: React.FC<{ title: string }> = ({ title }) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();
  const opacity = interpolate(frame, [0, 20], [0, 1], { extrapolateRight: 'clamp' });
  const scale = spring({ frame, fps, config: { damping: 12 } });
  return (
    <AbsoluteFill style={{ backgroundColor: '#111', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
      <h1 style={{ color: 'white', fontSize: 80, opacity, transform: `scale(${scale})` }}>
        {title}
      </h1>
    </AbsoluteFill>
  );
};
```

## Gotchas & Rules

- Use Img from remotion, never native img tag
- No side effects in render — use delayRender / continueRender for async
- fps math: 30fps = 30 frames/sec — always derive from useVideoConfig()
- Root.tsx must export a RemotionRoot

## New Project Setup

```bash
npx create-video@latest
cd my-video && npm install && npm run dev
# separate terminal:
claude
```

## Remotion Official Skills

```bash
npx skills add remotion-dev/skills
```
