# CINEGEN - AI Movie Generator

## Overview

CINEGEN is an AI-powered short film generator that transforms text prompts into complete cinematic videos. Users describe a movie idea, and the system automatically scripts scenes, generates images, creates voiceover narration, and compiles everything into a final video. The application follows a pipeline architecture where movie creation progresses through distinct stages: scripting → media generation → rendering → completion.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **Framework**: React 18 with TypeScript
- **Routing**: Wouter (lightweight router)
- **State Management**: TanStack React Query for server state with automatic polling for movie status updates
- **Styling**: Tailwind CSS with shadcn/ui component library (New York style)
- **Animations**: Framer Motion for page transitions and UI animations
- **Build Tool**: Vite with path aliases (@/ for client/src, @shared/ for shared code)

The frontend uses a dark cinematic theme with custom CSS variables and Google Fonts (Cinzel for display, Montserrat for body text). Pages include a home studio for creating movies, individual movie detail pages with progress tracking, and a gallery view.

### Backend Architecture
- **Runtime**: Node.js with Express
- **Language**: TypeScript with ESM modules
- **API Pattern**: RESTful endpoints defined in shared/routes.ts with Zod validation
- **Build Process**: esbuild for server bundling, Vite for client

Key server modules:
- `server/routes.ts` - API endpoints and movie processing orchestration
- `server/storage.ts` - Database abstraction layer using repository pattern
- `server/lib/` - AI service integrations (OpenAI, Vertex AI, ElevenLabs, FFmpeg)

### Data Storage
- **Database**: PostgreSQL with Drizzle ORM
- **Schema Location**: shared/schema.ts
- **Tables**: 
  - `movies` - Stores movie metadata, prompt, script JSON, status, and final video URL
  - `scenes` - Individual scene data with narration, image prompts, and generated asset URLs

Movie status workflow: `pending → scripting → generating_media → rendering → completed | failed`

### AI Pipeline Services
1. **Script Generation**: OpenRouter API with DeepSeek model - converts user prompts into structured scene arrays
2. **Image Generation**: Google Vertex AI (Imagen 3) - creates scene visuals from image prompts
3. **Voice Generation**: ElevenLabs API - produces narration audio from scene text
4. **Video Composition**: FFmpeg (fluent-ffmpeg) - combines images and audio into scene videos, then merges all scenes

All services include mock fallbacks when API keys are not configured.

## External Dependencies

### Required API Keys (Environment Variables)
- `DATABASE_URL` - PostgreSQL connection string
- `OPENROUTER_API_KEY` - For DeepSeek script generation via OpenRouter
- `ELEVENLABS_API_KEY` - For text-to-speech narration
- `GOOGLE_CLOUD_PROJECT` - GCP project ID for Vertex AI

### Google Cloud Configuration
- Service account key file at `key.json` in project root
- Vertex AI Image Generation endpoint in us-central1 region

### System Dependencies
- FFmpeg must be installed on the system for video processing
- Temporary files stored in `/tmp/movie-generator/`

### Key NPM Packages
- `@google-cloud/aiplatform` - Vertex AI SDK
- `fluent-ffmpeg` - FFmpeg wrapper for video processing
- `drizzle-orm` / `drizzle-kit` - Database ORM and migrations
- `express` - HTTP server
- `axios` - HTTP client for external APIs