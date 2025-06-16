# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Subler is a macOS application for muxing and tagging MP4 files. It supports:
- Creating tx3g subtitle tracks compatible with Apple devices
- Muxing video/audio/chapters/subtitles from mov/mp4/mkv files  
- Raw format support (H.264, AAC, AC3, SCC, VobSub)
- Metadata editing with TMDb, TVDB, and iTunes Store integration
- Batch processing through a queue system

## Build Commands

- **Build**: Open `Subler.xcodeproj` in Xcode, select 'Subler' scheme, then build/run
- **Dependencies**: Uses git submodules - ensure they're initialized with `git submodule update --init --recursive`

## Architecture Overview

### Core Components
- **Document-based app**: Uses NSDocument architecture with `Document.swift` wrapping `MP42File`
- **Window management**: `DocumentWindowController` handles main editing interface with split view
- **Queue system**: `QueueController` + `Queue.swift` for batch processing with thread-safe operations
- **Metadata system**: Protocol-based `MetadataImporter` with multiple service providers (TMDb, iTunes, etc.)
- **Preset system**: `PresetManager` handles reusable metadata and processing templates

### Key Data Flow
1. Files loaded via Document or added to Queue
2. Queue processes files through configurable action chains (`QueueAction` protocol)
3. MP42Foundation framework handles format conversion and muxing
4. Metadata services provide online data that gets merged
5. Final MP4 files written with all tracks and metadata

### Architecture Patterns
- **Singleton controllers**: QueueController, PresetManager for global state
- **Protocol-oriented**: QueueActionProtocol, MetadataService, Preset for extensibility
- **Observer pattern**: NotificationCenter for queue status and updates
- **Command pattern**: Queue actions as discrete, serializable operations

## Key Frameworks
- **MP42Foundation**: Core MP4 processing framework (included as submodule)
- **Standard macOS**: Cocoa, AVFoundation for media handling
- **FFmpeg**: Used by MP42Foundation for format conversion (statically linked)

## File Organization
- `Classes/`: Main Swift application code
- `Classes/MetadataImporters/`: Online metadata service implementations  
- `Classes/PropertyView/`: Track-specific editing view controllers
- `MP42Foundation/`: Core MP4 processing framework submodule
- Localization files for multiple languages (Spanish, French, Italian, Chinese)

## Threading
- Queue processing uses dedicated dispatch queues
- UI updates must be dispatched to main queue
- Thread-safe access patterns for queue items and shared state